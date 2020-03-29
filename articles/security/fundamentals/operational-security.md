---
title: Operationele beveiliging van Azure | Microsoft Documenten
description: Meer informatie over Microsoft Azure Monitor-logboeken, de services en hoe deze werken.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 34c0c52945abc6e0ab74b1cb180581c76464bee8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749954"
---
# <a name="azure-operational-security"></a>Operationele beveiliging van Azure
## <a name="introduction"></a>Inleiding

### <a name="overview"></a>Overzicht
We weten dat beveiliging taak één is in de cloud en hoe belangrijk het is dat u nauwkeurige en tijdige informatie over Azure-beveiliging vindt. Een van de beste redenen om Azure te gebruiken voor uw toepassingen en services is om te profiteren van het brede scala aan beveiligingsprogramma's en -mogelijkheden die beschikbaar zijn. Deze tools en mogelijkheden maken het mogelijk om veilige oplossingen te maken op het beveiligde Azure-platform. Windows Azure moet vertrouwelijkheid, integriteit en beschikbaarheid van klantgegevens bieden, terwijl ook transparante verantwoording mogelijk wordt gemaakt.

Om klanten beter inzicht te geven in het scala aan beveiligingscontroles die binnen Microsoft Azure zijn geïmplementeerd vanuit zowel de operationele perspectieven van de klant als van Microsoft, is deze whitepaper, "Azure Operational Security", geschreven met een uitgebreide bekijk de operationele beveiliging die beschikbaar is met Windows Azure.

### <a name="azure-platform"></a>Azure-platform
Azure is een public cloud service platform dat een brede selectie van besturingssystemen, programmeertalen, frameworks, tools, databases en apparaten ondersteunt. Het kan Linux-containers uitvoeren met Docker-integratie; apps bouwen met JavaScript, Python, .NET, PHP, Java en Node.js; back-ends bouwen voor iOS-, Android- en Windows-apparaten. Azure Cloud-service ondersteunt dezelfde technologieën waar miljoenen ontwikkelaars en IT-professionals al op vertrouwen en vertrouwen.

Wanneer u IT-assets bouwt of migreert naar een openbare cloudserviceprovider die u vertrouwt op de mogelijkheden van die organisatie om uw toepassingen en gegevens te beschermen met de services en de besturingselementen die ze bieden om de beveiliging van uw cloudgebaseerde assets te beheren.

De infrastructuur van Azure is ontworpen van de faciliteit tot toepassingen voor het hosten van miljoenen klanten tegelijk en biedt een betrouwbare basis waarop bedrijven kunnen voldoen aan hun beveiligingsvereisten. Daarnaast biedt Azure u een breed scala aan configureerbare beveiligingsopties en de mogelijkheid om deze te beheren, zodat u de beveiliging aanpassen aan de unieke vereisten van de implementaties van uw organisatie. Met dit document u begrijpen hoe azure-beveiligingsmogelijkheden u kunnen helpen aan deze vereisten te voldoen.

### <a name="abstract"></a>Abstract
Azure Operational Security verwijst naar de services, besturingselementen en functies die beschikbaar zijn voor gebruikers voor het beschermen van hun gegevens, toepassingen en andere elementen in Microsoft Azure. Azure Operational Security is gebouwd op een framework dat de opgedane kennis bevat die is opgedaan met verschillende mogelijkheden die uniek zijn voor Microsoft, waaronder de Microsoft Security Development Lifecycle (SDL), het Microsoft Security Response Center-programma, en diep bewustzijn van het landschap van cyberbeveiligingsdreiging.

Deze whitepaper beschrijft microsoft's benadering van Azure Operational Security binnen het Microsoft Azure-cloudplatform en omvat de volgende services:
1.  [Azure Monitor](../../azure-monitor/index.yml)

2.  [Azure Security Center](../../security-center/security-center-intro.md)

3.  [Azure Monitor](../../azure-monitor/overview.md)

4.  [Azure Network-watcher](../../network-watcher/network-watcher-monitoring-overview.md)

5.  [Azure Storage-analyses](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active-map](../../active-directory/fundamentals/active-directory-whatis.md)


## <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure Monitor-logboeken

Microsoft Azure Monitor logs is de IT-beheeroplossing voor de hybride cloud. Azure Monitor-logboeken worden alleen gebruikt of om uw bestaande System Center-implementatie uit te breiden, bieden u de maximale flexibiliteit en controle voor cloudgebaseerd beheer van uw infrastructuur.

![Azure Monitor-logboeken](./media/operational-security/azure-operational-security-fig1.png)

Met Azure Monitor-logboeken u elk exemplaar in elke cloud beheren, inclusief on-premises, Azure, AWS, Windows Server, Linux, VMware en OpenStack, tegen lagere kosten dan concurrerende oplossingen. Azure Monitor-logboeken zijn gebouwd voor de cloud-first-wereld en bieden een nieuwe aanpak voor het beheren van uw onderneming, die de snelste, meest kosteneffectieve manier is om nieuwe zakelijke uitdagingen het hoofd te bieden en nieuwe workloads, toepassingen en cloudomgevingen aan te kunnen.

### <a name="azure-monitor-services"></a>Azure Monitor-services

De kernfunctionaliteit van Azure Monitor-logboeken wordt geleverd door een reeks services die in Azure worden uitgevoerd. Elke service biedt een specifieke beheerfunctie. U kunt services combineren om verschillende beheerscenario's te bewerkstelligen.

| Service  | Beschrijving|
| :------------- | :-------------|
| Azure Monitor-logboeken | Bewaak en analyseer de beschikbaarheid en prestaties van verschillende resources, met inbegrip van fysieke en virtuele machines. |
|Automation | Automatiseer handmatige processen en dwing configuraties af voor fysieke en virtuele machines. |
| Back-up | Back-ups maken en herstel kritieke gegevens. |
| Site Recovery | Bied hoge beschikbaarheid voor kritieke toepassingen. |

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

[Azure Monitor-logboeken](https://azure.microsoft.com/documentation/services/log-analytics) bieden bewakingsservices door gegevens uit beheerde resources te verzamelen in een centrale opslagplaats. Deze gegevens kunnen gebeurtenissen, prestatiegegevens en aangepaste gegevens omvatten die via de API worden geleverd. Na verzameling zijn de gegevens beschikbaar voor waarschuwingen, analyse en export.


Met deze methode u gegevens uit verschillende bronnen consolideren, zodat u gegevens uit uw Azure-services combineren met uw bestaande on-premises omgeving. De methode maakt ook een duidelijk onderscheid tussen het verzamelen van gegevens en het bewerken hiervan. Zo zijn alle bewerkingen beschikbaar voor alle soorten gegevens.


![Azure Monitor-logboeken](./media/operational-security/azure-operational-security-fig2.png)

De Azure Monitor-service beheert uw cloudgegevens veilig met behulp van de volgende methoden:
-   datasegregatie
-   gegevensbewaring
-   fysieke beveiliging
-   incidentmanagement
-   Naleving
-   certificeringen voor beveiligingsstandaarden

### <a name="azure-backup"></a>Azure Backup

[Azure Backup](https://azure.microsoft.com/documentation/services/backup) biedt back-up- en herstelservices voor gegevens en maakt deel uit van de Azure Monitor-suite met producten en services.
Het beschermt uw toepassingsgegevens en bewaart deze jarenlang, zonder dat u grote investeringen hoeft te doen en met een minimum aan operationele kosten. Het kan een back-up maken van gegevens van fysieke en virtuele Windows-servers, naast toepassingsworkloads zoals SQL Server en SharePoint. Het kan ook worden gebruikt door [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) om beveiligde gegevens te repliceren naar Azure voor redundantie en langdurige opslag.


Beveiligde gegevens in Azure Backup worden opgeslagen in een back-upkluis in een bepaalde geografische regio. De gegevens worden gerepliceerd binnen dezelfde regio en kunnen, afhankelijk van het type kluis, ook worden gerepliceerd naar een andere regio voor verdere tolerantie.

### <a name="management-solutions"></a>Beheeroplossingen
[Azure Monitor](../../security-center/security-center-intro.md) is de cloudgebaseerde IT-beheeroplossing van Microsoft waarmee u uw on-premises en cloudinfrastructuur beheren en beschermen.


[Beheeroplossingen](../../monitoring/monitoring-solutions.md) zijn voorverpakte sets logica die een bepaald beheerscenario implementeren met behulp van een of meer Azure Monitor-services. Er zijn verschillende oplossingen beschikbaar bij Microsoft en bij partners die u eenvoudig aan uw Azure-abonnement toevoegen om de waarde van uw investering in Azure Monitor te verhogen. Als partner u uw eigen oplossingen maken om uw toepassingen en services te ondersteunen en deze aan gebruikers te leveren via de Azure Marketplace- of quickstart-sjablonen.


![Beheeroplossingen](./media/operational-security/azure-operational-security-fig4.png)

Een goed voorbeeld van een oplossing die meerdere services gebruikt om extra functionaliteit te bieden, is de [updatebeheeroplossing.](../../automation/automation-update-management.md) Deze oplossing maakt gebruik van de [Azure Monitor-logboekenagent](../../log-analytics/log-analytics-queries.md) voor Windows en Linux om informatie over vereiste updates op elke agent te verzamelen. Het schrijft deze gegevens naar de Azure Monitor logs repository waar u deze analyseren met een opgenomen dashboard.

Wanneer u een implementatie maakt, worden runbooks in [Azure Automation](../../automation/automation-intro.md) gebruikt om vereiste updates te installeren. U beheert dit hele proces in de portal en hoeft zich geen zorgen te maken over de onderliggende details.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center helpt uw Azure-bronnen te beschermen. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen. Binnen de service u niet alleen de politie definiëren op uw Azure-abonnementen, maar ook tegen [Resourcegroepen,](../../azure-resource-manager/management/overview.md#resource-groups)zodat u gedetailleerder zijn.

### <a name="security-policies-and-recommendations"></a>Beveiligingsbeleid en aanbevelingen

Een beveiligingsbeleid bepaalt welke set besturingselementen wordt aanbevolen voor resources binnen het opgegeven abonnement of de opgegeven resourcegroep.

In Security Center definieert u de beleidsregels op grond van de beveiligingsvereisten van uw bedrijf en het type toepassingen of de gevoeligheid van de gegevens.

![Beveiligingsbeleid en aanbevelingen](./media/operational-security/azure-operational-security-fig5.png)


Beleidsregels die zijn ingeschakeld in het abonnementsniveau worden automatisch doorgegeven aan alle resourcesgroepen binnen het abonnement, zoals weergegeven in het diagram aan de rechterkant:


### <a name="data-collection"></a>Gegevensverzameling

Met Security Center worden gegevens van uw virtuele machines (VM's) verzameld om de beveiligingsstatus van de VM's te beoordelen, aanbevelingen voor beveiliging te geven en u te waarschuwen bij bedreigingen. Wanneer uw eerste toegang tot security center, is het verzamelen van gegevens ingeschakeld op alle VM's in uw abonnement. Gegevensverzameling wordt aanbevolen, maar u kunt gegevensverzameling indien gewenst ook uitschakelen in het Security Center-beleid.

### <a name="data-sources"></a>Gegevensbronnen

- Azure Security Center analyseert gegevens uit de volgende bronnen om inzicht in uw beveiligingsstatus te geven, beveiligingsproblemen te identificeren, oplossingen aan te raden en actieve bedreigingen te detecteren:

-   Azure Services: gebruikt informatie over de configuratie van de Azure-services die u hebt geïmplementeerd door te communiceren met de resourceprovider van die service.

- Netwerkverkeer: gebruikt steekproefgewijs netwerkverkeermetagegevens uit de infrastructuur van Microsoft, zoals bron-/doel-IP/poort, pakketgrootte en netwerkprotocol.

-   Oplossingen van partners: gebruikt beveiligingswaarschuwingen van geïntegreerde partneroplossingen, zoals firewalls en antimalwareoplossingen.

-   Uw virtuele machines: gebruikt configuratie-informatie en informatie over beveiligingsgebeurtenissen, zoals Windows-gebeurtenis- en auditlogboeken, IIS-logboeken, syslog-berichten en crashdumpbestanden van uw virtuele machines.

### <a name="data-protection"></a>Gegevensbeveiliging

Om klanten te helpen bedreigingen te voorkomen, te detecteren en erop te reageren, verzamelt en verwerkt Azure Security Center gegevens over beveiliging, zoals configuratie-informatie, metagegevens, gebeurtenislogboeken, crashdumpbestanden en nog veel meer. Microsoft voldoet aan strikte nalevings- en beveiligingsrichtlijnen - van het schrijven van code tot de uitvoering van een service.

-   **Scheiding van gegevens**: gegevens worden op een logische manier apart van elkaar gehouden, in elk onderdeel van de service. Alle gegevens worden gemarkeerd per organisatie. Deze markering blijft aanwezig gedurende de levenscyclus van de gegevens en deze wordt afgedwongen op elke laag van de service.

-   **Toegang tot**gegevens: Om beveiligingsaanbevelingen te geven en potentiële beveiligingsbedreigingen te onderzoeken, heeft Microsoft-personeel toegang tot informatie die is verzameld of geanalyseerd door Azure-services, waaronder crashdumpbestanden, gebeurtenissen voor het maken van processen, VM-schijfmomentopnamen en artefacten, die onbedoeld klantgegevens of persoonlijke gegevens van uw virtuele machines kunnen bevatten. We houden ons aan de [algemene voorwaarden en privacyverklaring van Microsoft Online Services,](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)waarin staat dat Microsoft geen klantgegevens gebruikt of er informatie uit ontlenen voor reclame of soortgelijke commerciële doeleinden.

-   **Gegevensgebruik**: Microsoft gebruikt informatie over patronen en bedreigingen die worden gezien tussen meerdere tenants voor het verbeteren van onze mogelijkheden voor voorkoming en detectie; wij doen dit in overeenstemming met de privacyverplichtingen beschreven in onze [Privacyverklaring](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Gegevenslocatie

Azure Security Center verzamelt tijdelijke kopieën van uw crashdumpbestanden en analyseert deze op bewijs van pogingen tot misbruik en geslaagde aanvallen. Azure Security Center voert deze analyse uit binnen hetzelfde geografische gebied als de werkruimte en verwijdert de tijdelijke kopieën wanneer de analyse is voltooid. Machine-artefacten worden centraal opgeslagen in dezelfde regio als de virtuele machine.

-   **Uw opslagaccounts:** er wordt een opslagaccount opgegeven voor elk gebied waar virtuele machines worden uitgevoerd. Hierdoor kunt u gegevens in dezelfde regio opslaan als de virtuele machine waarvan de gegevens worden verzameld.

-   **Azure Security Center-opslag**: informatie over beveiligingswaarschuwingen, waaronder waarschuwingen van partners, aanbevelingen en de status van de beveiliging, worden centraal opgeslagen, momenteel in de Verenigde Staten. Deze informatie omvat mogelijk gerelateerde configuratie-informatie en beveiligingsgebeurtenissen die indien nodig zijn verzameld van uw virtuele machines om u de beveiligingswaarschuwing, aanbeveling of de beveiligingsstatus door te geven.


## <a name="azure-monitor"></a>Azure Monitor

Met de [Azure Monitor-logboeken beveiliging](../../security-center/security-center-monitoring.md) en audit-oplossing kan IT actief alle resources controleren, waardoor de impact van beveiligingsincidenten tot een minimum kan worden beperkt. Azure Monitor-logboeken Beveiliging en Controle hebben beveiligingsdomeinen die kunnen worden gebruikt voor het bewaken van resources. Het beveiligingsdomein biedt snelle toegang tot opties, voor beveiligingsbewaking worden de volgende domeinen in meer details behandeld:

-   Malware-evaluatie
-   Update-evaluatie
-   Identiteit en toegang.

Azure Monitor biedt verwijzingen naar informatie over specifieke typen resources. Het biedt visualisatie, query, routering, waarschuwingen, automatische schaal en automatisering op gegevens, zowel vanuit de Azure-infrastructuur (Activity Log) als elke afzonderlijke Azure-bron (Diagnostische logboeken).

![Azure Monitor](./media/operational-security/azure-operational-security-fig6.png)


Cloudtoepassingen zijn complex met veel bewegende delen. Monitoring biedt gegevens om ervoor te zorgen dat uw toepassing in een gezonde toestand actief blijft. Het helpt je ook om potentiële problemen af te wenden of problemen op te lossen in het verleden.

Daarnaast u monitoringgegevens gebruiken om diepgaande inzichten te krijgen over uw toepassing. Die kennis kan u helpen om de prestaties of onderhoudbaarheid van toepassingen te verbeteren, of acties te automatiseren waarvoor anders handmatige interventie nodig zou zijn.

### <a name="azure-activity-log"></a>Azure-activiteitenlogboek


Het is een logboek dat inzicht geeft in de bewerkingen die zijn uitgevoerd op resources in uw abonnement. Het activiteitenlogboek stond voorheen bekend als 'Controlelogboeken' of 'Operationele logboeken', omdat het gebeurtenissen in het beheervlak voor uw abonnementen rapporteert.

![Azure-activiteitenlogboek](./media/operational-security/azure-operational-security-fig7.png)

Met behulp van het activiteitenlogboek u bepalen wat, wie en wanneer voor schrijfbewerkingen (PUT, POST, DELETE) die zijn genomen op basis van de bronnen in uw abonnement. U ook de status van de bewerking en andere relevante eigenschappen begrijpen. Het activiteitenlogboek bevat geen leesbewerkingen of bewerkingen voor resources die het Klassieke model gebruiken.

### <a name="azure-diagnostic-logs"></a>Azure Diagnostische logboeken

Deze logboeken worden uitgezonden door een resource en bieden uitgebreide, frequente gegevens over de werking van die bron. De inhoud van deze logboeken verschilt per resourcetype.

Windows-gebeurtenissysteemlogboeken zijn bijvoorbeeld een categorie diagnostische logboeken voor VM's en blob-, tabel- en wachtrijlogboeken zijn categorieën diagnostische logboeken voor opslagaccounts.

Diagnostische logboeken verschillen van het [activiteitenlogboek (voorheen bekend als Audit Log of Operationeel Logboek)](../../azure-monitor/platform/platform-logs-overview.md). Het activiteitenlogboek geeft inzicht in de bewerkingen die zijn uitgevoerd op resources in uw abonnement. Diagnoselogboeken bieden inzicht in bewerkingen die door de resources zelf zijn uitgevoerd.

### <a name="metrics"></a>Metrische gegevens

Azure Monitor stelt u in staat telemetrie te gebruiken om inzicht te krijgen in de prestaties en status van uw workloads op Azure. Het belangrijkste type Azure-telemetriegegevens zijn de statistieken (ook wel prestatiemeteritems genoemd) die door de meeste Azure-bronnen worden uitgestraald. Azure Monitor biedt verschillende manieren om deze [statistieken](../../monitoring/monitoring-data-collection.md) te configureren en te gebruiken voor monitoring en probleemoplossing. Statistieken zijn een waardevolle bron van telemetrie en stellen u in staat om de volgende taken uit te voeren:

-   **Houd de prestaties** van uw resource bij (zoals een VM, website of logische app) door de statistieken ervan in een portaldiagram in te stellen en die grafiek vast te zetten aan een dashboard.

-   **Ontvang een melding van een probleem** dat van invloed is op de prestaties van uw resource wanneer een statistiek een bepaalde drempel overschrijdt.

-   **Configureer geautomatiseerde acties**, zoals het automatisch schalen van een resource of het afvuren van een runbook wanneer een statistiek een bepaalde drempel overschrijdt.

-   **Voer geavanceerde analyses uit** of rapporteer over prestatie- of gebruikstrends van uw resource.

-   **Archiveer** de prestatie- of gezondheidsgeschiedenis van uw resource voor nalevings- of controledoeleinden.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Het is de mogelijkheid binnen Azure waarmee diagnostische gegevens op een geïmplementeerde toepassing kunnen worden opgehaald. U de diagnostische extensie uit verschillende bronnen gebruiken. Momenteel worden [Azure Cloud Service Web- en Worker-rollen](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure Virtual Machines](../../virtual-machines/windows/overview.md) met Microsoft Windows en Service [Fabric](../../azure-monitor/platform/diagnostics-extension-overview.md)ondersteund. Andere Azure-services hebben hun eigen afzonderlijke diagnose.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Het controleren van uw netwerkbeveiliging is essentieel voor het opsporen van netwerkkwetsbaarheden en het waarborgen van naleving van uw IT-beveiligings- en regelgevingsmodel. Met de weergave Beveiligingsgroep u de geconfigureerde netwerkbeveiligingsgroep en beveiligingsregels en de effectieve beveiligingsregels ophalen. Met de lijst met toegepaste regels u de geopende poorten bepalen en het netwerkmisbruik beoordelen.

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) is een regionale service waarmee u de omstandigheden op netwerkniveau in, naar en vanuit Azure controleren en diagnosticeren. Netwerkdiagnose- en visualisatietools die beschikbaar zijn met Network Watcher helpen u inzicht te krijgen in uw netwerk in Azure, diagnoses en inzichten te krijgen. Deze service omvat packet capture, next hop, IP flow verify, security group view, NSG flow logs. Monitoring op scenarioniveau biedt een end-to-endweergave van netwerkbronnen in tegenstelling tot afzonderlijke netwerkbronbewaking.

![Azure Network Watcher](./media/operational-security/azure-operational-security-fig8.png)

Network Watcher heeft momenteel de volgende mogelijkheden:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Controlelogboeken</a>**- Bewerkingen die worden uitgevoerd als onderdeel van de configuratie van netwerken, worden geregistreerd. Deze logboeken kunnen worden bekeken in de Azure-portal of worden opgehaald met Microsoft-hulpprogramma's zoals Power BI of hulpprogramma's van derden. Controlelogboeken zijn beschikbaar via de api voor portal, PowerShell, CLI en Rest. Zie Controlebewerkingen met Resource Manager voor meer informatie over controlelogboeken. Controlelogboeken zijn beschikbaar voor bewerkingen die worden uitgevoerd op alle netwerkbronnen.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">IP-stroom controleert</a>** - Controleert of een pakket is toegestaan of geweigerd op basis van stroominformatie 5-tuple-pakketparameters (Doel-IP, Bron-IP, Doelpoort, Bronpoort en Protocol). Als het pakket wordt geweigerd door een netwerkbeveiligingsgroep, wordt de regel en netwerkbeveiligingsgroep die het pakket heeft geweigerd, geretourneerd.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Volgende hop</a>** : hiermee bepaalt u de volgende hop voor pakketten die worden gerouteerd in de Azure Network Fabric, zodat u verkeerd geconfigureerde door de gebruiker gedefinieerde routes diagnosticeren.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Weergave beveiligingsgroep</a>** - Krijgt de effectieve en toegepaste beveiligingsregels die op een vm worden toegepast.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">NSG Flow logging</a>** - Met stroomlogboeken voor netwerkbeveiligingsgroepen u logboeken vastleggen die betrekking hebben op verkeer dat is toegestaan of geweigerd door de beveiligingsregels in de groep. De stroom wordt gedefinieerd door een 5-tuple-informatie : Bron-IP, Doel-IP, Bronpoort, Doelpoort en Protocol.

## <a name="azure-storage-analytics"></a>Azure Storage Analytics

[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) kan statistieken opslaan die geaggregeerde transactiestatistieken en capaciteitsgegevens over aanvragen voor een opslagservice bevatten. Transacties worden gerapporteerd op zowel api-bewerkingsniveau als op het niveau van de opslagservice en de capaciteit wordt gerapporteerd op het niveau van de opslagservice. Statistiekengegevens kunnen worden gebruikt om het gebruik van opslagservices te analyseren, problemen met aanvragen tegen de opslagservice te diagnosticeren en de prestaties van toepassingen die een service gebruiken te verbeteren.

[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) voert logboekregistratie uit en biedt metrische gegevens voor een opslagaccount. U deze gegevens gebruiken om aanvragen te traceren, gebruikstrends te analyseren en problemen met uw opslagaccount te diagnosticeren. Storage Analytics-logboekregistratie is beschikbaar voor de [services Blob, Queue en Table.](../../storage/common/storage-introduction.md) Opslaganalyse registreert gedetailleerde informatie over geslaagde en mislukte aanvragen bij een opslagservice.

Deze informatie kan worden gebruikt voor het bewaken van afzonderlijke aanvragen en voor het vaststellen van problemen met een opslagservice. Aanvragen worden op een best-effort basis geregistreerd. Logboekvermeldingen worden alleen gemaakt als er aanvragen zijn ingediend tegen het eindpunt van de service. Als een opslagaccount bijvoorbeeld activiteit heeft in het Blob-eindpunt, maar niet in de eindpunten tabel of wachtrij, worden alleen logboeken met betrekking tot de Blob-service gemaakt.

Als u Storage Analytics wilt gebruiken, moet u deze afzonderlijk inschakelen voor elke service die u wilt controleren. U het inschakelen in de [Azure-portal;](https://portal.azure.com/) Zie Een [opslagaccount controleren in de Azure-portal](../../storage/common/storage-monitor-storage-account.md)voor meer informatie. U Storage Analytics ook programmatisch inschakelen via de REST API of de clientbibliotheek. Gebruik de bewerking Serviceeigenschappen instellen om Storage Analytics afzonderlijk in te schakelen voor elke service.

De geaggregeerde gegevens worden opgeslagen in een bekende blob (voor logboekregistratie) en in bekende tabellen (voor metrische gegevens), die toegankelijk kunnen zijn via de Blob-service en Tabelservice-API's.

Storage Analytics heeft een limiet van 20 TB voor de hoeveelheid opgeslagen gegevens die onafhankelijk is van de totale limiet voor uw opslagaccount. Alle logboeken worden opgeslagen in [blokblobs](../../storage/common/storage-analytics.md) in een container met de naam $logs, die automatisch worden gemaakt wanneer Storage Analytics is ingeschakeld voor een opslagaccount.

De volgende acties van Storage Analytics zijn factureerbaar:

-   Aanvragen om blobs te maken voor logboekregistratie
-   Verzoeken om tabelentiteiten voor statistieken te maken.

> [!Note]
> Zie [Storage Analytics en Facturering](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)voor meer informatie over facturerings- en gegevensbewaarbeleid.
> Voor optimale prestaties wilt u het aantal veelgebruikte schijven aan de virtuele machine beperken om mogelijke beperking te voorkomen. Als niet alle schijven tegelijkertijd sterk worden gebruikt, kan het opslagaccount een grotere getalschijf ondersteunen.

> [!Note]
> Zie [Schaalbaarheidsdoelen voor standaardopslagaccounts voor](../../storage/common/scalability-targets-standard-account.md)meer informatie over opslagaccountlimieten.


De volgende typen geverifieerde en anonieme verzoeken worden geregistreerd.

| Geverifieerd  | Anoniem|
| :------------- | :-------------|
| Geslaagde aanvragen | Geslaagde aanvragen |
|Mislukte aanvragen, inclusief time-out-, beperkings-, netwerk-, autorisatiefouten en overige fouten | Aanvragen met behulp van een SAS (Shared Access Signature), inclusief mislukte en succesvolle aanvragen |
| Aanvragen met behulp van een SAS (Shared Access Signature), inclusief mislukte en succesvolle aanvragen |Time-outfouten voor zowel de client als de server |
|   Aanvragen voor analysegegevens |    Mislukte GET-aanvragen met foutcode 304 (Niet gewijzigd) |
| Verzoeken van Storage Analytics zelf, zoals het maken of verwijderen van logboeken, worden niet geregistreerd. Een volledige lijst met de geregistreerde gegevens wordt gedocumenteerd in de onderwerpen [Storage Analytics Logged Operations en Status Messages](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) en Storage Analytics Log [Format.](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) | Alle andere mislukte anonieme verzoeken worden niet geregistreerd. Een volledige lijst met de geregistreerde gegevens wordt gedocumenteerd in de [geregistreerde bewerkingen en statusberichten en](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) [opslaganalyselogboekindeling](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD bevat ook een volledige suite van mogelijkheden voor identiteitsbeheer, waaronder multi-factor authenticatie, apparaatregistratie, selfservice wachtwoordbeheer, selfservicegroepsbeheer, geprivilegieerd accountbeheer, op rollen gebaseerde toegang controle, controle van het toepassingsgebruik, uitgebreide controle en beveiligingsbewaking en -waarschuwingen.

-   Verbeter de toepassingsbeveiliging met Azure AD-multifactorauthenticatie en voorwaardelijke toegang.

-   Monitor het gebruik van toepassingen en bescherm uw bedrijf tegen geavanceerde bedreigingen met beveiligingsrapportage en -bewaking.

Azure Active Directory (Azure AD) bevat beveiligings-, activiteits- en controlerapporten voor uw directory. [Met het Azure Active Directory-controlerapport](../../active-directory/active-directory-reporting-azure-portal.md) kunnen klanten geprivilegieerde acties identificeren die zijn uitgevoerd in hun Azure Active Directory. Bevoorrechte acties omvatten hoogtewijzigingen (bijvoorbeeld het maken van rollen of het opnieuw instellen van wachtwoorden), het wijzigen van beleidsconfiguraties (bijvoorbeeld wachtwoordbeleid) of wijzigingen in directoryconfiguratie (bijvoorbeeld wijzigingen in domeinfederatie-instellingen).

De rapporten bieden de controlerecord voor de gebeurtenisnaam, de actor die de actie heeft uitgevoerd, de doelbron die door de wijziging is beïnvloed en de datum en tijd (in UTC). Klanten kunnen de lijst met controlegebeurtenissen voor hun Azure Active Directory ophalen via de [Azure-portal](https://portal.azure.com/), zoals beschreven in [Uw controlelogboeken weergeven.](../../active-directory/reports-monitoring/overview-reports.md) Hierna volgt een lijst met de beschikbare rapporten:

| Beveiligingsrapporten  | Activiteitsrapporten| Controlerapporten |
| :------------- | :-------------| :-------------|
|Aanmeldingen van onbekende bronnen | Toepassingsgebruik: samenvatting | Directorycontrolerapport |
|Aanmeldingen na meerdere mislukte pogingen | Toepassingsgebruik: gedetailleerd |   |
|Aanmeldingen vanuit meerdere locaties | Toepassingsdashboard |  |
|Aanmeldingen van IP-adressen met verdachte activiteit |Fouten bij het inrichten van een account |  |
|Onregelmatige aanmeldingsactiviteiten |Afzonderlijke gebruikersapparaten |  |
|Aanmeldingen vanaf mogelijk geïnfecteerde apparaten |Afzonderlijke gebruikersactiviteiten |   |
|Gebruikers met afwijkende aanmeldingsactiviteiten |Rapport met groepsactiviteiten |   |
| |Rapport voor de registratie van opnieuw ingestelde wachtwoorden |   |
| |Activiteit voor wachtwoord opnieuw instellen |   |



De gegevens van deze rapporten kunnen nuttig zijn voor uw toepassingen, zoals SIEM-systemen, audit en business intelligence-tools. De Azure [AD-rapportage-API's](../../active-directory/active-directory-reporting-api-getting-started-azure-portal.md) bieden programmatische toegang tot de gegevens via een set API's op basis van REST. U deze API's bellen vanuit verschillende programmeertalen en -tools.

Gebeurtenissen in het Azure AD-controlerapport worden 180 dagen bewaard.

> [!Note]
> Zie [Azure Active Directory-beleid](../../active-directory/reports-monitoring/reference-reports-data-retention.md)voor het bewaren van rapporten voor meer informatie over het bewaren van rapporten .

Voor klanten die hun [controlegebeurtenissen](../../active-directory/active-directory-reporting-activity-audit-logs.md) voor langere bewaarperioden willen opslaan, kan de Rapportage-API worden gebruikt om regelmatig auditgebeurtenissen in een apart gegevensarchief op te halen.

## <a name="summary"></a>Samenvatting

In dit artikel wordt een overzicht van de bescherming van uw privacy en het beveiligen van uw gegevens, terwijl u software en services levert waarmee u de IT-infrastructuur van uw organisatie beheren. Microsoft erkent dat wanneer ze hun gegevens aan anderen toevertrouwen, dat vertrouwen strenge beveiliging vereist. Microsoft voldoet aan strikte nalevings- en beveiligingsrichtlijnen - van het schrijven van code tot de uitvoering van een service. Het beveiligen en beveiligen van gegevens is een topprioriteit bij Microsoft.

In dit artikel wordt uitgelegd

-   Hoe gegevens worden verzameld, verwerkt en beveiligd in de Azure Monitor-suite.

-   Analyseer snel gebeurtenissen in meerdere gegevensbronnen. Identificeer beveiligingsrisico's en begrijp de omvang en impact van bedreigingen en aanvallen om de schade van een inbreuk op de beveiliging te beperken.

-   Identificeer aanvalspatronen door uitgaand kwaadaardig IP-verkeer en schadelijke bedreigingstypen te visualiseren. Begrijp de beveiligingshouding van uw hele omgeving, ongeacht het platform.

-   Leg alle logboek- en gebeurtenisgegevens vast die nodig zijn voor een beveiligings- of nalevingsaudit. Verlaag de tijd en middelen die nodig zijn om een beveiligingscontrole te voorzien van een volledige, doorzoekbare en exporteerbare logboek- en gebeurtenisgegevensset.

<ul>
<li>Verzamel beveiligingsgerelateerde gebeurtenissen, audit en inbreukanalyse om uw assets goed in de gaten te houden:</li>
<ul>
<li>Veiligheidshouding</li>
<li>Opmerkelijk probleem</li>
<li>Samenvattingen bedreigingen</li>
</ul>
</ul>

## <a name="next-steps"></a>Volgende stappen

- [Ontwerp en operationele beveiliging](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft ontwerpt zijn services en software met beveiliging in het achterhoofd om ervoor te zorgen dat de cloudinfrastructuur veerkrachtig is en tegen aanvallen wordt beschermd.

- [Azure Monitor-logboeken | Beveiliging & naleving](https://www.microsoft.com/cloud-platform/security-and-compliance)

Gebruik Microsoft-beveiligingsgegevens en -analyses om intelligentere en effectievere bedreigingsdetectie uit te voeren.

- [Planning en bewerkingen van Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md) Een reeks stappen en taken die u volgen om uw gebruik van Security Center te optimaliseren op basis van de beveiligingsvereisten en het cloudbeheermodel van uw organisatie.

