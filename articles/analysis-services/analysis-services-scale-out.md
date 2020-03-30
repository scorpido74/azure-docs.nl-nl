---
title: Scale-out azure Analysis Services| Microsoft Documenten
description: Repliceer Azure Analysis Services-servers met scale-out. Clientquery's kunnen vervolgens worden verdeeld over meerdere queryreplica's in een scale-out querygroep.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3ea304d038618fc428f20e7ad72b398f593d09a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78247996"
---
# <a name="azure-analysis-services-scale-out"></a>Uitschalen van Azure Analysis Services

Met scale-out kunnen clientquery's worden verdeeld over meerdere *queryreplica's* in een *querygroep,* waardoor de responstijden tijdens hoge queryworkloads worden verkort. U de verwerking ook scheiden van de querygroep, zodat clientquery's niet nadelig worden beïnvloed door verwerkingsbewerkingen. Scale-out kan worden geconfigureerd in Azure portal of met behulp van de Analysis Services REST API.

Scale-out is beschikbaar voor servers in de standaardprijslaag. Elke queryreplica wordt in rekening gebracht met dezelfde snelheid als uw server. Alle queryreplica's worden gemaakt in dezelfde regio als uw server. Het aantal queryreplica's dat u configureren, wordt beperkt door de regio waarin uw server zich bevindt. Zie [Beschikbaarheid per regio](analysis-services-overview.md#availability-by-region)voor meer informatie. Scale-out verhoogt de hoeveelheid beschikbaar geheugen voor uw server niet. Om het geheugen te verhogen, moet u uw abonnement upgraden. 

## <a name="why-scale-out"></a>Waarom uitschalen?

In een typische serverimplementatie fungeert één server als verwerkingsserver en queryserver. Als het aantal clientquery's ten opzichte van modellen op uw server hoger is dan de Query Processing Units (QPU) voor het abonnement van uw server of als modelverwerking tegelijkertijd plaatsvindt met hoge queryworkloads, kunnen de prestaties afnemen. 

Met scale-out u een querygroep maken met maximaal zeven extra queryreplicabronnen (in totaal acht, inclusief uw *primaire* server). U het aantal replica's in de querygroep schalen om op kritieke momenten aan de QPU-eisen te voldoen en u op elk gewenst moment een verwerkingsserver uit de querygroep scheiden. 

Ongeacht het aantal queryreplica's dat u in een querygroep hebt, worden verwerkingsworkloads niet verdeeld over queryreplica's. De primaire server fungeert als verwerkingsserver. Queryreplica's worden alleen query's weergegeven ten opzichte van de modeldatabases die zijn gesynchroniseerd tussen de primaire server en elke replica in de querygroep. 

Wanneer u uitschalen wordt uitgevoerd, kan het tot vijf minuten duren voordat nieuwe queryreplica's stapsgewijs aan de querygroep worden toegevoegd. Wanneer alle nieuwe queryreplica's actief zijn, worden nieuwe clientverbindingen in evenwicht gebracht tussen resources in de querygroep. Bestaande clientverbindingen worden niet gewijzigd van de bron waarmee ze momenteel zijn verbonden. Bij het inschalen worden alle bestaande clientverbindingen met een querygroepbron die uit de querygroep wordt verwijderd, beëindigd. Clients kunnen opnieuw verbinding maken met een resterende querygroepbron.

## <a name="how-it-works"></a>Hoe werkt het?

Wanneer u de eerste keer scale-out configureert, worden modeldatabases op uw primaire server *automatisch* gesynchroniseerd met nieuwe replica's in een nieuwe querygroep. Automatische synchronisatie vindt slechts één keer plaats. Tijdens automatische synchronisatie worden de gegevensbestanden van de primaire server (versleuteld in rust in blobopslag) gekopieerd naar een tweede locatie, ook versleuteld in rust in blob-opslag. Replica's in de querygroep worden vervolgens *gehydrateerd* met gegevens uit de tweede set bestanden. 

Hoewel een automatische synchronisatie alleen wordt uitgevoerd wanneer u een server voor de eerste keer schaalt, u ook een handmatige synchronisatie uitvoeren. Het synchroniseren van gegevens over replica's in de querygroep komt overeen met die van de primaire server. Wanneer de verwerking (refresh) modellen op de primaire server, een synchronisatie moet worden uitgevoerd *nadat* de verwerking bewerkingen zijn voltooid. Met deze synchronisatie worden bijgewerkte gegevens van de bestanden van de primaire server in blobopslag kopieën gemaakt naar de tweede set bestanden. Replica's in de querygroep worden vervolgens gehydrateerd met bijgewerkte gegevens uit de tweede set bestanden in blob-opslag. 

Bij het uitvoeren van een volgende scale-outbewerking, bijvoorbeeld door het aantal replica's in de querygroep te verhogen van twee naar vijf, worden de nieuwe replica's gehydrateerd met gegevens uit de tweede set bestanden in blobopslag. Er is geen synchronisatie. Als u vervolgens een synchronisatie uitvoert nadat u hebt uitschad, worden de nieuwe replica's in de querygroep twee keer gehydrateerd - een redundante hydratatie. Bij het uitvoeren van een volgende scale-outbewerking is het belangrijk om in gedachten te houden:

* Voer een synchronisatie uit *vóór de scale-outbewerking* om redundante hydratatie van de toegevoegde replica's te voorkomen. Gelijktijdige synchronisatie en scale-outbewerkingen die tegelijkertijd worden uitgevoerd, zijn niet toegestaan.

* Bij het automatiseren van zowel *verwerkings-* als scale-outbewerkingen is het belangrijk om eerst gegevens op de primaire server te verwerken, vervolgens een synchronisatie uit te voeren en vervolgens de scale-outbewerking uit te voeren. Deze reeks zorgt voor minimale impact op QPU- en geheugenbronnen.

* Synchronisatie is toegestaan, zelfs als er geen replica's in de querygroep staan. Als u uitschalen van nul naar een of meer replica's met nieuwe gegevens van een verwerkingsbewerking op de primaire server, voert u de synchronisatie eerst uit zonder replica's in de querygroep en vervolgens uitschalen. Synchroniseren voordat u uitschaalt, voorkomt redundante hydratatie van de nieuw toegevoegde replica's.

* Bij het verwijderen van een modeldatabase van de primaire server wordt deze niet automatisch verwijderd uit replica's in de querygroep. U moet een synchronisatiebewerking uitvoeren met de opdracht [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell, waarmee het bestand/de bestand/s voor die database wordt verwijderd uit de gedeelde blob-opslaglocatie van de replica en vervolgens de modeldatabase op de replica's in de querygroep wordt verwijderd. Als u wilt bepalen of er een modeldatabase bestaat op replica's in de querygroep, maar niet op de primaire server, moet u ervoor zorgen dat de verwerkingsserver scheiden van de instelling **voor querygroepen** is naar **Ja**. Gebruik vervolgens SSMS om verbinding te `:rw` maken met de primaire server met behulp van de kwalificatie om te zien of de database bestaat. Maak vervolgens verbinding met replica's in `:rw` de querygroep door verbinding te maken zonder de kwalificatie om te zien of dezelfde database ook bestaat. Als de database bestaat op replica's in de querygroep, maar niet op de primaire server, voert u een synchronisatiebewerking uit.   

* Wanneer u een database op de primaire server hernoemt, is er een extra stap nodig om ervoor te zorgen dat de database correct is gesynchroniseerd met alle replica's. Nadat u de naam hebt gewijzigd, voert u een synchronisatie uit `-Database` met de opdracht [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) waarin de parameter met de oude databasenaam wordt opgegeven. Met deze synchronisatie worden de database en bestanden met de oude naam verwijderd uit replica's. Voer vervolgens een andere synchronisatie `-Database` uit waarin de parameter met de nieuwe databasenaam wordt opgegeven. De tweede synchronisatie kopieert de nieuw benoemde database naar de tweede set bestanden en hydrateert eventuele replica's. Deze synchronisaties kunnen niet worden uitgevoerd met de opdracht Model synchroniseren in de portal.

### <a name="synchronization-mode"></a>Synchronisatiemodus

Queryreplica's worden standaard volledig gerehydrateerd, niet stapsgewijs. Rehydratie gebeurt in fasen. Ze zijn losgemaakt en bevestigd twee tegelijk (ervan uitgaande dat er ten minste drie replica's) om ervoor te zorgen ten minste een replica wordt online gehouden voor vragen op een bepaald moment. In sommige gevallen moeten clients mogelijk opnieuw verbinding maken met een van de online replica's terwijl dit proces plaatsvindt. Door de instelling (in Voorbeeld) **ReplicaSyncMode** te gebruiken, u nu tegelijkertijd synchronisatie van queryreplica's opgeven. Parallelle synchronisatie biedt de volgende voordelen: 

- Aanzienlijke vermindering van de synchronisatietijd. 
- Gegevens over replica's zijn eerder consistent tijdens het synchronisatieproces. 
- Omdat databases online worden gehouden op alle replica's tijdens het synchronisatieproces, hoeven clients geen verbinding opnieuw te maken. 
- De in-memory cache wordt stapsgewijs bijgewerkt met alleen de gewijzigde gegevens, die sneller kunnen zijn dan het volledig hydrateren van het model. 

#### <a name="setting-replicasyncmode"></a>ReplicaSyncMode instellen

Gebruik SSMS om ReplicaSyncMode in te stellen in geavanceerde eigenschappen. De mogelijke waarden zijn: 

- `1`(standaard): Volledige replica database rehydratie in fasen (incrementeel). 
- `2`: Geoptimaliseerde synchronisatie parallel. 

![RelicaSyncMode-instelling](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

Bij het instellen van **ReplicaSyncMode=2**, afhankelijk van hoeveel van de cache moet worden bijgewerkt, kan extra geheugen worden verbruikt door de queryreplica's. Om de database online te houden en beschikbaar voor query's, afhankelijk van hoeveel van de gegevens is gewijzigd, kan de bewerking het geheugen op *de* replica verdubbelen omdat zowel de oude als de nieuwe segmenten tegelijkertijd in het geheugen worden bewaard. Replicaknooppunten hebben dezelfde geheugentoewijzing als het primaire knooppunt en er is normaal gesproken extra geheugen op het primaire knooppunt voor vernieuwingsbewerkingen, dus het is onwaarschijnlijk dat de replica's zonder geheugen komen te zitten. Bovendien is het algemene scenario dat de database stapsgewijs wordt bijgewerkt op het primaire knooppunt, en daarom moet de vereiste voor het dubbele van het geheugen ongewoon zijn. Als de synchronisatiebewerking een geheugenfout niet bevat, wordt de standaardtechniek opnieuw geprobeerd (twee tegelijk koppelen/loskoppelen). 

### <a name="separate-processing-from-query-pool"></a>Afzonderlijke verwerking van querygroep

Voor maximale prestaties voor zowel verwerkings- als querybewerkingen u ervoor kiezen om uw verwerkingsserver te scheiden van de querygroep. Wanneer ze gescheiden zijn, worden nieuwe clientverbindingen toegewezen aan alleen queryreplica's in de querygroep. Als verwerkingsbewerkingen slechts een korte tijd in beslag nemen, u ervoor kiezen om uw verwerkingsserver alleen te scheiden van de querygroep voor de hoeveelheid tijd die nodig is om verwerkings- en synchronisatiebewerkingen uit te voeren en deze vervolgens weer in de querygroep op te nemen. Wanneer u de verwerkingsserver scheidt van de querygroep of deze weer toevoegt aan de querygroep, kan het tot vijf minuten duren voordat de bewerking is voltooid.

## <a name="monitor-qpu-usage"></a>QPU-gebruik controleren

Als u wilt bepalen of een uitschaals voor uw server nodig is, [controleert u uw server](analysis-services-monitor.md) in Azure-portal met behulp van Metrische gegevens. Als uw QPU regelmatig maxes uit, betekent dit dat het aantal query's tegen uw modellen is meer dan de QPU limiet voor uw plan. De statistiek van de wachtrijlengte van de querygroepgroep neemt ook toe wanneer het aantal query's in de wachtrij voor de querythreadgroep de beschikbare QPU overschrijdt. 

Een andere goede statistiek om naar te kijken is de gemiddelde QPU van ServerResourceType. Deze statistiek vergelijkt de gemiddelde QPU voor de primaire server met de querygroep. 

![Statistieken voor het uitschalen van query's](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**QPU by ServerResourceType configureren**

1. Klik in een grafiek met statistieken op **Statistiek toevoegen**. 
2. Selecteer **in RESOURCE**uw server en selecteer vervolgens in METRISCHE **NAAMRUIMTE** **standaardmetrische gegevens**, selecteer vervolgens in **METRIC**, selecteer **QPU**en selecteer vervolgens in **AGGREGATIE** **Avg**. 
3. Klik **op Splitsen toepassen**. 
4. Selecteer **ServerResourceType**in **WAARDEN**.  

### <a name="detailed-diagnostic-logging"></a>Gedetailleerde diagnostische logboekregistratie

Gebruik Azure Monitor Logs voor meer gedetailleerde diagnostiek van uitgeschroefde serverbronnen. Met logboeken u Logboekanalysequery's gebruiken om QPU en geheugen per server en replica uit te breken. Zie voorbeeldquery's voor voorbeelden in [Analysis Services diagnostics logging](analysis-services-logging.md#example-queries).


## <a name="configure-scale-out"></a>Uitschalen configureren

### <a name="in-azure-portal"></a>In Azure-portal

1. Klik in de portal op **Uitschalen**. Gebruik de schuifregelaar om het aantal queryreplicaservers te selecteren. Het aantal replica's dat u kiest is een aanvulling op uw bestaande server.  

2. Selecteer in **De verwerkingsserver scheiden van de querygroep**ja om uw verwerkingsserver uit te sluiten van queryservers. [Clientverbindingen](#connections) met behulp van `:rw`de standaardverbindingstekenreeks (zonder) worden doorgestuurd naar replica's in de querygroep. 

   ![Schuifregelaar schalen](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Klik **op Opslaan** om uw nieuwe queryreplicaservers in te richten. 

Wanneer modellen op uw primaire server de eerste keer scale-out configureren voor een server, worden ze automatisch gesynchroniseerd met replica's in de querygroep. Automatische synchronisatie vindt slechts één keer plaats wanneer u scale-out voor het eerst configureert voor een of meer replica's. Latere wijzigingen in het aantal replica's op dezelfde server *leiden niet tot een andere automatische synchronisatie.* Automatische synchronisatie zal niet opnieuw plaatsvinden, zelfs als u de server op nul replica's en vervolgens weer scale-out naar een aantal replica's. 

## <a name="synchronize"></a>Synchroniseren 

Synchronisatiebewerkingen moeten handmatig of met behulp van de REST API worden uitgevoerd.

### <a name="in-azure-portal"></a>In Azure-portal

In **Overzicht** > model > **synchronisatiemodel**.

![Schuifregelaar schalen](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

Gebruik de **synchronisatiebewerking.**

#### <a name="synchronize-a-model"></a>Een model synchroniseren   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Synchronisatiestatus krijgen  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Statuscodes retourneren:


|Code  |Beschrijving  |
|---------|---------|
|-1     |  Ongeldig       |
|0     | Repliceren        |
|1     |  Rehydrating       |
|2     |   Voltooid       |
|3     |   Mislukt      |
|4     |    Finaliseren     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installeer of werk voordat u PowerShell gebruikt [de nieuwste Azure PowerShell-module.](/powershell/azure/install-az-ps) 

Als u synchronisatie wilt uitvoeren, gebruikt u [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Als u het aantal queryreplica's wilt instellen, gebruikt u [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Geef de `-ReadonlyReplicaCount` optionele parameter op.

Als u de verwerkingsserver wilt scheiden van de querygroep, gebruikt u [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Geef de `-DefaultConnectionMode` optionele `Readonly`parameter op die u wilt gebruiken .

Zie Een [serviceprincipal gebruiken met de az.AnalysisServices-module](analysis-services-service-principal.md#azmodule)voor meer informatie.

## <a name="connections"></a>Verbindingen

Op de overzichtspagina van uw server staan twee servernamen. Als u scale-out nog niet hebt geconfigureerd voor een server, werken beide servernamen hetzelfde. Nadat u scale-out voor een server hebt geconfigureerd, moet u de juiste servernaam opgeven, afhankelijk van het verbindingstype. 

Voor clientverbindingen voor eindgebruikers, zoals Power BI Desktop, Excel en aangepaste apps, gebruikt u **servernaam**. 

Voor SSMS, Visual Studio en verbindingstekenreeksen in PowerShell, Azure Function-apps en AMO gebruikt u **de naam beheerserver**. De naam van de `:rw` beheerserver bevat een speciale (lees-schrijf)kwalificatie. Alle verwerkingsbewerkingen vinden plaats op de (primaire) beheerserver.

![Servernamen](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Scale-up, Scale-down vs. Scale-out

U de prijscategorie op een server met meerdere replica's wijzigen. Dezelfde prijscategorie is van toepassing op alle replica's. Een schaalbewerking brengt eerst alle replica's in één keer naar beneden en brengt vervolgens alle replica's op de nieuwe prijscategorie naar boven.

## <a name="troubleshoot"></a>Problemen oplossen

**Probleem:** Gebruikers krijgen fout **Kan\<de server ' Naam van de server>' instantie niet vinden in verbindingsmodus 'ReadOnly'.**

**Oplossing:** Wanneer u de **verwerkingsserver scheiden selecteert van de querygroepoptie,** worden `:rw`clientverbindingen met de standaardverbindingstekenreeks (zonder ) doorgestuurd naar querygroepreplica's. Als replica's in de querygroep nog niet online zijn omdat synchronisatie nog niet is voltooid, kunnen omgeleide clientverbindingen mislukken. Om mislukte verbindingen te voorkomen, moeten er ten minste twee servers in de querygroep zijn bij het uitvoeren van een synchronisatie. Elke server wordt individueel gesynchroniseerd, terwijl andere online blijven. Als u ervoor kiest om de verwerkingsserver tijdens de verwerking niet in de querygroep te hebben, u ervoor kiezen deze uit de groep te verwijderen en deze vervolgens weer in de groep toe te voegen nadat de verwerking is voltooid, maar voordat u wordt gesynchroniseerd. Gebruik geheugen- en QPU-statistieken om de synchronisatiestatus te controleren.



## <a name="related-information"></a>Gerelateerde informatie

[Serverstatistieken controleren](analysis-services-monitor.md)   
[Azure-analyseservices beheren](analysis-services-manage.md) 
