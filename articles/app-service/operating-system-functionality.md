---
title: Functionaliteit van besturingssysteem
description: Meer informatie over de OS-functionaliteit in Azure App Service op Windows. Ontdek welke typen bestanden, netwerken en registertoegang uw app krijgt.
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.topic: article
ms.date: 10/30/2018
ms.custom: seodec18
ms.openlocfilehash: ed84cb2b0cb8d98b12fe787e49c400ba47e4e38a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671612"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Functie van het besturingssysteem op Azure App Service
In dit artikel wordt de algemene functionaliteit van het besturingssysteem basislijn beschreven die beschikbaar is voor alle Windows-apps die op [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)worden uitgevoerd. Deze functionaliteit omvat bestands-, netwerk- en registertoegang en diagnostische logboeken en gebeurtenissen. 

> [!NOTE] 
> [Linux-apps](containers/app-service-linux-intro.md) in App Service worden uitgevoerd in hun eigen containers. Geen toegang tot het host besturingssysteem is toegestaan, je hebt root toegang tot de container. Ook voor [apps die in Windows-containers worden uitgevoerd,](app-service-web-get-started-windows-container.md)hebt u administratieve toegang tot de container, maar geen toegang tot het hostbesturingssysteem. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>App-planlagen
App Service voert klantapps uit in een hostingomgeving met meerdere tenantn. Apps die worden geïmplementeerd in de lagen **Gratis** en **Gedeeld,** worden uitgevoerd in werkprocessen op gedeelde virtuele machines, terwijl apps die zijn geïmplementeerd in de **standaard-** en **premiumlagen** worden uitgevoerd op virtuele machines die specifiek zijn bedoeld voor de apps die zijn gekoppeld aan één klant.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Omdat App Service een naadloze schaling-ervaring tussen verschillende lagen ondersteunt, blijft de beveiligingsconfiguratie die wordt afgedwongen voor App Service-apps hetzelfde. Dit zorgt ervoor dat apps zich niet plotseling anders gedragen, zonder onverwachte manieren, wanneer app-service-abonnement van de ene laag naar de andere schakelt.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Ontwikkelingskaders
Prijsniveaus van App Service bepalen de hoeveelheid rekenbronnen (CPU, schijfopslag, geheugen en netwerkuitgang) die beschikbaar zijn voor apps. De breedte van de frameworkfunctionaliteit die beschikbaar is voor apps blijft echter hetzelfde, ongeacht de schaalniveaus.

App Service ondersteunt een verscheidenheid aan ontwikkelingsframeworks, waaronder ASP.NET, klassieke ASP, node.js, PHP en Python - die allemaal worden uitgevoerd als extensies binnen IIS. Om de beveiligingsconfiguratie te vereenvoudigen en te normaliseren, voeren App Service-apps doorgaans de verschillende ontwikkelframeworks uit met hun standaardinstellingen. Een benadering van het configureren van apps had kunnen zijn om het API-oppervlak en de functionaliteit voor elk afzonderlijk ontwikkelingskader aan te passen. App Service hanteert in plaats daarvan een meer algemene benadering door een gemeenschappelijke basislijn van de functionaliteit van het besturingssysteem in te schakelen, ongeacht het ontwikkelingskader van een app.

In de volgende secties worden de algemene soorten functionaliteit van het besturingssysteem samengevat die beschikbaar zijn voor App Service-apps.

<a id="FileAccess"></a>

## <a name="file-access"></a>Bestandstoegang
Er bestaan verschillende schijven binnen App Service, waaronder lokale schijven en netwerkstations.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Lokale stations
In de kern is App Service een service die wordt uitgevoerd bovenop de Azure PaaS -infrastructuur (platform as a service). Als gevolg hiervan zijn de lokale stations die zijn 'gekoppeld' aan een virtuele machine dezelfde schijftypen die beschikbaar zijn voor elke werknemersrol die in Azure wordt uitgevoerd. Dit omvat:

- Een station voor het besturingssysteem (de D:\ station)
- Een toepassingsstation met Azure Package cspkg-bestanden die uitsluitend worden gebruikt door App Service (en niet toegankelijk zijn voor klanten)
- Een "gebruiker" station (de C:\ schijf), waarvan de grootte varieert afhankelijk van de grootte van de VM. 

Het is belangrijk om uw schijfgebruik te controleren naarmate uw toepassing groeit. Als het schijfquotum is bereikt, kan dit nadelige gevolgen hebben voor uw toepassing. Bijvoorbeeld: 

- De app kan een fout plaatsen die aangeeft dat er niet genoeg ruimte op de schijf is.
- U schijffouten zien wanneer u naar de Kudu-console bladert.
- Implementatie vanuit Azure DevOps of `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)`Visual Studio kan mislukken met .
- Uw app kan trage prestaties ondervinden.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Netwerkstations (aka UNC-aandelen)
Een van de unieke aspecten van App Service die app-implementatie en onderhoud eenvoudig maakt, is dat alle gebruikersinhoud wordt opgeslagen op een set UNC-shares. Dit model brengt een goed beeld van het algemene patroon van inhoudsopslag dat wordt gebruikt door on-premises webhostingomgevingen met meerdere load-balanced servers. 

Binnen App Service is er een aantal UNC-aandelen gemaakt in elk datacenter. Aan elk UNC-aandeel wordt een percentage van de gebruikersinhoud voor alle klanten in elk datacenter toegewezen. Bovendien wordt alle bestandsinhoud voor het abonnement van één klant altijd op hetzelfde UNC-aandeel geplaatst. 

Vanwege de manier waarop Azure-services werken, verandert de specifieke virtuele machine die verantwoordelijk is voor het hosten van een UNC-aandeel in de loop van de tijd. Het is gegarandeerd dat UNC-aandelen worden gemonteerd door verschillende virtuele machines als ze worden gebracht omhoog en omlaag tijdens de normale loop van Azure-bewerkingen. Om deze reden mogen apps nooit hardgecodeerde veronderstellingen maken dat de machine-informatie in een UNC-bestandspad na verloop van tijd stabiel blijft. In plaats daarvan moeten ze gebruik maken van de handige *faux* absolute pad **D:\home\site** die App Service biedt. Deze faux absolute pad biedt een draagbare, app-en-user-agnostische methode voor het verwijzen naar de eigen app. Door **D:\home\site**te gebruiken, kan men gedeelde bestanden van app naar app overbrengen zonder dat u voor elke overdracht een nieuw absoluut pad hoeft te configureren.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Typen bestandstoegang die aan een app worden verleend
Het abonnement van elke klant heeft een gereserveerde directorystructuur voor een specifiek UNC-aandeel binnen een datacenter. Een klant kan meerdere apps hebben gemaakt binnen een specifiek datacenter, zodat alle mappen die behoren tot een abonnement van één klant worden gemaakt op hetzelfde UNC-aandeel. Het aandeel kan mappen bevatten, zoals die voor inhoud, fout- en diagnostische logboeken en eerdere versies van de app die zijn gemaakt door bronbeheer. Zoals verwacht zijn de app-mappen van een klant beschikbaar voor lees- en schrijftoegang tijdens de looptijd door de toepassingscode van de app.

Op de lokale stations die zijn gekoppeld aan de virtuele machine waarop een app wordt uitgevoerd, reserveert App Service een stuk ruimte op de C:\ schijf voor app-specifieke tijdelijke lokale opslag. Hoewel een app volledige lees-/schrijftoegang heeft tot zijn eigen tijdelijke lokale opslag, is die opslag echt niet bedoeld om direct door de toepassingscode te worden gebruikt. Integendeel, de bedoeling is om tijdelijke bestandsopslag te bieden voor IIS en webtoepassingsframeworks. App Service beperkt ook de hoeveelheid tijdelijke lokale opslag die beschikbaar is voor elke app om te voorkomen dat afzonderlijke apps overmatige hoeveelheden lokale bestandsopslag verbruiken.

Twee voorbeelden van hoe App Service tijdelijke lokale opslag gebruikt, zijn de map voor tijdelijke ASP.NET bestanden en de map voor iIS-gecomprimeerde bestanden. Het ASP.NET compilatiesysteem gebruikt de map 'Tijdelijke ASP.NET bestanden' als tijdelijke locatie voor compilatiecache. IIS gebruikt de map 'IIS Tijdelijke gecomprimeerde bestanden' om gecomprimeerde responsuitvoer op te slaan. Beide soorten bestandsgebruik (evenals andere) worden in App Service toegewezen aan tijdelijke lokale opslag per app. Deze remapping zorgt ervoor dat de functionaliteit blijft zoals verwacht.

Elke app in App Service wordt uitgevoerd als een willekeurige unieke low-privileged werknemer [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities)proces identiteit genaamd de "application pool identiteit", verder beschreven hier: . Toepassingscode gebruikt deze identiteit voor basisalleen-lezen toegang tot het station van het besturingssysteem (de D:\ station). Dit betekent dat toepassingscode algemene directorystructuren kan weergeven en veelvoorkomende bestanden op het station van het besturingssysteem kan lezen. Hoewel dit een enigszins breed toegangsniveau lijkt te zijn, zijn dezelfde mappen en bestanden toegankelijk wanneer u een werknemersrol indient in een Azure-gehoste service en de inhoud van het station leest. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Bestandstoegang in meerdere instanties
De startmap bevat de inhoud van een app en de toepassingscode kan deze schrijven. Als een app op meerdere exemplaren wordt uitgevoerd, wordt de startmap tussen alle instanties gedeeld, zodat alle instanties dezelfde map te zien krijgen. Als een app bijvoorbeeld geüploade bestanden opslaat in de thuismap, zijn deze bestanden onmiddellijk beschikbaar voor alle instanties. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Netwerktoegang
Toepassingscode kan TCP/IP- en UDP-protocollen gebruiken om uitgaande netwerkverbindingen te maken met internettoegankelijke eindpunten die externe services blootleggen. Apps kunnen dezelfde protocollen gebruiken om verbinding te maken met services binnen Azure, bijvoorbeeld door HTTPS-verbindingen met SQL Database tot stand te brengen.

Er is ook een beperkte mogelijkheid voor apps om een lokale netback-verbinding tot stand te brengen en een app te laten luisteren op die lokale netback-socket. Deze functie bestaat voornamelijk om apps in te schakelen die luisteren op lokale loopback-sockets als onderdeel van hun functionaliteit. Elke app ziet een "privé" loopback-verbinding. App "A" kan niet luisteren naar een lokale loopback-socket die is ingesteld door app "B".

Benoemde leidingen worden ook ondersteund als een inter-process communicatie (IPC) mechanisme tussen verschillende processen die gezamenlijk een app uitvoeren. De IIS FastCGI-module is bijvoorbeeld gebaseerd op benoemde leidingen om de afzonderlijke processen te coördineren die PHP-pagina's uitvoeren.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Codeuitvoering, processen en geheugen
Zoals eerder opgemerkt, worden apps uitgevoerd in werkprocessen met een laag bevoorrecht aantal werknemers met behulp van een willekeurige identiteit van de groep van toepassingen. Toepassingscode heeft toegang tot de geheugenruimte die is gekoppeld aan het werkproces, evenals onderliggende processen die kunnen worden voortgebracht door CGI-processen of andere toepassingen. De ene app heeft echter geen toegang tot het geheugen of de gegevens van een andere app, zelfs niet als deze zich op dezelfde virtuele machine bevindt.

Apps kunnen scripts of pagina's uitvoeren die zijn geschreven met ondersteunde webontwikkelingsframeworks. App Service configureert geen webframeworkinstellingen voor beperktere modi. ASP.NET apps die op App Service worden uitgevoerd, worden bijvoorbeeld in 'volledige' vertrouwensrelatie uitgevoerd in tegenstelling tot een beperktere vertrouwensmodus. Webframeworks, waaronder zowel klassieke ASP- als ASP.NET, kunnen in-process COM-componenten oproepen (maar niet buiten procesCOM-componenten) zoals ADO (ActiveX-gegevensobjecten) die standaard zijn geregistreerd op het Windows-besturingssysteem.

Apps kunnen willekeurige code spawnen en uitvoeren. Het is toegestaan voor een app om dingen te doen zoals een opdrachtshell spawnen of een PowerShell-script uitvoeren. Hoewel willekeurige code en processen uit een app kunnen worden voortgebracht, zijn uitvoerbare programma's en scripts nog steeds beperkt tot de bevoegdheden die worden verleend aan de bovenliggende toepassingsgroep. Een app kan bijvoorbeeld een uitvoerbare spawnen die een uitgaand HTTP-gesprek maakt, maar diezelfde uitvoerbare kan niet proberen het IP-adres van een virtuele machine los te maken van de NIC. Het voeren van een uitgaande netwerkaanroep is toegestaan voor code met een lage bevoegdheden, maar voor een poging om netwerkinstellingen op een virtuele machine opnieuw te configureren, zijn beheerdersbevoegdheden vereist.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnostische logboeken en gebeurtenissen
Loggegevens zijn een andere set gegevens die sommige apps proberen te openen. De typen logboekgegevens die beschikbaar zijn voor code die wordt uitgevoerd in App-service, omvatten diagnostische en logboekgegevens die worden gegenereerd door een app die ook gemakkelijk toegankelijk is voor de app. 

W3C HTTP-logboeken die door een actieve app worden gegenereerd, zijn bijvoorbeeld beschikbaar op een logboekmap in de locatie voor netwerkshare die voor de app is gemaakt, of beschikbaar in blob-opslag als een klant W3C-logboekregistratie heeft ingesteld op opslag. Met deze laatste optie kunnen grote hoeveelheden logboeken worden verzameld zonder het risico dat de limieten voor bestandsopslag die aan een netwerkaandeel zijn gekoppeld, worden overschreden.

In een vergelijkbare geest kunnen realtime diagnostische gegevens van .NET-apps ook worden geregistreerd met behulp van de .NET-tracerings- en diagnostische infrastructuur, met opties om de traceringsinformatie te schrijven naar het netwerkaandeel van de app of als alternatief voor een blob-opslaglocatie.

Gebieden van diagnostische logboekregistratie en tracering die niet beschikbaar zijn voor apps zijn Windows ETW-gebeurtenissen en algemene Windows-gebeurtenislogboeken (bijvoorbeeld logboeken voor gebeurtenissystemen voor systeem, toepassing en beveiliging). Aangezien ETW-traceinformatie mogelijk machinebreed (met de juiste ACL's) zichtbaar is, worden lees- en schrijftoegang tot ETW-gebeurtenissen geblokkeerd. Ontwikkelaars kunnen merken dat API-aanroepen om ETW-gebeurtenissen te lezen en te schrijven en algemene Windows-gebeurtenislogboeken lijken te werken, maar dat komt omdat App Service de aanroepen "faket", zodat ze lijken te slagen. In werkelijkheid heeft de toepassingscode geen toegang tot deze gebeurtenisgegevens.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Toegang tot het register
Apps hebben alleen-lezen toegang tot veel (hoewel niet alle) van het register van de virtuele machine waarop ze draaien. In de praktijk betekent dit dat registersleutels die alleen-lezen toegang tot de lokale groep Gebruikers toestaan, toegankelijk zijn voor apps. Een gebied van het register dat momenteel niet wordt ondersteund voor\_\_lees- of schrijftoegang is de HKEY CURRENT USER hive.

Schrijftoegang tot het register wordt geblokkeerd, inclusief toegang tot registersleutels per gebruiker. Vanuit het perspectief van de app mag schrijftoegang tot het register nooit worden ingeroepen in de Azure-omgeving, omdat apps kunnen (en doen) worden gemigreerd over verschillende virtuele machines. De enige permanente schrijfbare opslag waarop een app kan worden afhankelijk, is de inhoudsmapstructuur per app die is opgeslagen in de UNC-aandelen van de appservice. 

## <a name="remote-desktop-access"></a>Toegang tot extern bureaublad

App Service biedt geen externe bureaubladtoegang tot de VM-exemplaren.

## <a name="more-information"></a>Meer informatie

[Azure App Service sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) - De meest actuele informatie over de uitvoeringsomgeving van App Service. Deze pagina wordt rechtstreeks onderhouden door het App Service development team.

