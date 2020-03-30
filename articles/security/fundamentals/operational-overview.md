---
title: Overzicht van operationele beveiliging van Azure| Microsoft Documenten
description: In dit artikel vindt u een overzicht van de operationele beveiliging van Azure.
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: bcaf45af4c02dad22e2cc611fa7ea5a32ad3a853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443883"
---
# <a name="azure-operational-security-overview"></a>Overzicht van operationele beveiliging azure

[Azure operational security](/azure/security/fundamentals/operational-security) verwijst naar de services, besturingselementen en functies die beschikbaar zijn voor gebruikers voor het beschermen van hun gegevens, toepassingen en andere elementen in Microsoft Azure. Het is een framework dat de opgedane kennis bevat die is opgedaan door een verscheidenheid aan mogelijkheden die uniek zijn voor Microsoft. Deze mogelijkheden omvatten de Microsoft Security Development Lifecycle (SDL), het Microsoft Security Response Center-programma en een diep bewustzijn van het landschap van cyberbeveiligingsbedreigingen.

## <a name="azure-management-services"></a>Azure-beheerservices

Een IT-operations team is verantwoordelijk voor het beheer van datacenterinfrastructuur, applicaties en gegevens, inclusief de stabiliteit en beveiliging van deze systemen. Het verkrijgen van beveiligingsinzichten in toenemende complexe IT-omgevingen vereist echter vaak dat organisaties gegevens uit meerdere beveiligings- en beheersystemen samenvoegen.

[Microsoft Azure Monitor-logboeken](/azure/operations-management-suite/operations-management-suite-overview) is een cloudgebaseerde IT-beheeroplossing waarmee u uw on-premises en cloudinfrastructuur beheren en beschermen. De kernfunctionaliteit wordt geleverd door de volgende services die in Azure worden uitgevoerd. Azure bevat meerdere services waarmee u uw on-premises en cloudinfrastructuur beheren en beschermen. Elke service biedt een specifieke beheerfunctie. U services combineren om verschillende beheerscenario's te realiseren. 

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](/azure/azure-monitor/overview) verzamelt gegevens uit beheerde bronnen in centrale gegevensarchieven. Deze gegevens kunnen gebeurtenissen, prestatiegegevens of aangepaste gegevens bevatten die via de API worden verstrekt. Nadat de gegevens zijn verzameld, is deze beschikbaar voor waarschuwingen, analyses en export.

U gegevens uit verschillende bronnen consolideren en gegevens uit uw Azure-services combineren met uw bestaande on-premises omgeving. Azure Monitor-logboeken scheidt ook duidelijk het verzamelen van de gegevens van de actie die op die gegevens wordt uitgevoerd, zodat alle acties beschikbaar zijn voor allerlei gegevens.

### <a name="automation"></a>Automation

[Azure Automation](/azure/automation/automation-intro) biedt u een manier om de handmatige, langlopende, foutgevoelige en vaak herhaalde taken te automatiseren die vaak worden uitgevoerd in een cloud- en bedrijfsomgeving. Het bespaart tijd en verhoogt de betrouwbaarheid van administratieve taken. Het plant zelfs deze taken automatisch worden uitgevoerd op regelmatige tijdstippen. U processen automatiseren met runbooks of configuratiebeheer automatiseren met de gewenste statusconfiguratie.

### <a name="backup"></a>Back-up

[Azure Backup](/azure/backup/backup-introduction-to-azure-backup) is de op Azure gebaseerde service die u gebruiken om een back-up te maken (of te beveiligen) en uw gegevens te herstellen in de Microsoft Cloud. Azure Backup vervangt uw bestaande on-premises of off-site back-upoplossing door een cloudgebaseerde oplossing die betrouwbaar, veilig en kostenconcurrerend is.

Azure Backup biedt onderdelen die u downloadt en implementeert op de juiste computer of server of in de cloud. Welk onderdeel, of welke agent, u implementeert, is afhankelijk van wat u wilt beveiligen. Alle Azure Backup-componenten (of u nu on-premises gegevens beschermt of in de cloud) kunnen worden gebruikt om back-ups te maken van gegevens naar een Azure Recovery Services-kluis in Azure.

Zie de tabel [Azure Backup-onderdelen](/azure/backup/backup-overview#what-can-i-back-up)voor meer informatie .

### <a name="site-recovery"></a>Site Recovery

[Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) biedt bedrijfscontinuïteit door de replicatie van on-premises virtuele en fysieke machines te orkestreren naar Azure of naar een secundaire site. Als uw primaire site niet beschikbaar is, wordt u niet naar de secundaire locatie gegaan, zodat gebruikers kunnen blijven werken. U faalt terug wanneer systemen weer in orde zijn. Gebruik Azure Security Center om intelligentere en effectievere bedreigingsdetectie uit te voeren.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](/azure/active-directory/active-directory-enable-sso-scenario) is een uitgebreide identiteitsservice die:

-   Maakt identity and access management (IAM) mogelijk als cloudservice.
-   Biedt centraal toegangsbeheer, single sign-on (SSO) en rapportage.
-   Ondersteunt geïntegreerd toegangsbeheer voor [duizenden toepassingen](https://azure.microsoft.com/marketplace/active-directory/) in azure marketplace, waaronder Salesforce, Google Apps, Box en Concur.

Azure AD bevat ook een volledige suite van mogelijkheden voor [identiteitsbeheer,](/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports)waaronder:

- [Multi-factor authentication](/azure/multi-factor-authentication/multi-factor-authentication)
- [Wachtwoordbeheer via selfservice](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Selfservice groepsbeheer](/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Geprivilegieerd accountbeheer](/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Op rollen gebaseerd toegangscontrole](/azure/role-based-access-control/overview)
- [Controle van toepassingsgebruik](/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Uitgebreide auditing](/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Bewaking en waarschuwing en waarschuwing en waarschuwingen voor beveiliging](/azure/operations-management-suite/oms-security-responding-alerts)

Met Azure Active Directory hebben alle toepassingen die u publiceert voor uw partners en klanten (bedrijf of consument) dezelfde mogelijkheden voor identiteits- en toegangsbeheer. Hierdoor u uw operationele kosten aanzienlijk verlagen.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](/azure/security-center/security-center-intro) helpt u bedreigingen te voorkomen, detecteren en erop te reageren met meer inzicht in (en controle over) de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw abonnementen. Het helpt bij het detecteren van bedreigingen die anders onopgemerkt zouden kunnen blijven, en het werkt met een breed ecosysteem van beveiligingsoplossingen.

[Bescherm vm-gegevens (virtual machine)](/azure/security-center/security-center-linux-virtual-machine) in Azure door inzicht te bieden in de beveiligingsinstellingen van uw virtuele machine en bewaking voor bedreigingen. Security Center kan uw virtuele machines controleren op:

- Beveiligingsinstellingen van het besturingssysteem met de aanbevolen configuratieregels.
- Systeembeveiliging en kritieke updates die ontbreken.
- Aanbevelingen voor endpointbescherming.
- Validatie van schijfversleuteling.
- Netwerkaanvallen.

Security Center maakt gebruik [van RBAC (Role-Based Access Control).](/azure/role-based-access-control/role-assignments-portal) RBAC biedt [ingebouwde rollen](../../role-based-access-control/built-in-roles.md) die kunnen worden toegewezen aan gebruikers, groepen en services in Azure.

Security Center beoordeelt de configuratie van uw resources om beveiligingsproblemen en kwetsbaarheden te identificeren. In Security Center ziet u informatie met betrekking tot een resource alleen wanneer u de rol van eigenaar, bijdrager of lezer toegewezen krijgt voor het abonnement of de resourcegroep waartoe een resource behoort.

>[!Note]
>Zie [Machtigingen in Azure Security Center](/azure/security-center/security-center-permissions)voor meer informatie over rollen en toegestane acties in Beveiligingscentrum.

Security Center maakt gebruik van de Microsoft Monitoring Agent. Dit is dezelfde agent die de Azure Monitor-service gebruikt. Gegevens die van deze agent worden verzameld, worden opgeslagen in een bestaande Log [Analytics-werkruimte](/azure/log-analytics/log-analytics-manage-access) die is gekoppeld aan uw Azure-abonnement of een nieuwe werkruimte, rekening houdend met de geolocatie van de VM.

## <a name="azure-monitor"></a>Azure Monitor

Prestatieproblemen in uw cloud-app kunnen van invloed zijn op uw bedrijf. Met meerdere onderling verbonden componenten en frequente releases kunnen degradaties op elk gewenst moment plaatsvinden. En als u een app ontwikkelt, ontdekken uw gebruikers meestal problemen die u niet hebt gevonden bij het testen. U moet weten over deze problemen onmiddellijk, en je moet tools voor het diagnosticeren en oplossen van de problemen.

[Azure Monitor](/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) is basistool voor het bewaken van services die op Azure worden uitgevoerd. Het geeft u gegevens op infrastructuurniveau over de doorvoer van een service en de omgeving. Als u uw apps allemaal in Azure beheert en beslist of resources moeten worden opgeschaald of omlaag wilt, is Azure Monitor de plek om te beginnen.

U ook monitoringgegevens gebruiken om diepgaande inzichten over uw toepassing te verkrijgen. Die kennis kan u helpen om de prestaties of onderhoudbaarheid van toepassingen te verbeteren, of acties te automatiseren waarvoor anders handmatige interventie nodig zou zijn.

Azure Monitor bevat de volgende onderdelen.

### <a name="azure-activity-log"></a>Azure-activiteitenlogboek

Het [Azure-activiteitenlogboek](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) biedt inzicht in de bewerkingen die zijn uitgevoerd op resources in uw abonnement. Het was voorheen bekend als "Audit Log" of "Operationeel Logboek", omdat het rapporteert control-plane gebeurtenissen voor uw abonnementen.

### <a name="azure-diagnostic-logs"></a>Diagnostische logboeken in Azure

[Azure diagnostische logboeken](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) worden uitgestoten door een resource en bieden uitgebreide, frequente gegevens over de werking van die bron. De inhoud van deze logboeken verschilt per resourcetype.

Logboeken van windows-gebeurtenissystemen zijn een categorie diagnostische logboeken voor VM's. Blob-, tabel- en wachtrijlogboeken zijn categorieën diagnostische logboeken voor opslagaccounts.

Diagnostische logboeken verschillen van het [activiteitenlogboek](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Het activiteitenlogboek geeft inzicht in de bewerkingen die zijn uitgevoerd op resources in uw abonnement. Diagnostische logboeken geven inzicht in bewerkingen die uw resource zelf heeft uitgevoerd.

### <a name="metrics"></a>Metrische gegevens

Azure Monitor biedt telemetrie die u inzicht geeft in de prestaties en status van uw workloads op Azure. Het belangrijkste type Azure-telemetriegegevens zijn de [statistieken](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (ook wel prestatiemeteritems genoemd) die door de meeste Azure-bronnen worden uitgestraald. Azure Monitor biedt verschillende manieren om deze statistieken te configureren en te gebruiken voor monitoring en probleemoplossing.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Azure Diagnostics maakt het verzamelen van diagnostische gegevens op een geïmplementeerde toepassing mogelijk. U de extensie Diagnostische gegevens uit verschillende bronnen gebruiken. Momenteel worden [azure-cloudservicerollen,](/azure/vs-azure-tools-configure-roles-for-cloud-service) [Azure-virtuele machines](/azure/vs-azure-tools-configure-roles-for-cloud-service) met Microsoft Windows en [Azure Service Fabric](/azure/monitoring-and-diagnostics/azure-diagnostics)ondersteund.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Klanten bouwen een end-to-end netwerk in Azure door afzonderlijke netwerkbronnen te orkestreren en samen te stellen, zoals virtuele netwerken, Azure ExpressRoute, Azure Application Gateway en load balancers. Monitoring is beschikbaar op elk van de netwerkbronnen.

Het end-to-end netwerk kan complexe configuraties en interacties tussen resources hebben. Het resultaat zijn complexe scenario's die op scenario's gebaseerde bewaking via [Azure Network Watcher](/azure/network-watcher/network-watcher-monitoring-overview)nodig hebben.

Network Watcher vereenvoudigt het bewaken en diagnosticeren van uw Azure-netwerk. U de diagnostische en visualisatietools in Network Watcher gebruiken om:

- Neem externe pakketopnamen op een virtuele Azure-machine.
- Krijg inzicht in uw netwerkverkeer door flowlogs te gebruiken.
- Diagnose van Azure VPN Gateway en verbindingen.

Network Watcher heeft momenteel de volgende mogelijkheden:

- [Topologie](/azure/network-watcher/network-watcher-topology-overview): geeft een overzicht van de verschillende interconnecties en koppelingen tussen netwerkbronnen in een resourcegroep.
- [Variabele pakketopname:](/azure/network-watcher/network-watcher-packet-capture-overview)legt pakketgegevens vast in en uit een virtuele machine. Geavanceerde filteropties en verfijnde besturingselementen, zoals de mogelijkheid om tijd- en groottebeperkingen in te stellen, bieden flexibiliteit. De pakketgegevens kunnen worden opgeslagen in een blob-winkel of op de lokale schijf in .cap-indeling.
- [IP-stroomverificatie:](/azure/network-watcher/network-watcher-ip-flow-verify-overview)controleert of een pakket is toegestaan of geweigerd op basis van pakketparameters van 5 tuple voor stroominformatie (doel-IP, bron-IP, doelpoort, bronpoort en protocol). Als een beveiligingsgroep het pakket weigert, worden de regel en groep die het pakket heeft geweigerd, geretourneerd.
- [Volgende hop:](/azure/network-watcher/network-watcher-next-hop-overview)hiermee bepaalt u de volgende hop voor pakketten die worden gerouteerd in de Azure-netwerkstructuur, zodat u verkeerd geconfigureerde door de gebruiker gedefinieerde routes diagnosticeren.
- [Weergave Beveiligingsgroep:](/azure/network-watcher/network-watcher-security-group-view-overview)de effectieve en toegepaste beveiligingsregels die op een vm worden toegepast.
- [NSG-stroomlogboeken voor netwerkbeveiligingsgroepen:](/azure/network-watcher/network-watcher-nsg-flow-logging-overview)U logboeken vastleggen die betrekking hebben op verkeer dat is toegestaan of geweigerd door de beveiligingsregels in de groep. De stroom wordt gedefinieerd door 5-tuple-informatie: bron-IP, doel-IP, bronpoort, doelpoort en protocol.
- [Probleemoplossing voor virtuele netwerkgateways en verbindingsproblemen:](/azure/network-watcher/network-watcher-troubleshoot-manage-rest)biedt de mogelijkheid om problemen op te lossen met virtuele netwerkgateways en -verbindingen.
- [Limieten voor netwerkabonnementen:](/azure/network-watcher/network-watcher-monitoring-overview)hiermee u het gebruik van netwerkbronnen bekijken tegen limieten.
- [Diagnostische logboeken:](/azure/network-watcher/network-watcher-monitoring-overview)biedt één deelvenster om diagnostische logboeken voor netwerkbronnen in een resourcegroep in te schakelen of uit te schakelen.

Zie [Netwerkwatcher configureren](/azure/network-watcher/network-watcher-create)voor meer informatie.

## <a name="cloud-service-provider-access-transparency"></a>Transparantie over toegang tot cloudserviceprovider

[Customer Lockbox voor Microsoft Azure](customer-lockbox-overview.md) is een service die is geïntegreerd in Azure-portal en die u expliciete controle geeft in het zeldzame geval wanneer een Microsoft Support Engineer mogelijk toegang tot uw gegevens nodig heeft om een probleem op te lossen.
Er zijn zeer weinig gevallen, zoals een probleem met het opsporen van externe toegang, waarbij een Microsoft Support Engineer verhoogde machtigingen nodig heeft om dit probleem op te lossen. In dergelijke gevallen gebruiken Microsoft-technici just-in-time toegangsservice die een beperkte, tijdgebonden autorisatie biedt met toegang tot de service.  
Hoewel Microsoft altijd toestemming van de klant heeft verkregen voor toegang, biedt Customer Lockbox u nu de mogelijkheid om dergelijke verzoeken van de Azure Portal te controleren en goed te keuren of te weigeren. Ondersteuningstechnici van Microsoft krijgen pas toegang als u het verzoek goedkeurt.

## <a name="standardized-and-compliant-deployments"></a>Gestandaardiseerde en compatibele implementaties

[Azure Blueprints](/azure/governance/blueprints/overview) stellen cloudarchitecten en centrale informatietechnologiegroepen in staat om een herhaalbare set Azure-resources te definiëren die de standaarden, patronen en vereisten van een organisatie implementeren en naleven.  
Dit maakt het mogelijk voor DevOps-teams om snel nieuwe omgevingen te bouwen en op te staan en erop te vertrouwen dat ze ze bouwen met infrastructuur die de naleving van de organisatie handhaaft.
Blauwdrukken bieden een declaratieve manier om de implementatie van verschillende resourcesjablonen en andere artefacten te orkestreren, zoals:

- Roltoewijzingen
- Beleidstoewijzingen
- Azure Resource Manager-sjablonen
- Resourcegroepen

## <a name="devops"></a>DevOps

Voordat [Developer Operations (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) applicatie ontwikkeling, teams waren belast met het verzamelen van zakelijke eisen voor een software-programma en het schrijven van code. Vervolgens testte een apart QA-team het programma in een geïsoleerde ontwikkelomgeving. Als aan de vereisten is voldaan, heeft het QA-team de code vrijgegeven voor bewerkingen die moeten worden geïmplementeerd. De implementatieteams werden verder gefragmenteerd in groepen zoals netwerken en database. Elke keer dat een softwareprogramma werd "gegooid over de muur" om een onafhankelijk team, het toegevoegd knelpunten.

DevOps stelt teams in staat om sneller en goedkoper veiligere oplossingen van hogere kwaliteit te leveren. Klanten verwachten een dynamische en betrouwbare ervaring bij het consumeren van software en diensten. Teams moeten snel herhalen op software-updates en de impact van de updates te meten. Ze moeten snel reageren met nieuwe ontwikkelingsiteraties om problemen aan te pakken of meer waarde te bieden.  

Cloudplatforms zoals Microsoft Azure hebben traditionele knelpunten verwijderd en de infrastructuur helpen commoditiseren. Software regeert in elk bedrijf als de belangrijkste differentiator en factor in de zakelijke resultaten. Geen enkele organisatie, ontwikkelaar of IT-medewerker kan of moet de DevOps-beweging vermijden.

Volwassen DevOps beoefenaars nemen een aantal van de volgende praktijken. Bij deze praktijken [worden mensen betrokken](https://www.visualstudio.com/learn/what-is-devops-culture/) om strategieën te vormen op basis van de bedrijfsscenario's. Tooling kan helpen bij het automatiseren van de verschillende praktijken.

- [Agile planning- en projectmanagementtechnieken](https://www.visualstudio.com/learn/what-is-agile/) worden gebruikt om werk te plannen en te isoleren in sprints, teamcapaciteit te beheren en teams te helpen zich snel aan te passen aan veranderende bedrijfsbehoeften.
- [Versiebeheer, meestal met Git,](https://www.visualstudio.com/learn/what-is-git/)stelt teams die zich overal ter wereld bevinden in staat om bron te delen en te integreren met softwareontwikkelingstools om de releasepijplijn te automatiseren.
- [Continue integratie](https://www.visualstudio.com/learn/what-is-continuous-integration/) stimuleert het voortdurend samenvoegen en testen van code, wat leidt tot het vroegtijdig vinden van defecten.  Andere voordelen zijn minder tijd verspild aan het bestrijden van merge-problemen en snelle feedback voor ontwikkelteams.
- [Continue levering](https://www.visualstudio.com/learn/what-is-continuous-delivery/) van softwareoplossingen aan productie- en testomgevingen helpt organisaties om bugs snel op te lossen en in te spelen op steeds veranderende bedrijfsvereisten.
- [Monitoring](https://www.visualstudio.com/learn/what-is-monitoring/) van lopende toepassingen - inclusief productieomgevingen voor toepassingsstatus, evenals klantgebruik - helpt organisaties een hypothese te vormen en snel strategieën te valideren of te weerleggen.  Rijke gegevens worden vastgelegd en opgeslagen in verschillende logging-indelingen.
- [Infrastructure as Code (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) is een praktijk die het mogelijk maakt de automatisering en validatie van het creëren en afbreken van netwerken en virtuele machines om te helpen bij het leveren van veilige, stabiele applicatie hosting platforms.
- [Microservices-architectuur](https://www.visualstudio.com/learn/what-are-microservices/) wordt gebruikt om business use cases te isoleren in kleine herbruikbare services.  Deze architectuur maakt schaalbaarheid en efficiëntie mogelijk.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over de oplossing voor beveiliging en controle:

- [Beveiliging en naleving](https://azure.microsoft.com/overview/trusted-cloud/)
- [Azure Security Center](/azure/security-center/security-center-intro)
- [Azure Monitor](/azure/azure-monitor/overview)
