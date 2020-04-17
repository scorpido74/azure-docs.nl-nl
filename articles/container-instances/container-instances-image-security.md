---
title: Beveiliging voor containerexemplaren
description: Aanbevelingen om afbeeldingen en geheimen voor Azure Container Instances en algemene beveiligingsoverwegingen voor elk containerplatform te beveiligen
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: 87fa28cf9bdb546a5f108284023a9f787645a1fd
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457991"
---
# <a name="security-considerations-for-azure-container-instances"></a>Beveiligingsoverwegingen voor Azure Container Instances

In dit artikel worden beveiligingsoverwegingen geïntroduceerd voor het gebruik van Azure Container Instances voor het uitvoeren van container-apps. Dit zijn een aantal van de onderwerpen:

> [!div class="checklist"]
> * **Beveiligingsaanbevelingen** voor het beheren van afbeeldingen en geheimen voor Azure Container Instances
> * **Overwegingen voor het containerecosysteem** gedurende de gehele levenscyclus van containers, voor elk containerplatform

## <a name="security-recommendations-for-azure-container-instances"></a>Beveiligingsaanbevelingen voor Azure Container Instances

### <a name="use-a-private-registry"></a>Een privéregister gebruiken

Containers worden gemaakt van installatiekopieën die zijn opgeslagen in een of meer opslagplaatsen. Deze repositories kunnen deel uitmaken van een openbaar register, zoals [Docker Hub,](https://hub.docker.com)of tot een privéregister. Een voorbeeld van een persoonlijk register is de [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), dat lokaal kan worden geïnstalleerd of in een virtuele privécloud. U ook cloudgebaseerde privécontainerregisterservices gebruiken, waaronder [Azure Container Registry.](../container-registry/container-registry-intro.md) 

Een openbaar beschikbare containerafbeelding garandeert geen veiligheid. Containerafbeeldingen bestaan uit meerdere softwarelagen en elke softwarelaag kan kwetsbaarheden hebben. Om de dreiging van aanvallen te verminderen, moet u afbeeldingen opslaan en ophalen uit een privéregister, zoals Azure Container Registry of Docker Trusted Registry. Azure Container Registry biedt niet alleen een beheerd privéregister, maar ondersteunt [verificatie op basis van serviceprincipal](../container-registry/container-registry-authentication.md) via Azure Active Directory voor basisverificatiestromen. Deze verificatie omvat op rollen gebaseerde toegang voor alleen-lezen (pull), schrijven (pushen) en andere machtigingen.

### <a name="monitor-and-scan-container-images"></a>Containerafbeeldingen controleren en scannen

Profiteer van oplossingen om containerafbeeldingen in een privéregister te scannen en potentiële kwetsbaarheden te identificeren. Het is belangrijk om de diepte van bedreigingsdetectie te begrijpen die de verschillende oplossingen bieden.

Azure Container Registry [integreert](../security-center/azure-container-registry-integration.md) bijvoorbeeld optioneel met Azure Security Center om automatisch alle Linux-afbeeldingen te scannen die naar een register worden geschoven. De geïntegreerde Qualys-scanner van Azure Security Center detecteert kwetsbaarheden in afbeeldingen, classificeert deze en biedt herstelrichtlijnen.

Beveiligingsmonitoring en oplossingen voor het scannen van afbeeldingen zoals [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) en [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) zijn ook beschikbaar via de Azure Marketplace.  

### <a name="protect-credentials"></a>Referenties beveiligen

Containers kunnen zich verspreiden over verschillende clusters en Azure-regio's. U moet dus referenties beveiligen die vereist zijn voor aanmeldingen of API-toegang, zoals wachtwoorden of tokens. Zorg ervoor dat alleen bevoorrechte gebruikers toegang hebben tot deze containers tijdens doorvoer en in rust. Inventariseer alle referentiegeheimen en vraag ontwikkelaars om opkomende tools voor geheimenbeheer te gebruiken die zijn ontworpen voor containerplatforms.  Zorg ervoor dat uw oplossing versleutelde databases, TLS-versleuteling voor geheimengegevens tijdens het transport en op de minste bevoegdheden [gebaseerde toegangscontrole](../role-based-access-control/overview.md)bevat. [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) is een cloudservice die versleutelingssleutels en -geheimen (zoals certificaten, verbindingstekenreeksen en wachtwoorden) voor containertoepassingen beschermt. Omdat deze gegevens gevoelig en bedrijfskritisch zijn, u veilig toegang krijgen tot uw sleutelkluizen, zodat alleen geautoriseerde toepassingen en gebruikers er toegang toe hebben.

## <a name="considerations-for-the-container-ecosystem"></a>Overwegingen voor het containerecosysteem

De volgende beveiligingsmaatregelen, goed geïmplementeerd en effectief beheerd, kunnen u helpen uw containerecosysteem te beveiligen en te beschermen. Deze maatregelen zijn van toepassing gedurende de gehele levenscyclus van de container, van ontwikkeling tot productie-implementatie en op een reeks containerorchestrators, hosts en platforms. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Gebruik kwetsbaarheidsbeheer als onderdeel van de levenscyclus van containerontwikkeling 

Door effectief kwetsbaarheidsbeheer te gebruiken gedurende de hele levenscyclus van containerontwikkeling, verbetert u de kans dat u beveiligingsproblemen identificeert en oplost voordat ze een ernstiger probleem worden. 

### <a name="scan-for-vulnerabilities"></a>Scan op kwetsbaarheden 

Nieuwe kwetsbaarheden worden ontdekt de hele tijd, dus het scannen op en identificeren van kwetsbaarheden is een continu proces. Neem kwetsbaarheidsscans op gedurende de hele levenscyclus van de container:

* Als laatste controle in uw ontwikkelingspijplijn moet u een kwetsbaarheidsscan uitvoeren op containers voordat u de afbeeldingen naar een openbaar of privéregister duwt. 
* Doorgaan met het scannen van containerafbeeldingen in het register, zowel om eventuele gebreken die op de een of andere manier werden gemist tijdens de ontwikkeling te identificeren en om nieuw ontdekte kwetsbaarheden die kunnen bestaan in de code die in de containerafbeeldingen wordt gebruikt, aan te pakken.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Afbeeldingskwetsbaarheden toewijzen aan het uitvoeren van containers 

U moet een manier hebben om kwetsbaarheden in containerafbeeldingen in kaart te brengen aan het uitvoeren van containers, zodat beveiligingsproblemen kunnen worden verholpen of opgelost.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Ervoor zorgen dat alleen goedgekeurde afbeeldingen in uw omgeving worden gebruikt 

Er is genoeg verandering en volatiliteit in een container ecosysteem zonder dat onbekende containers ook. Alleen goedgekeurde containerafbeeldingen toestaan. Beschikken over tools en processen om het gebruik van niet-goedgekeurde containerafbeeldingen te controleren en te voorkomen. 

Een effectieve manier om het aanvalsoppervlak te verminderen en te voorkomen dat ontwikkelaars kritieke beveiligingsfouten maken, is door de stroom van containerafbeeldingen in uw ontwikkelomgeving te controleren. U bijvoorbeeld een enkele Linux-distributie bestraffen als basisafbeelding, bij voorkeur een die lean is (Alpine of CoreOS in plaats van Ubuntu), om het oppervlak voor potentiële aanvallen te minimaliseren. 

Beeldondertekening of fingerprinting kan een keten van bewaring bieden waarmee u de integriteit van de containers controleren. Azure Container Registry ondersteunt bijvoorbeeld het [contenttrustmodel](https://docs.docker.com/engine/security/trust/content_trust) van Docker, waarmee uitgevers van afbeeldingen die naar een register worden gepusht, kunnen ondertekenen en beeldconsumenten alleen ondertekende afbeeldingen kunnen trekken.

### <a name="permit-only-approved-registries"></a>Alleen goedgekeurde registers toestaan 

Een uitbreiding van ervoor te zorgen dat uw omgeving gebruikt alleen goedgekeurde beelden is om alleen het gebruik van goedgekeurde container registers mogelijk te maken. Het vereisen van het gebruik van goedgekeurde containerregisters vermindert uw blootstelling aan risico's door het potentieel voor het introduceren van onbekende kwetsbaarheden of beveiligingsproblemen te beperken. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Zorg voor de integriteit van afbeeldingen gedurende de hele levenscyclus 

Een deel van het beheer van de beveiliging gedurende de gehele levenscyclus van de container is om de integriteit van de containerafbeeldingen in het register te waarborgen en wanneer ze worden gewijzigd of geïmplementeerd in productie. 

* Afbeeldingen met kwetsbaarheden, zelfs klein, mogen niet worden uitgevoerd in een productieomgeving. Idealiter moeten alle afbeeldingen die in de productie worden geïmplementeerd, worden opgeslagen in een privéregister dat toegankelijk is voor een select aantal afbeeldingen. Houd het aantal productiebeelden klein om ervoor te zorgen dat ze effectief kunnen worden beheerd.

* Omdat het moeilijk is om de oorsprong van software uit een openbaar beschikbare containerafbeelding te lokaliseren, u afbeeldingen van de bron bouwen om kennis van de oorsprong van de laag te garanderen. Wanneer er een beveiligingsprobleem aan het licht komt in een zelf gebouwde containerinstallatiekopie, kunnen klanten sneller een oplossing vinden. Met een openbare afbeelding moeten klanten de basis van een openbare afbeelding vinden om deze te repareren of een andere beveiligde afbeelding van de uitgever te krijgen. 

* Een grondig gescande afbeelding die in productie wordt geïmplementeerd, is niet gegarandeerd up-to-date voor de levensduur van de toepassing. Er kunnen beveiligingsproblemen worden gerapporteerd voor lagen van de installatiekopie die niet bekend waren of die na de implementatie naar productie zijn geïntroduceerd. 

  Controleer regelmatig afbeeldingen die in de productie zijn geïmplementeerd om afbeeldingen te identificeren die verouderd zijn of al een tijdje niet zijn bijgewerkt. U blauwgroene implementatiemethoden en rolling upgrade-mechanismen gebruiken om containerafbeeldingen bij te werken zonder downtime. U afbeeldingen scannen met behulp van gereedschappen die in de vorige sectie zijn beschreven. 

* Gebruik een CI-pijplijn (continuous integration) met geïntegreerde beveiligingscan om beveiligde afbeeldingen te bouwen en naar uw privéregister te pushen. De ingebouwde scanfunctie voor beveiligingsproblemen van de CI-oplossing zorgt ervoor dat installatiekopieën die voor alle tests slagen, naar het persoonlijke register worden gepusht van waaruit productieworkloads worden geïmplementeerd. 

  Een FOUT in de CI-pijplijn zorgt ervoor dat kwetsbare afbeeldingen niet worden gepusht naar het privéregister dat wordt gebruikt voor implementaties van productieworkloads. Het automatiseert ook het scannen van beeldbeveiliging als er een aanzienlijk aantal afbeeldingen is. Het handmatig controleren van installatiekopieën op beveiligingsproblemen kan erg langdurig en foutgevoelig zijn. 

### <a name="enforce-least-privileges-in-runtime"></a>Minste bevoegdheden afdwingen in runtime 

Het concept van de minste privileges is een basis beveiligingsbest practice die ook van toepassing is op containers. Wanneer een beveiligingslek wordt misbruikt, geeft het de aanvaller over het algemeen toegang en bevoegdheden gelijk aan die van de gecompromitteerde toepassing of proces. Ervoor zorgen dat containers werken met de laagste privileges en toegang die nodig zijn om de klus te klaren vermindert uw blootstelling aan risico's. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Het oppervlak van de containeraanval verkleinen door overbodige bevoegdheden te verwijderen 

U ook het potentiële aanvalsoppervlak minimaliseren door ongebruikte of onnodige processen of bevoegdheden uit de runtime van de container te verwijderen. Geprivilegieerde containers worden als root uitgevoerd. Als een kwaadwillende gebruiker of werkbelasting ontsnapt in een bevoorrechte container, wordt de container vervolgens uitgevoerd als root op dat systeem.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Bestanden en uitvoerbare bestanden vooraf goedkeuren die de container mag openen of uitvoeren 

Het verminderen van het aantal variabelen of onbekenden helpt u een stabiele, betrouwbare omgeving te behouden. Het beperken van containers zodat ze alleen vooraf goedgekeurde of safelisted bestanden en uitvoerbare bestanden kunnen openen of uitvoeren, is een beproefde methode om de blootstelling aan risico's te beperken.  

Het is een stuk eenvoudiger om een safelist te beheren wanneer deze vanaf het begin wordt geïmplementeerd. Een safelist biedt een mate van controle en beheerbaarheid terwijl u leert welke bestanden en uitvoerbare bestanden nodig zijn om de toepassing correct te laten functioneren. 

Een safelist vermindert niet alleen het aanvalsoppervlak, maar kan ook een basislijn bieden voor afwijkingen en voorkomen dat de use cases van de "luidruchtige buurman" en container breakout scenario's. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Netwerksegmentatie afdwingen bij het uitvoeren van containers  

Om containers in het ene subnet te beschermen tegen beveiligingsrisico's in een ander subnet, houdt u netwerksegmentatie (of nanosegmentatie) of segregatie tussen lopende containers. Het onderhouden van netwerksegmentatie kan ook nodig zijn om containers te gebruiken in industrieën die nodig zijn om te voldoen aan nalevingsmandaten.  

Zo biedt de partnertool [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) een geautomatiseerde aanpak voor nanosegmentatie. Aqua monitort containernetwerkactiviteiten in runtime. Het identificeert alle inkomende en uitgaande netwerkverbindingen van/naar andere containers, services, IP-adressen en het openbare internet. Nanosegmentatie wordt automatisch gemaakt op basis van gecontroleerd verkeer. 

### <a name="monitor-container-activity-and-user-access"></a>Containeractiviteit en gebruikerstoegang controleren 

Zoals bij elke IT-omgeving moet u de activiteit en de toegang van gebruikers tot uw containerecosysteem consistent controleren om elke verdachte of schadelijke activiteit snel te identificeren. Azure biedt oplossingen voor containerbewaking, waaronder:

* [Azure Monitor voor containers](../azure-monitor/insights/container-insights-overview.md) bewaakt de prestaties van uw workloads die zijn geïmplementeerd in Kubernetes-omgevingen die worden gehost op Azure Kubernetes Service (AKS). Azure Monitor voor containers geeft u inzicht in de prestaties door geheugen- en processorstatistieken te verzamelen van controllers, knooppunten en containers die beschikbaar zijn in Kubernetes via de Metrics API. 

* Met de [azure containermonitoringoplossing](../azure-monitor/insights/containers.md) u andere Docker- en Windows-containerhosts op één locatie weergeven en beheren. Bijvoorbeeld:

  * Bekijk gedetailleerde controle-informatie met opdrachten die worden gebruikt bij containers. 
  * Problemen oplossen door gecentraliseerde logboeken te bekijken en te doorzoeken zonder docker- of Windows-hosts op afstand te hoeven bekijken.  
  * Zoek containers die luidruchtig kunnen zijn en overtollige resources verbruiken op een host.
  * Bekijk gecentraliseerde CPU-, geheugen-, opslag- en netwerkgebruiks- en prestatiegegevens voor containers.  

  De oplossing ondersteunt containerorchestrators waaronder Docker Swarm, DC/OS, onbeheerde Kubernetes, Service Fabric en Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Activiteit containerresource controleren 

Houd uw resourceactiviteit in de gaten, zoals bestanden, netwerk en andere bronnen waartoe uw containers toegang hebben. Het monitoren van resourceactiviteit en -verbruik is nuttig, zowel voor prestatiebewaking als als beveiligingsmaatregel. 

[Azure Monitor](../azure-monitor/overview.md) maakt kernbewaking voor Azure-services mogelijk door het verzamelen van statistieken, activiteitslogboeken en diagnostische logboeken toe te staan. Het activiteitenlogboek geeft bijvoorbeeld aan wanneer nieuwe resources worden gemaakt of gewijzigd. 

  Er zijn metrische gegevens beschikbaar met prestatiestatistieken voor verschillende resources en zelfs het besturingssysteem binnen een virtuele machine. U kunt deze gegevens bekijken met een van de verkenners in de Azure-portal en waarschuwingen maken op basis van deze metrische gegevens. Azure Monitor biedt de snelste pijplijn voor metrische gegevens (vijf minuten tot één minuut); gebruik het dus voor waarschuwingen en meldingen waarbij tijd van cruciaal belang is. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Alle toegang tot containerbeheergebruikers registreren voor controle 

Houd een nauwkeurig controlespoor bij van de administratieve toegang tot uw containerecosysteem, inclusief uw Kubernetes-cluster, containerregister en containerafbeeldingen. Deze logboeken kunnen nodig zijn voor controledoeleinden en zullen nuttig zijn als forensisch bewijs na een beveiligingsincident. Azure-oplossingen zijn onder andere:

* [Integratie van Azure Kubernetes Service met Azure Security Center](../security-center/azure-kubernetes-service-integration.md) om de beveiligingsconfiguratie van de clusteromgeving te bewaken en beveiligingsaanbevelingen te genereren
* [Azure Container Monitoring-oplossing](../azure-monitor/insights/containers.md)
* Bronlogboeken voor [Azure Container Instances](container-instances-log-analytics.md) en Azure Container [Registry](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het gebruik van [Azure Security Center](../security-center/container-security.md) voor realtime bedreigingsdetectie in uw gecontaineriseerde omgevingen.

* Meer informatie over het beheren van containerkwetsbaarheden met oplossingen van [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) en [Aqua Security.](https://www.aquasec.com/solutions/azure-container-security/)