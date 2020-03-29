---
title: Een cloudservice bijwerken | Microsoft Documenten
description: Meer informatie over het bijwerken van cloudservices in Azure. Meer informatie over hoe een update voor een cloudservice verloopt om de beschikbaarheid te garanderen.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 731f4e8cc8a93f33d6887f44fc8d09585e92a75a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360341"
---
# <a name="how-to-update-a-cloud-service"></a>Een cloudservice bijwerken

Het bijwerken van een cloudservice, inclusief zowel de rollen als het gastbesturingssysteem, is een proces in drie stappen. Eerst moeten de binaire bestanden en configuratiebestanden voor de nieuwe cloudservice of OS-versie worden geüpload. Vervolgens reserveert Azure reken- en netwerkbronnen voor de cloudservice op basis van de vereisten van de nieuwe cloudserviceversie. Ten slotte voert Azure een rolling upgrade uit om de tenant stapsgewijs bij te werken naar de nieuwe versie of gastbesturingssysteem, met behoud van uw beschikbaarheid. Dit artikel bespreekt de details van deze laatste stap - de rolling upgrade.

## <a name="update-an-azure-service"></a>Een Azure-service bijwerken
Azure ordent uw rolinstanties in logische groeperingen genaamd upgradedomeinen (UD). Upgradedomeinen (UD) zijn logische sets van rolinstanties die als groep worden bijgewerkt.  Azure werkt een cloudservice één UD tegelijk bij, waardoor exemplaren in andere UD's verkeer kunnen blijven weergeven.

Het standaardaantal upgradedomeinen is 5. U een ander aantal upgradedomeinen opgeven door het kenmerk upgradeDomainCount op te nemen in het definitiebestand van de service (.csdef). Zie [Azure Cloud Services Definition Schema (.csdef File) voor](https://docs.microsoft.com/azure/cloud-services/schema-csdef-file)meer informatie over het kenmerk upgradeDomainCount.

Wanneer u een in-place update van een of meer rollen in uw service uitvoert, werkt Azure sets rolexemplaren bij op basis van het upgradedomein waartoe ze behoren. Azure werkt alle exemplaren in een bepaald upgradedomein bij - stop ze, werkt ze bij, brengt ze online terug - en gaat vervolgens naar het volgende domein. Door alleen de exemplaren te stoppen die in het huidige upgradedomein worden uitgevoerd, zorgt Azure ervoor dat er een update plaatsvindt met de minst mogelijke impact op de lopende service. Zie [Hoe de update](#howanupgradeproceeds) later in dit artikel verloopt voor meer informatie.

> [!NOTE]
> Hoewel de termen **bijwerken** en **upgraden** iets andere betekenis hebben in de context Azure, kunnen ze door elkaar worden gebruikt voor de processen en beschrijvingen van de functies in dit document.
>
>

Uw service moet ten minste twee exemplaren van een rol definiëren om die rol op hun plaats te kunnen bijwerken zonder downtime. Als de service slechts uit één exemplaar van één rol bestaat, is uw service niet beschikbaar totdat de update op zijn plaats is voltooid.

In dit onderwerp vindt u de volgende informatie over Azure-updates:

* [Toegestane servicewijzigingen tijdens een update](#AllowedChanges)
* [Hoe een upgrade verloopt](#howanupgradeproceeds)
* [Terugdraaien van een update](#RollbackofanUpdate)
* [Meerdere muterende bewerkingen starten voor een lopende implementatie](#multiplemutatingoperations)
* [Verdeling van rollen over upgradedomeinen](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Toegestane servicewijzigingen tijdens een update
In de volgende tabel worden de toegestane wijzigingen in een service weergegeven tijdens een update:

| Wijzigingen die zijn toegestaan in hosting, services en rollen | In-place update | Geënsceneerd (VIP-swap) | Verwijderen en opnieuw implementeren |
| --- | --- | --- | --- |
| Besturingssysteem |Ja |Ja |Ja |
| .NET-vertrouwensniveau |Ja |Ja |Ja |
| Virtuele machinegrootte<sup>1</sup> |Ja<sup>2</sup> |Ja |Ja |
| Instellingen voor lokale opslag |Slechts<sup>2 verhogen</sup> |Ja |Ja |
| Rollen in een service toevoegen of verwijderen |Ja |Ja |Ja |
| Aantal exemplaren van een bepaalde rol |Ja |Ja |Ja |
| Aantal of type eindpunten voor een service |Ja<sup>2</sup> |Nee |Ja |
| Namen en waarden van configuratie-instellingen |Ja |Ja |Ja |
| Waarden (maar geen namen) van configuratie-instellingen |Ja |Ja |Ja |
| Nieuwe certificaten toevoegen |Ja |Ja |Ja |
| Bestaande certificaten wijzigen |Ja |Ja |Ja |
| Nieuwe code implementeren |Ja |Ja |Ja |

<sup>1</sup> Groottewijziging beperkt tot de subset van formaten die beschikbaar zijn voor de cloudservice.

<sup>2</sup> Vereist Azure SDK 1.5 of hoger versies.

> [!WARNING]
> Als u de grootte van de virtuele machine wijzigt, worden lokale gegevens vernietigd.
>
>

De volgende items worden niet ondersteund tijdens een update:

* De naam van een rol wijzigen. Verwijder en voeg de rol toe aan de nieuwe naam.
* Het aantal upgradedomeinen wijzigen.
* Het verkleinen van de grootte van de lokale bronnen.

Als u andere updates uitvoert voor de definitie van uw service, zoals het verkleinen van de grootte van lokale bronnen, moet u in plaats daarvan een VIP-swap-update uitvoeren. Zie [Implementatie ruilen voor](/previous-versions/azure/reference/ee460814(v=azure.100))meer informatie .

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Hoe een upgrade verloopt
U zelf bepalen of u alle rollen in uw service of één rol in de service wilt bijwerken. In beide gevallen worden alle exemplaren van elke rol die wordt geüpgraded en behoren tot het eerste upgradedomein gestopt, geüpgraded en weer online gebracht. Zodra ze weer online zijn, worden de exemplaren in het tweede upgradedomein gestopt, geüpgraded en weer online gebracht. Een cloudservice kan maximaal één upgrade tegelijk actief hebben. De upgrade wordt altijd uitgevoerd ten opzichte van de nieuwste versie van de cloudservice.

In het volgende diagram ziet u hoe de upgrade verloopt als u alle rollen in de service upgradet:

![Upgradeservice](media/cloud-services-update-azure-service/IC345879.png "Upgradeservice")

In dit volgende diagram ziet u hoe de update verloopt als u slechts één rol upgradet:

![Upgraderol](media/cloud-services-update-azure-service/IC345880.png "Upgraderol")  

Tijdens een automatische update evalueert de Azure Fabric Controller periodiek de status van de cloudservice om te bepalen wanneer de volgende UD veilig is om de volgende UD te lopen. Deze gezondheidsevaluatie wordt per rol uitgevoerd en houdt alleen rekening met exemplaren in de nieuwste versie (d.w.z. exemplaren van UD's die al zijn gelopen). Het controleert of een minimum aantal rolexemplaren, voor elke rol, een bevredigende eindtoestand hebben bereikt.

### <a name="role-instance-start-timeout"></a>Time-out voor startfunctie voor rolinstantie
De fabriccontroller wacht 30 minuten voordat elke rolinstantie een gestarte status bereikt. Als de time-outduur verstrijkt, blijft de fabriccontroller naar de volgende rolinstantie lopen.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Impact om gegevens te stimuleren tijdens cloudservice-upgrades

Wanneer u een service upgradet van één exemplaar naar meerdere exemplaren, wordt uw service omlaag gebracht terwijl de upgrade wordt uitgevoerd vanwege de manier waarop Azure-upgrades services uitvoeren. De serviceniveauovereenkomst die de beschikbaarheid van de service garandeert, is alleen van toepassing op services die met meer dan één exemplaar worden geïmplementeerd. In de volgende lijst wordt beschreven hoe de gegevens op elk station worden beïnvloed door elk azure-service-upgradescenario:

|Scenario|C-station|D Drive|E-station|
|--------|-------|-------|-------|
|VM opnieuw opstarten|Bewaard|Bewaard|Bewaard|
|Portal opnieuw opstarten|Bewaard|Bewaard|Vernietigd|
|Portal reimage|Bewaard|Vernietigd|Vernietigd|
|In-Place Upgrade|Bewaard|Bewaard|Vernietigd|
|Knooppuntmigratie|Vernietigd|Vernietigd|Vernietigd|

Houd er rekening mee dat de E: drive in de bovenstaande lijst de hoofdschijf van de rol vertegenwoordigt en niet hard gecodeerd mag zijn. Gebruik in plaats daarvan de omgevingsvariabele **%RoleRoot%om** de schijf weer te geven.

Als u de downtime wilt minimaliseren bij het upgraden van een service met één instantie, implementeert u een nieuwe multi-instanceservice op de staging-server en voert u een VIP-swap uit.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Terugdraaien van een update
Azure biedt flexibiliteit bij het beheren van services tijdens een update door u extra bewerkingen op een service te laten starten, nadat de eerste updateaanvraag is geaccepteerd door de Azure Fabric Controller. Een terugdraaiing kan alleen worden uitgevoerd wanneer een update (configuratiewijziging) of upgrade in de **lopende** status van de implementatie is. Een update of upgrade wordt beschouwd als in uitvoering zolang er ten minste één exemplaar van de service is die nog niet is bijgewerkt naar de nieuwe versie. Als u wilt testen of een terugdraaiing is toegestaan, controleert u of de waarde van de gemarkeerde vlag RollbackA, geretourneerd door [Implementatie halen](/previous-versions/azure/reference/ee460804(v=azure.100)) en [Bewerkingen Voor Cloudservice-eigenschappen](/previous-versions/azure/reference/ee460806(v=azure.100)) krijgen, is ingesteld op true.

> [!NOTE]
> Het heeft alleen zin om Rollback te bellen op een **in-place** update of upgrade, omdat VIP swap upgrades te betrekken het vervangen van een hele lopende instantie van uw dienst met een andere.
>
>

Het terugdraaien van een voortgangsupdate heeft de volgende gevolgen voor de implementatie:

* Rolexemplaren die nog niet zijn bijgewerkt of geüpgraded naar de nieuwe versie, worden niet bijgewerkt of bijgewerkt, omdat in die exemplaren al de doelversie van de service wordt uitgevoerd.
* Rolexemplaren die al waren bijgewerkt of geüpgraded naar\*de nieuwe versie van het servicepakket (.cspkg)-bestand of het serviceconfiguratiebestand (.cscfg)\*bestand (of beide bestanden) worden teruggezet naar de pre-upgradeversie van deze bestanden.

Dit wordt functioneel geleverd door de volgende functies:

* De [bewerking Rollback-update of upgrade,](/previous-versions/azure/reference/hh403977(v=azure.100)) die kan worden aangeroepen voor een configuratie-update (geactiveerd door [wijzigingsconfiguratie aan](/previous-versions/azure/reference/ee460809(v=azure.100))te roepen) of een upgrade (geactiveerd door [upgrade-implementatie](/previous-versions/azure/reference/ee460793(v=azure.100))te bellen), zolang er ten minste één exemplaar in de service is die nog niet is bijgewerkt naar de nieuwe versie.
* Het element Vergrendeld en het element RollbackAtoegestaan, dat wordt geretourneerd als onderdeel van de antwoordtekst van de [bewerkingen Implementatie en](/previous-versions/azure/reference/ee460804(v=azure.100)) [Cloudservice-eigenschappen ontvangen:](/previous-versions/azure/reference/ee460806(v=azure.100))

  1. Met het element Vergrendeld u detecteren wanneer een muterende bewerking kan worden aangeroepen bij een bepaalde implementatie.
  2. Met het element RollbackAllowed u detecteren wanneer de bewerking [Rollback-update of upgrade](/previous-versions/azure/reference/hh403977(v=azure.100)) kan worden opgeroepen voor een bepaalde implementatie.

  Om een terugdraaiing uit te voeren, hoeft u niet zowel de vergrendelde als de toegestane terugdraaielementen te controleren. Het volstaat om te bevestigen dat RollbackA toegestaan is ingesteld op true. Deze elementen worden alleen geretourneerd als deze methoden worden aangeroepen met behulp van de aanvraagheader ingesteld op "x-ms-versie: 2011-10-01" of een latere versie. Zie [ServicemanagementVersioning](/previous-versions/azure/gg592580(v=azure.100))voor meer informatie over versiekoppen.

Er zijn enkele situaties waarin een terugdraaiing van een update of upgrade niet wordt ondersteund, dit zijn als volgt:

* Vermindering van lokale resources - Als de update de lokale resources voor een rol verhoogt, staat het Azure-platform het terugdraaien niet toe.
* Quotabeperkingen : als de update een schaalbewerking was, beschikt u mogelijk niet meer over voldoende rekenquotum om de terugdraaibewerking te voltooien. Aan elk Azure-abonnement is een quotum gekoppeld dat het maximum aantal cores opgeeft dat kan worden verbruikt door alle gehoste services die tot dat abonnement behoren. Als het uitvoeren van een terugdraaiing van een bepaalde update uw abonnement boven quota plaatst, wordt een terugdraaiing niet ingeschakeld.
* Raceconditie - Als de eerste update is voltooid, is een terugdraaiing niet mogelijk.

Een voorbeeld van wanneer het terugdraaien van een update nuttig kan zijn, is als u de bewerking [Upgrade-implementatie](/previous-versions/azure/reference/ee460793(v=azure.100)) in de handmatige modus gebruikt om de snelheid te bepalen waarmee een belangrijke upgrade in plaats van uw Azure-gehoste service wordt uitgerold.

Tijdens de uitrol van de upgrade roept u [Upgrade Deployment](/previous-versions/azure/reference/ee460793(v=azure.100)) in de handmatige modus aan en begint u upgradedomeinen te lopen. Als u op een gegeven moment, terwijl u de upgrade monitort, enkele rolexemplaren opmerkt in de eerste upgradedomeinen die u onderzoekt, niet meer reageren, u de bewerking [Rollback-update of upgrade](/previous-versions/azure/reference/hh403977(v=azure.100)) op de implementatie bellen, waardoor de exemplaren die nog niet zijn bijgewerkt en rollback-exemplaren die zijn bijgewerkt naar het vorige servicepakket en de vorige configuratie, onaangeroerd blijven.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Meerdere muterende bewerkingen starten voor een lopende implementatie
In sommige gevallen u meerdere gelijktijdige muterende bewerkingen starten op een lopende implementatie. U bijvoorbeeld een service-update uitvoeren en terwijl die update in uw service wordt uitgerold, wilt u een wijziging aanbrengen, bijvoorbeeld om de update terug te rollen, een andere update toe te passen of zelfs de implementatie te verwijderen. Een geval waarin dit nodig kan zijn, is als een service-upgrade buggycode bevat waardoor een geüpgradede rolinstantie herhaaldelijk crasht. In dit geval kan de Azure Fabric Controller geen vooruitgang boeken bij het toepassen van die upgrade omdat een onvoldoende aantal exemplaren in het geüpgradede domein in orde is. Deze status wordt aangeduid als een *vastzittende implementatie*. U de implementatie loskoppelen door de update terug te draaien of een nieuwe update over de niet-falende update toe te passen.

Zodra het eerste verzoek om de service bij te werken of te upgraden is ontvangen door de Azure Fabric Controller, u de volgende muterende bewerkingen starten. Dat wil zeggen, je hoeft niet te wachten tot de eerste bewerking is voltooid voordat je een andere muterende bewerking starten.

Het starten van een tweede updatebewerking terwijl de eerste update aan de gang is, wordt vergelijkbaar met de terugdraaibewerking uitgevoerd. Als de tweede update in de automatische modus is, wordt het eerste upgradedomein onmiddellijk bijgewerkt, wat mogelijk leidt tot exemplaren van meerdere upgradedomeinen die op hetzelfde moment offline zijn.

De muterende bewerkingen zijn als volgt: [Implementatieconfiguratie wijzigen,](/previous-versions/azure/reference/ee460809(v=azure.100)) [Implementatie bijwerken,](/previous-versions/azure/reference/ee460793(v=azure.100)) [Implementatiestatus bijwerken,](/previous-versions/azure/reference/ee460808(v=azure.100)) [Implementatie verwijderen](/previous-versions/azure/reference/ee460815(v=azure.100))en [Rollback-update of -upgrade .](/previous-versions/azure/reference/hh403977(v=azure.100))

Twee bewerkingen, [Get Deployment](/previous-versions/azure/reference/ee460804(v=azure.100)) en [Get Cloud Service Properties,](/previous-versions/azure/reference/ee460806(v=azure.100))retourneer de vergrendelde vlag die kan worden onderzocht om te bepalen of een muterende bewerking kan worden aangeroepen op een bepaalde implementatie.

Als u de versie van deze methoden wilt aanroepen die de vergrendelde vlag retourneert, moet u de aanvraagkopinstellen op "x-ms-versie: 2011-10-01" of een later. Zie [ServicemanagementVersioning](/previous-versions/azure/gg592580(v=azure.100))voor meer informatie over versiekoppen.

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Verdeling van rollen over upgradedomeinen
Azure distribueert exemplaren van een rol gelijkmatig over een ingesteld aantal upgradedomeinen, die kunnen worden geconfigureerd als onderdeel van het servicedefinitiebestand (.csdef). Het maximum aantal upgradedomeinen is 20 en de standaardinstelling 5. Zie [Azure Service Definition Schema (.csdef File)](cloud-services-model-and-package.md#csdef)voor meer informatie over het wijzigen van het servicedefinitiebestand.

Als uw rol bijvoorbeeld tien exemplaren heeft, bevat elk upgradedomein standaard twee exemplaren. Als uw rol 14 exemplaren heeft, bevatten vier van de upgradedomeinen drie exemplaren en een vijfde domein twee.

Upgradedomeinen worden geïdentificeerd met een nulindex: het eerste upgradedomein heeft een ID van 0 en het tweede upgradedomein heeft een ID van 1, enzovoort.

In het volgende diagram ziet u hoe een service dan twee rollen bevat wanneer de service twee upgradedomeinen definieert. De service wordt uitgevoerd acht exemplaren van de webrol en negen exemplaren van de werknemerrol.

![Distributie van upgradedomeinen](media/cloud-services-update-azure-service/IC345533.png "Distributie van upgradedomeinen")

> [!NOTE]
> Houd er rekening mee dat Azure bepaalt hoe instanties worden toegewezen aan upgradedomeinen. Het is niet mogelijk om aan te geven welke instanties aan welk domein worden toegewezen.
>
>

## <a name="next-steps"></a>Volgende stappen
[Cloud Services beheren](cloud-services-how-to-manage-portal.md)  
[Cloudservices controleren](cloud-services-how-to-monitor.md)  
[Cloud Services configureren](cloud-services-how-to-configure-portal.md)  



