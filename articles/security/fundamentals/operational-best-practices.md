---
title: Aanbevolen procedures voor beveiliging voor uw Azure-assets
titleSuffix: Azure security
description: In dit artikel vindt u een reeks operationele aanbevolen procedures voor het beveiligen van uw gegevens, toepassingen en andere elementen in Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 72d7a2dd112e5e7a5105ff977e3917ccdfd7b53e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500290"
---
# <a name="azure-operational-security-best-practices"></a>Aanbevolen procedures voor Azure Operational Security
In dit artikel vindt u een reeks operationele aanbevolen procedures voor het beveiligen van uw gegevens, toepassingen en andere elementen in Azure.

De aanbevolen procedures zijn gebaseerd op een consensus van mening en werken met de huidige Azure-platformmogelijkheden en functiesets. Meningen en technologieën veranderen in de loop van de tijd en dit artikel wordt regelmatig bijgewerkt om deze veranderingen weer te geven.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Sterke operationele beveiligingspraktijken definiëren en implementeren
Azure operational security verwijst naar de services, besturingselementen en functies die beschikbaar zijn voor gebruikers voor het beschermen van hun gegevens, toepassingen en andere elementen in Azure. Azure operational security is gebouwd op een framework dat de opgedane kennis bevat die is opgedaan door mogelijkheden die uniek zijn voor Microsoft, waaronder de [Security Development Lifecycle (SDL),](https://www.microsoft.com/sdl)het [Microsoft Security Response Center-programma](https://www.microsoft.com/msrc?rtc=1) en een diep bewustzijn van het landschap van cyberbeveiligingsbedreigingen.

## <a name="manage-and-monitor-user-passwords"></a>Gebruikerswachtwoorden beheren en bewaken
In de volgende tabel worden enkele aanbevolen procedures weergegeven met betrekking tot het beheren van gebruikerswachtwoorden:

**Aanbevolen procedures**: Zorg ervoor dat u het juiste niveau van wachtwoordbeveiliging in de cloud hebt.   
**Detail:** Volg de richtlijnen in [Microsoft Password Guidance](https://www.microsoft.com/research/publication/password-guidance/), die wordt uitgebreid met gebruikers van de Microsoft-identiteitsplatforms (Azure Active Directory, Active Directory en Microsoft-account).

**Aanbevolen procedures:** controleer op verdachte acties met betrekking tot uw gebruikersaccounts.   
**Detail:** Controleer voor [gebruikers met risico's](/azure/active-directory/reports-monitoring/concept-user-at-risk) en [risicovolle aanmeldingen](../../active-directory/reports-monitoring/concept-risk-events.md) met Azure AD-beveiligingsrapporten.

**Aanbevolen procedures**: Wachtwoorden met een hoog risico automatisch detecteren en herstellen.   
**Detail:** [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview) is een functie van de Azure AD Premium P2-editie waarmee u:

- Mogelijke kwetsbaarheden detecteren die van invloed zijn op de identiteit van uw organisatie
- Geautomatiseerde antwoorden configureren op gedetecteerde verdachte acties die gerelateerd zijn aan de identiteit van uw organisatie
- Onderzoek verdachte incidenten en onderneem passende maatregelen om deze op te lossen

## <a name="receive-incident-notifications-from-microsoft"></a>Incidentmeldingen van Microsoft ontvangen
Zorg ervoor dat uw beveiligingsteam Azure-incidentmeldingen van Microsoft ontvangt. Een incidentmelding laat uw beveiligingsteam weten dat u Azure-bronnen hebt gecompromitteerd, zodat ze snel kunnen reageren op potentiële beveiligingsrisico's en deze kunnen herstellen.

In de Azure-inschrijvingsportal u ervoor zorgen dat beheerderscontactgegevens gegevens bevatten die beveiligingsbewerkingen melden. Contactgegevens zijn een e-mailadres en telefoonnummer.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Azure-abonnementen ordenen in beheergroepen
Als uw organisatie veel abonnementen heeft, wilt u mogelijk de toegang, het beleid en de naleving voor die abonnementen op efficiënte wijze beheren. [Azure-beheergroepen](/azure/governance/management-groups/create) bieden een scopeniveau dat hoger is dan abonnementen. Je organiseert abonnementen in containers genaamd managementgroepen en past je governancevoorwaarden toe op de beheergroepen. Alle abonnementen in een beheergroep nemen automatisch de voorwaarden over die op de beheergroep zijn toegepast.

U een flexibele structuur van beheergroepen en abonnementen in een map inbouwen. Elke map krijgt één beheergroep op het hoogste niveau, de hoofdbeheergroep. Deze hoofdbeheergroep is zo in de hiërarchie ingebouwd dat alle beheergroepen en abonnementen hierin zijn opgevouwen. Met de rootmanagementgroep kunnen globale beleidsregels en RBAC-toewijzingen worden toegepast op directoryniveau.

Hier volgen enkele aanbevolen procedures voor het gebruik van beheergroepen:

**Aanbevolen procedures**: zorg ervoor dat nieuwe abonnementen governance-elementen toepassen, zoals beleidsregels en machtigingen wanneer ze worden toegevoegd.   
**Detail:** Gebruik de hoofdbeheergroep om bedrijfsbrede beveiligingselementen toe te wijzen die van toepassing zijn op alle Azure-assets. Beleid en machtigingen zijn voorbeelden van elementen.

**Aanbevolen procedures**: Stem de hoogste niveaus van beheergroepen af op de segmentatiestrategie om een punt te bieden voor controle en beleidsconsistentie binnen elk segment.   
**Detail:** Maak één beheergroep voor elk segment onder de hoofdbeheergroep. Maak geen andere beheergroepen onder de hoofdmap.

**Aanbevolen procedures**: Beperk de groepsdiepte van het beheer om verwarring te voorkomen die zowel operaties als beveiliging belemmert.   
**Detail:** Beperk uw hiërarchie tot drie niveaus, waaronder de hoofdmap.

**Aanbevolen procedures**: Selecteer zorgvuldig welke artikelen van toepassing zijn op de gehele onderneming met de rootmanagementgroep.   
**Detail:** Zorg ervoor dat rootmanagementgroepelementen een duidelijke behoefte hebben om voor elke resource te worden toegepast en dat ze weinig impact hebben.

Goede kandidaten zijn:

- Wettelijke vereisten die een duidelijke zakelijke impact hebben (bijvoorbeeld beperkingen met betrekking tot gegevenssoevereiniteit)
- Vereisten met bijna nul potentiële negatieve gevolgen voor bewerkingen, zoals beleid met auditeffect of RBAC-machtigingstoewijzingen die zorgvuldig zijn herzien

**Aanbevolen procedures**: Plan en test zorgvuldig alle bedrijfsbrede wijzigingen op de rootmanagementgroep voordat u deze toepast (beleid, RBAC-model, enzovoort).   
**Detail:** Wijzigingen in de hoofdbeheergroep kunnen van invloed zijn op elke bron op Azure. Hoewel ze een krachtige manier bieden om consistentie in de hele onderneming te garanderen, kunnen fouten of onjuist gebruik een negatieve invloed hebben op de productieactiviteiten. Test alle wijzigingen in de root management groep in een testlab of productiepilot.

## <a name="streamline-environment-creation-with-blueprints"></a>Het creëren van omgevingen stroomlijnen met blauwdrukken
[Met de Azure Blueprints-service](/azure/governance/blueprints/overview) kunnen cloudarchitecten en centrale informatietechnologiegroepen een herhaalbare set Azure-resources definiëren die de standaarden, patronen en vereisten van een organisatie implementeert en naleeft. Azure Blueprints maakt het mogelijk voor ontwikkelteams om snel nieuwe omgevingen te bouwen en op te staan met een reeks ingebouwde componenten en het vertrouwen dat ze die omgevingen creëren binnen de naleving van de organisatie.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Opslagservices controleren op onverwachte gedragsveranderingen
Het diagnosticeren en oplossen van problemen in een gedistribueerde toepassing die wordt gehost in een cloudomgeving, kan complexer zijn dan in traditionele omgevingen. Toepassingen kunnen worden geïmplementeerd in een PaaS- of IaaS-infrastructuur, on-premises, op een mobiel apparaat of in een combinatie van deze omgevingen. Het netwerkverkeer van uw toepassing kan openbare en particuliere netwerken doorkruisen en uw toepassing kan meerdere opslagtechnologieën gebruiken.

U moet continu de opslagservices controleren die uw toepassing gebruikt voor onverwachte gedragswijzigingen (zoals tragere responstijden). Gebruik logboekregistratie om meer gedetailleerde gegevens te verzamelen en een probleem diepgaand te analyseren. De diagnostische informatie die u verkrijgt van zowel monitoring als logging helpt u om de hoofdoorzaak van het probleem te bepalen dat uw toepassing heeft ondervonden. Vervolgens u het probleem oplossen en de juiste stappen bepalen om het probleem te verhelpen.

[Azure Storage Analytics](../../storage/common/storage-analytics.md) voert logboekregistratie uit en biedt metrische gegevens voor een Azure-opslagaccount. We raden u aan deze gegevens te gebruiken om verzoeken te traceren, gebruikstrends te analyseren en problemen met uw opslagaccount te diagnosticeren.

## <a name="prevent-detect-and-respond-to-threats"></a>Bedreigingen voorkomen, detecteren en erop reageren
[Azure Security Center](../../security-center/security-center-intro.md) helpt u bedreigingen te voorkomen, te detecteren en erop te reageren door meer inzicht te bieden in (en) de beveiliging van uw Azure-resources te beheren. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden kunnen blijven en werkt met verschillende beveiligingsoplossingen.

De gratis laag van Security Center biedt beperkte beveiliging voor alleen uw Azure-bronnen. De standaardlaag breidt deze mogelijkheden uit naar on-premises en andere clouds. Security Center Standard helpt u bij het opsporen en oplossen van beveiligingsproblemen, het toepassen van toegangs- en toepassingsbesturingselementen om schadelijke activiteiten te blokkeren, bedreigingen te detecteren met behulp van analyses en intelligentie en snel te reageren wanneer deze wordt aangevallen. U kunt Security Center Standard de eerste 60 dagen kosteloos proberen. We raden u aan [uw Azure-abonnement te upgraden naar Security Center Standard.](../../security-center/security-center-get-started.md)

Gebruik Beveiligingscentrum om een centrale weergave te krijgen van de beveiligingsstatus van al uw Azure-bronnen. Controleer in één oogopslag of de juiste beveiligingsbesturingselementen zijn ingesteld en correct zijn geconfigureerd en identificeer snel alle resources die aandacht nodig hebben.

Security Center integreert ook met [Microsoft Defender Advanced Threat Protection (ATP),](../../security-center/security-center-wdatp.md)die uitgebreide Endpoint Detection and Response (EDR) mogelijkheden biedt. Met microsoft Defender ATP-integratie u afwijkingen herkennen. U ook geavanceerde aanvallen op servereindpunten detecteren en erop reageren die door Security Center worden gecontroleerd.

Bijna alle bedrijfsorganisaties beschikken over een SIEM-systeem (Security Information and Event Management) om nieuwe bedreigingen te identificeren door logboekinformatie van diverse signaalverzamelapparaten te consolideren. De logboeken worden vervolgens geanalyseerd door een data-analysesysteem om te helpen identificeren wat "interessant" is van het geluid dat onvermijdelijk is in alle oplossingen voor het verzamelen en analyseren van logboeken.

[Azure Sentinel](/azure/sentinel/overview) is een schaalbare, cloud-native SIEM-oplossing (Security Information and Event Management) en security orchestration automated response (SOAR). Azure Sentinel biedt intelligente beveiligingsanalyses en bedreigingsinformatie via waarschuwingsdetectie, zichtbaarheid van bedreigingen, proactieve jacht en geautomatiseerde bedreigingsrespons.

Hier volgen enkele aanbevolen procedures voor het voorkomen, detecteren en reageren op bedreigingen:

**Aanbevolen procedures**: Verhoog de snelheid en schaalbaarheid van uw SIEM-oplossing met behulp van een siem in de cloud.   
**Detail:** Onderzoek de functies en mogelijkheden van [Azure Sentinel](/azure/sentinel/overview) en vergelijk ze met de mogelijkheden van wat u momenteel on-premises gebruikt. Overweeg Azure Sentinel te gebruiken als deze voldoet aan de SIEM-vereisten van uw organisatie.

**Aanbevolen procedures**: Zoek de ernstigste beveiligingsproblemen, zodat u prioriteit geven aan onderzoek.   
**Detail:** controleer uw [azure secure score](../../security-center/security-center-secure-score.md) om de aanbevelingen te zien die voortvloeien uit het Azure-beleid en de initiatieven die zijn ingebouwd in Azure Security Center. Deze aanbevelingen helpen bij het aanpakken van toprisico's zoals beveiligingsupdates, endpoint-beveiliging, versleuteling, beveiligingsconfiguraties, ontbrekende WAF, vm's met internetverbinding en nog veel meer.

Met de beveiligde score, die is gebaseerd op CIS-besturingselementen (Center for Internet Security), u de Azure-beveiliging van uw organisatie benchmarken ten opzichte van externe bronnen. Externe validatie helpt bij het valideren en verrijken van de beveiligingsstrategie van uw team.

**Aanbevolen procedures**: Controleer de beveiligingshouding van machines, netwerken, opslag- en gegevensservices en toepassingen om potentiële beveiligingsproblemen te ontdekken en te prioriteren.  
**Detail:** Volg de [beveiligingsaanbevelingen](../../security-center/security-center-recommendations.md) in Security Center vanaf het begin, met de hoogste prioriteit items.

**Aanbevolen procedures**: Integreer waarschuwingen voor beveiligingspersoneel in uw siem-oplossing (security information and event management).   
**Detail**: De meeste organisaties met een SIEM gebruiken het als een centraal clearinghouse voor beveiligingswaarschuwingen die een reactie van analisten vereisen. Verwerkte gebeurtenissen die zijn geproduceerd door Security Center, worden gepubliceerd in het Azure Activity Log, een van de logboeken die beschikbaar zijn via Azure Monitor. Azure Monitor biedt een geconsolideerde pijplijn voor het routeren van uw bewakingsgegevens naar een SIEM-tool. Zie [Beveiligingsoplossingen integreren in Security Center](../../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) voor instructies. Zie [Azure Security Center verbinden](../../sentinel/connect-azure-security-center.md)als u Azure Sentinel gebruikt.

**Aanbevolen procedures**: Integreer Azure-logboeken met uw SIEM.   
**Detail:** Gebruik [Azure Monitor om gegevens te verzamelen en te exporteren.](/azure/azure-monitor/overview#integrate-and-export-data) Deze praktijk is van cruciaal belang voor het inschakelen van beveiligingsincidenten en het bewaren van onlinelogboeken is beperkt. Zie [Gegevensbronnen verbinden](../../sentinel/connect-data-sources.md)als u Azure Sentinel gebruikt.

**Aanbevolen procedures**: Versnel uw onderzoeks- en jachtprocessen en verminder false positives door endpoint detection and response (EDR) te integreren in uw aanvalsonderzoek.   
**Detail:** [Schakel Microsoft Defender ATP-integratie in](../../security-center/security-center-wdatp.md#enable-microsoft-defender-atp-integration) via uw beveiligingsbeleid voor beveiligingscentrum. Overweeg Azure Sentinel te gebruiken voor bedreigingsjacht en incidentrespons.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>End-to-end-netwerkbewaking op basis van scenario's bewaken
Klanten bouwen een end-to-end netwerk in Azure door netwerkbronnen zoals een virtueel netwerk, ExpressRoute, Application Gateway en load balancers te combineren. Monitoring is beschikbaar op elk van de netwerkbronnen.

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) is een regionale service. Gebruik de diagnostische en visualisatietools om de omstandigheden op netwerkscenarioniveau in, naar en vanuit Azure te controleren en te diagnosticeren.

Hieronder volgen best practices voor netwerkbewaking en beschikbare tools.

**Aanbevolen procedures**: Automatiseer externe netwerkbewaking met packet capture.  
**Detail:** Monitor en diagnosticeer netwerkproblemen zonder in te loggen op uw VM's met Behulp van Network Watcher. Activeer [het vastleggen van pakketten](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) door waarschuwingen in te stellen en toegang te krijgen tot realtime prestatie-informatie op pakketniveau. Wanneer u een probleem ziet, u in detail onderzoeken of u betere diagnoses stellen.

**Aanbevolen procedures**: Krijg inzicht in uw netwerkverkeer met behulp van stroomlogboeken.  
**Detail:** Bouw een beter begrip van uw netwerkverkeerspatronen met behulp van [netwerkbeveiligingsgroepstroomlogboeken.](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) Met informatie in stroomlogboeken u gegevens verzamelen voor naleving, controle en bewaking van uw netwerkbeveiligingsprofiel.

**Aanbevolen procedures**: Diagnose vpn-connectiviteit problemen.  
**Detail:** Gebruik Network Watcher om [uw meest voorkomende VPN-gateway- en verbindingsproblemen te diagnosticeren.](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md) U niet alleen het probleem identificeren, maar ook gedetailleerde logboeken gebruiken om verder te onderzoeken.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Veilige implementatie met behulp van beproefde DevOps-tools
Gebruik de volgende devOps-aanbevolen procedures om ervoor te zorgen dat uw onderneming en teams productief en efficiënt zijn.

**Aanbevolen procedures**: Automatiseer de build en implementatie van services.  
**Detail**: [Infrastructuur als code](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) is een reeks technieken en praktijken die IT-professionals helpen de last van de dagelijkse bouw en het beheer van modulaire infrastructuur te verlichten. Het stelt IT-professionals in staat om hun moderne serveromgeving te bouwen en te onderhouden op een manier die vergelijkbaar is met hoe softwareontwikkelaars applicatiecode bouwen en onderhouden.

U [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) gebruiken om uw toepassingen in te richten met behulp van een declaratieve sjabloon. U kunt in één enkele sjabloon meerdere services plus de bijbehorende afhankelijkheden implementeren. U gebruikt dezelfde sjabloon om uw toepassing herhaaldelijk te implementeren in elke fase van de levenscyclus van de toepassing.

**Aanbevolen procedures**: Automatisch bouwen en implementeren voor Azure-webapps of cloudservices.  
**Detail:** U uw Azure DevOps-projecten configureren om automatisch te [bouwen en te implementeren in](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) Azure-webapps of cloudservices. Azure DevOps implementeert de binaire bestanden automatisch na het maken van een build naar Azure na elke codecheck-in. Het proces voor het bouwen van pakketten is gelijk aan de opdracht Pakket in Visual Studio en de publicatiestappen zijn gelijk aan de opdracht Publiceren in Visual Studio.

**Aanbevolen procedures**: Automatiseer releasemanagement.  
**Detail:** [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) is een oplossing voor het automatiseren van implementatie in meerdere fasen en het beheren van het releaseproces. Maak beheerde pijplijnen voor continue implementatie om snel, eenvoudig en vaak vrij te geven. Met Azure Pipelines u uw releaseproces automatiseren en u vooraf gedefinieerde goedkeuringswerkstromen hebben. Implementeer on-premises en naar de cloud, breid uit en pas deze naar behoefte uit.

**Aanbevolen procedures**: controleer de prestaties van uw app voordat u deze start of implementeer updates voor de productie.  
**Detail:** Voer cloudgebaseerde [belastingstests](/azure/devops/test/load-test/overview#alternatives) uit om:

- Zoek prestatieproblemen in uw app.
- Verbeter de implementatiekwaliteit.
- Zorg ervoor dat uw app altijd beschikbaar is.
- Zorg ervoor dat uw app verkeer kan verwerken voor uw volgende lancerings- of marketingcampagne.

[Apache JMeter](https://jmeter.apache.org/) is een gratis, populaire open source tool met een sterke community backing.

**Aanbevolen procedures**: Monitor de prestaties van toepassingen.  
**Detail:** [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) is een uitbreidbare APM-service (Application Performance Management) voor webontwikkelaars op meerdere platforms. Gebruik Application Insights om uw live webapplicatie te controleren. Het detecteert automatisch prestatieafwijkingen. Het bevat analysetools om u te helpen problemen te diagnosticeren en te begrijpen wat gebruikers daadwerkelijk met uw app doen. Het is bedoeld om u te helpen de prestaties en bruikbaarheid continu te verbeteren.

## <a name="mitigate-and-protect-against-ddos"></a>Beperken en beschermen tegen DDoS
Distributed denial of service (DDoS) is een type aanval dat toepassingsbronnen probeert uit te putten. Het doel is om de beschikbaarheid van de toepassing en de mogelijkheid om legitieme verzoeken te behandelen beïnvloeden. Deze aanvallen worden steeds geavanceerder en groter in omvang en impact. Ze kunnen worden gericht op elk eindpunt dat openbaar bereikbaar is via het internet.

Ontwerpen en bouwen voor DDoS-tolerantie vereist planning en ontwerp voor verschillende foutmodi. Hieronder volgen de aanbevolen procedures voor het bouwen van DDoS-robuuste services op Azure.

**Aanbevolen procedures**: Zorg ervoor dat beveiliging een prioriteit is gedurende de gehele levenscyclus van een toepassing, van ontwerp en implementatie tot implementatie en bewerkingen. Toepassingen kunnen bugs hebben die een relatief laag aantal aanvragen mogelijk maken om veel resources te gebruiken, wat resulteert in een servicestoring.  
**Detail:** Om een service die op Microsoft Azure wordt uitgevoerd te beschermen, moet u een goed begrip hebben van uw toepassingsarchitectuur en zich richten op de [vijf pijlers van softwarekwaliteit.](https://docs.microsoft.com/azure/architecture/guide/pillars) U moet typische verkeersvolumes kennen, het connectiviteitsmodel tussen de toepassing en andere toepassingen en de serviceeindpunten die worden blootgesteld aan het openbare internet.

Ervoor zorgen dat een toepassing veerkrachtig genoeg is om een denial of service te verwerken die is gericht op de toepassing zelf, is het belangrijkst. Beveiliging en privacy zijn ingebouwd in het Azure-platform, te beginnen met de [Security Development Lifecycle (SDL).](https://www.microsoft.com/sdl) De SDL richt zich op beveiliging in elke ontwikkelingsfase en zorgt ervoor dat Azure voortdurend wordt bijgewerkt om het nog veiliger te maken.

**Aanbevolen procedures**: Ontwerp uw toepassingen [horizontaal](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) om te voldoen aan de vraag naar een versterkte belasting, met name in het geval van een DDoS-aanval. Als uw toepassing afhankelijk is van één exemplaar van een service, wordt één storingspunt veroorzaakt. Door meerdere exemplaren in te richten, wordt uw systeem veerkrachtiger en schaalbaarder.  
**Detail:** Selecteer voor [Azure App Service](/azure/app-service/app-service-value-prop-what-is)een App [Service-abonnement](../../app-service/overview-hosting-plans.md) dat meerdere exemplaren biedt.

Configureer voor Azure Cloud Services elk van uw rollen om [meerdere exemplaren](../../cloud-services/cloud-services-choose-me.md)te gebruiken.

Voor [Azure Virtual Machines](/azure/virtual-machines/windows/overview)moet u ervoor zorgen dat uw VM-architectuur meer dan één VM bevat en dat elke vm is opgenomen in een [beschikbaarheidsset.](/azure/virtual-machines/virtual-machines-windows-manage-availability) We raden u aan virtuele machineschaalsets te gebruiken voor automatische schaling.

**Aanbevolen procedures**: Gelaagdheid beveiliging verdediging in een toepassing vermindert de kans op een succesvolle aanval. Implementeer veilige ontwerpen voor uw toepassingen met behulp van de ingebouwde mogelijkheden van het Azure-platform.  
**Detail**: Het risico op een aanval neemt toe met de grootte (oppervlakte) van de toepassing. U het oppervlak verkleinen door whitelisting te gebruiken om de blootgestelde IP-adresruimte en luisterpoorten te sluiten die niet nodig zijn op de load balancers[(Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) en [Azure Application Gateway).](/azure/application-gateway/application-gateway-create-probe-portal)

[Netwerkbeveiligingsgroepen](../../virtual-network/security-overview.md) zijn een andere manier om het aanvalsoppervlak te verkleinen. U [servicetags](../../virtual-network/security-overview.md#service-tags) en [toepassingsbeveiligingsgroepen](../../virtual-network/security-overview.md#application-security-groups) gebruiken om de complexiteit voor het maken van beveiligingsregels en het configureren van netwerkbeveiliging te minimaliseren, als een natuurlijke uitbreiding van de structuur van een toepassing.

U moet Azure-services waar mogelijk implementeren in een [virtueel netwerk.](../../virtual-network/virtual-networks-overview.md) Deze praktijk stelt serviceresources in staat om te communiceren via privé IP-adressen. Azure-serviceverkeer vanuit een virtueel netwerk gebruikt standaard openbare IP-adressen als bron-IP-adressen.

Met [serviceeindpunten](../../virtual-network/virtual-network-service-endpoints-overview.md) schakelt serviceverkeer over om privéadressen van virtuele netwerken te gebruiken als bron-IP-adressen wanneer ze de Azure-service vanuit een virtueel netwerk openen.

We zien vaak dat de on-premises bronnen van klanten worden aangevallen, samen met hun resources in Azure. Als u een on-premises omgeving met Azure verbindt, minimaliseert u de blootstelling van on-premises bronnen op het openbare internet.

Azure heeft twee [DDoS-serviceaanbiedingen](../../virtual-network/ddos-protection-overview.md) die bescherming bieden tegen netwerkaanvallen:

- Basisbeveiliging is standaard geïntegreerd in Azure zonder extra kosten. De schaal en capaciteit van het wereldwijd geïmplementeerde Azure-netwerk biedt verdediging tegen veelvoorkomende netwerklaagaanvallen door middel van always-on verkeersbewaking en real-time mitigatie. Basic vereist geen wijzigingen in de gebruikersconfiguratie of toepassing en helpt alle Azure-services te beschermen, inclusief PaaS-services zoals Azure DNS.
- Standaardbescherming biedt geavanceerde DDoS-mitigatiemogelijkheden tegen netwerkaanvallen. Het wordt automatisch afgestemd om uw specifieke Azure-bronnen te beschermen. Bescherming is eenvoudig mogelijk te maken tijdens het maken van virtuele netwerken. Het kan ook worden gedaan na het maken en vereist geen toepassing of resource wijzigingen.

## <a name="enable-azure-policy"></a>Azure-beleid inschakelen
[Azure Policy](/azure/governance/policy/overview) is een service in Azure die u gebruikt om beleid te maken, toe te wijzen en te beheren. Met dit beleid worden regels en effecten voor uw resources afgedwongen, zodat deze resources in overeenstemming blijven met uw bedrijfsstandaarden en serviceovereenkomsten. Azure Policy voorziet in deze behoefte door uw resources met toegewezen beleid te controleren op niet-naleving.

Azure-beleid inschakelen om het geschreven beleid van uw organisatie te controleren en af te dwingen. Dit zorgt ervoor dat aan uw bedrijf of wettelijke beveiligingsvereisten wordt voldaan door het beveiligingsbeleid centraal te beheren voor uw hybride cloudworkloads. Meer informatie over het [maken en beheren van beleid om naleving af te dwingen](../../governance/policy/tutorials/create-and-manage.md). Zie [Azure Policy-definitiestructuur](../../governance/policy/concepts/definition-structure.md) voor een overzicht van de elementen van een beleid.

Hier volgen enkele aanbevolen procedures voor beveiliging nadat u Azure Policy hebt goedgekeurd:

**Beste praktijken**: Beleid ondersteunt verschillende soorten effecten. U erover lezen in [de definitiestructuur van Azure Policy](../../governance/policy/concepts/definition-structure.md#policy-rule). Bedrijfsactiviteiten kunnen negatief worden beïnvloed door het **deny-effect** en het **hersteleffect,** dus begin met het **auditeffect** om het risico van negatieve gevolgen van beleid te beperken.   
**Detail:** [Start beleidsimplementaties in de auditmodus](../../governance/policy/concepts/definition-structure.md#policy-rule) en vervolgens de voortgang om **te weigeren** of **te herstellen.** Test en beoordeel de resultaten van het controle-effect voordat u overgaat tot **weigering** of **herstel.**

Zie [Beleid maken en beheren om naleving af te dwingen](../../governance/policy/tutorials/create-and-manage.md)voor meer informatie.

**Beste praktijken**: Identificeer de rollen die verantwoordelijk zijn voor het monitoren van beleidsschendingen en het snel nemen van de juiste saneringsmaatregelen.   
**Detail:** Laat de toegewezen rol de naleving controleren via de [Azure-portal](../../governance/policy/how-to/get-compliance-data.md#portal) of via de [opdrachtregel](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Aanbevolen procedures**: Azure Policy is een technische weergave van het geschreven beleid van een organisatie. Wijs alle Azure-beleidsregels toe aan organisatiebeleid om verwarring te verminderen en de consistentie te vergroten.   
**Detail:** Documenttoewijzing in de documentatie van uw organisatie of in het Azure-beleid zelf door een verwijzing toe te voegen naar het organisatiebeleid in de [Azure-beleidsbeschrijving](../../governance/policy/concepts/definition-structure.md#display-name-and-description) of de beschrijving van het Azure-beleidsinitiatief. [initiative](../../governance/policy/concepts/definition-structure.md#initiatives)

## <a name="monitor-azure-ad-risk-reports"></a>Azure AD-risicorapporten controleren
De overgrote meerderheid van de inbreuken op de beveiliging vindt plaats wanneer aanvallers toegang krijgen tot een omgeving door het stelen van de identiteit van een gebruiker. Het ontdekken van gecompromitteerde identiteiten is geen gemakkelijke taak. Azure AD maakt gebruik van adaptieve machine learning-algoritmen en heuristiek om verdachte acties te detecteren die gerelateerd zijn aan uw gebruikersaccounts. Elke gedetecteerde verdachte actie wordt opgeslagen in een record genaamd een [risicodetectie.](../../active-directory/reports-monitoring/concept-risk-events.md) Risicodetecties worden geregistreerd in Azure AD-beveiligingsrapporten. Lees voor meer informatie over het [beveiligingsrapport van gebruikers met risico's](../../active-directory/reports-monitoring/concept-user-at-risk.md) en het [risicovolle beveiligingsrapport voor aanmeldingen](../../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Volgende stappen
Zie [aanbevolen procedures en patronen voor Azure-beveiliging](best-practices-and-patterns.md) voor meer aanbevolen procedures voor beveiliging die u gebruiken bij het ontwerpen, implementeren en beheren van uw cloudoplossingen met Azure.

De volgende bronnen zijn beschikbaar om meer algemene informatie te verstrekken over Azure-beveiliging en gerelateerde Microsoft-services:
* [Azure Security Team Blog](https://blogs.msdn.microsoft.com/azuresecurity/) - voor up-to-date informatie over de nieuwste azure security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) - waar beveiligingsproblemen van Microsoft, waaronder problemen met Azure, kunnen worden gemeld of via e-mail naarsecure@microsoft.com
