---
title: Aanbevolen beveiligings procedures voor het beveiligen van uw assets-Microsoft Azure
description: Dit artikel bevat een reeks operationele aanbevolen procedures voor het beveiligen van uw gegevens, toepassingen en andere assets in Azure.
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
ms.openlocfilehash: 9da58bf3152867bc13dd1879ec57a69b8bd38cb0
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129336"
---
# <a name="azure-operational-security-best-practices"></a>Best practices voor Azure Operational Security
Dit artikel bevat een reeks operationele aanbevolen procedures voor het beveiligen van uw gegevens, toepassingen en andere assets in Azure.

De aanbevolen procedures zijn gebaseerd op een advies van de mening en ze werken met de huidige mogelijkheden en functie sets van het Azure-platform. Meningen en technologieën veranderen in de loop van de tijd en dit artikel wordt regel matig bijgewerkt om deze wijzigingen weer te geven.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Krachtige operationele beveiligings procedures definiëren en implementeren
Azure Operational Security heeft betrekking op de services, besturings elementen en functies die beschikbaar zijn voor gebruikers voor het beveiligen van hun gegevens, toepassingen en andere assets in Azure. Azure Operational Security is gebaseerd op een Framework met de kennis die is opgedaan via mogelijkheden die uniek zijn voor micro soft, waaronder de [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl), het [micro soft Security Response Center](https://www.microsoft.com/msrc?rtc=1) Program ma's en dieper inzicht in de Cyber beveiliging Threat landschap.

## <a name="manage-and-monitor-user-passwords"></a>Gebruikers wachtwoorden beheren en bewaken
De volgende tabel bevat enkele aanbevolen procedures voor het beheren van gebruikers wachtwoorden:

**Aanbevolen procedure**: Zorg ervoor dat u het juiste niveau van wachtwoord beveiliging hebt in de Cloud.   
**Details**: Volg de richt lijnen in [micro soft-wachtwoord richtlijnen](https://www.microsoft.com/research/publication/password-guidance/)voor gebruikers van de micro soft Identity platforms (Azure Active Directory, Active Directory en Microsoft-account).

**Aanbevolen procedure**: Controleren op verdachte acties die betrekking hebben op uw gebruikers accounts.   
**Details**: Bewaak voor [gebruikers die risico](/azure/active-directory/reports-monitoring/concept-user-at-risk) lopen en [Risk ante](../../active-directory/reports-monitoring/concept-risk-events.md) aanmeldingen met BEhulp van Azure AD-beveiligings rapporten.

**Aanbevolen procedure**: Automatische detectie en herstel van wacht woorden met een hoog risico.   
**Details**: [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview) is een functie van de Azure AD Premium P2-editie waarmee u het volgende kunt doen:

- Mogelijke beveiligings problemen detecteren die van invloed zijn op de identiteiten van uw organisatie
- Automatische antwoorden configureren op gedetecteerde verdachte acties die zijn gerelateerd aan de identiteiten van uw organisatie
- Onderzoek verdachte incidenten en onderneem passende maat regelen om deze op te lossen

## <a name="receive-incident-notifications-from-microsoft"></a>Meldingen over incidenten ontvangen van micro soft
Zorg ervoor dat uw beveiligings team meldingen van Azure-incidenten ontvangt van micro soft. Met een melding over incidenten kan uw beveiligings team weten dat er inbreuk is op Azure-resources, zodat ze snel kunnen reageren op potentiële beveiligings Risico's.

In de Azure-inschrijvings Portal kunt u ervoor zorgen dat de contact gegevens van de beheerder gegevens bevatten over de beveiliging. De contact gegevens zijn een e-mail adres en telefoon nummer.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Azure-abonnementen organiseren in beheer groepen
Als uw organisatie veel abonnementen heeft, hebt u mogelijk een manier nodig om de toegang, het beleid en de naleving van deze abonnementen efficiënt te beheren. [Azure-beheer groepen](/azure/governance/management-groups/create) bieden een niveau van bereik dat hoger is dan abonnementen. U kunt abonnementen in containers met de naam beheer groepen organiseren en uw governance-voor waarden Toep assen op de-beheer groepen. Alle abonnementen in een beheergroep nemen automatisch de voorwaarden over die op de beheergroep zijn toegepast.

U kunt een flexibele structuur van beheer groepen en abonnementen bouwen in een directory. Elke map krijgt één beheer groep op het hoogste niveau met de naam de hoofd beheer groep. Deze hoofdbeheergroep is zo in de hiërarchie ingebouwd dat alle beheergroepen en abonnementen hierin zijn opgevouwen. Met de hoofd beheer groep kunnen globale beleids regels en RBAC-toewijzingen op mapniveau worden toegepast.

Hier volgen enkele aanbevolen procedures voor het gebruik van beheer groepen:

**Aanbevolen procedure**: Zorg ervoor dat nieuwe abonnementen governance-elementen Toep assen zoals beleid en machtigingen wanneer ze worden toegevoegd.   
**Details**: Gebruik de hoofd beheer groep om beveiligings elementen op ondernemings niveau toe te wijzen die van toepassing zijn op alle Azure-assets. Beleids regels en machtigingen zijn voor beelden van elementen.

**Aanbevolen procedure**: Lijn de hoogste niveaus van beheer groepen uit met segmentatie strategie om een punt te bieden voor de consistentie van beheer en beleid binnen elk segment.   
**Details**: Maak één beheer groep voor elk segment in de hoofd beheer groep. Maak geen andere beheer groepen in de hoofdmap.

**Aanbevolen procedure**: Beperk de diepte van de beheer groep om Verwar ring te voor komen die zowel bewerkingen als beveiliging belemmeren.   
**Details**: Beperk uw hiërarchie tot drie niveaus, met inbegrip van de hoofdmap.

**Aanbevolen procedure**: Selecteer zorgvuldig welke items u wilt Toep assen op de hele onderneming met de hoofd beheer groep.   
**Details**: Zorg ervoor dat de elementen van de hoofd beheer groep duidelijk moeten worden toegepast op elke resource en dat ze weinig invloed hebben.

Goede kandidaten zijn onder andere:

- Wettelijke vereisten die een duidelijke bedrijfs impact hebben (bijvoorbeeld beperkingen met betrekking tot data soevereiniteit)
- Vereisten met bijna nul mogelijke negatieve invloed op bewerkingen, zoals beleid met controle-of RBAC-machtigingen toewijzingen die zorgvuldig zijn gecontroleerd

**Aanbevolen procedure**: Plan alle zakelijke wijzigingen in de hoofd beheer groep zorgvuldig en test deze voordat u ze toepast (beleid, RBAC-model, enzovoort).   
**Details**: Wijzigingen in de hoofd beheer groep kunnen van invloed zijn op elke resource in Azure. Hoewel ze een krachtige manier bieden om consistentie tussen de onderneming te garanderen, kunnen fouten of een onjuist gebruik de productie bewerkingen negatief beïnvloeden. Test alle wijzigingen in de hoofd beheer groep in een testlab of productie pilot.

## <a name="streamline-environment-creation-with-blueprints"></a>Het maken van een omgeving stroom lijnen met blauw drukken
Met de Azure-service voor [blauw drukken](/azure/governance/blueprints/overview) kunnen Cloud architecten en centrale informatie technologie groepen een Herhaal bare set Azure-resources definiëren die worden geïmplementeerd en voldoet aan de normen, patronen en vereisten van een organisatie. Met Azure-blauw drukken kunnen ontwikkel teams snel nieuwe omgevingen bouwen en maken met een aantal ingebouwde componenten en het vertrouwen dat ze in de organisatie-en bedrijfs voorwaarden maken.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Opslag Services controleren op onverwachte wijzigingen in gedrag
Diagnose-en probleemoplossings problemen in een gedistribueerde toepassing die wordt gehost in een cloud omgeving, kunnen complexer zijn dan in traditionele omgevingen. Toepassingen kunnen worden geïmplementeerd in een PaaS-of IaaS-infra structuur, on-premises, op een mobiel apparaat of in een bepaalde combi natie van deze omgevingen. Het netwerk verkeer van uw toepassing kan open bare en particuliere netwerken passeren en uw toepassing kan gebruikmaken van meerdere opslag technologieën.

U moet voortdurend de opslag services bewaken die uw toepassing gebruikt voor eventuele onverwachte wijzigingen in gedrag (zoals tragere reactie tijden). Gebruik logboek registratie voor het verzamelen van gedetailleerde gegevens en het analyseren van een probleem. De diagnostische gegevens die u van zowel controle als logboek registratie verkrijgt, helpt u bij het bepalen van de hoofd oorzaak van het probleem dat de toepassing heeft aangetroffen. Vervolgens kunt u het probleem oplossen en de juiste stappen voor het herstellen van de oplossing bepalen.

[Azure Opslaganalyse](../../storage/common/storage-analytics.md) voert logboek registratie uit en geeft metrische gegevens voor een Azure-opslag account. U wordt aangeraden deze gegevens te gebruiken voor het traceren van aanvragen, het analyseren van gebruiks trends en het vaststellen van problemen met uw opslag account.

## <a name="prevent-detect-and-respond-to-threats"></a>Bedreigingen voor komen, detecteren en erop reageren
[Azure Security Center](../../security-center/security-center-intro.md) helpt u bedreigingen te voor komen, te detecteren en erop te reageren door beter inzicht te krijgen in de beveiliging van uw Azure-resources (en controle over). Het biedt geïntegreerde beveiligings bewaking en beleids beheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders niet kunnen worden opgemerkt en die samen werken met verschillende beveiligings oplossingen.

De gratis laag van Security Center biedt beperkte beveiliging voor alleen uw Azure-resources. De laag standaard breidt deze mogelijkheden uit naar on-premises en andere Clouds. Security Center standaard helpt u bij het vinden en oplossen van beveiligings problemen, het Toep assen van toegangs-en toepassings besturings elementen om schadelijke activiteiten te blok keren, bedreigingen te detecteren met behulp van analyses en intelligentie en snel te reageren wanneer het een aanval doet. U kunt Security Center Standard de eerste 60 dagen kosteloos proberen. U wordt aangeraden [uw Azure-abonnement bij te werken naar Security Center Standard](../../security-center/security-center-get-started.md).

Gebruik Security Center om een centraal overzicht te krijgen van de beveiligings status van al uw Azure-resources. Controleer in één oogopslag of de juiste beveiligings maatregelen aanwezig zijn en correct zijn geconfigureerd, en geef snel alle resources die aandacht nodig hebben.

Security Center is ook geïntegreerd met behulp van [Windows Defender Advanced Threat Protection (ATP)](../../security-center/security-center-wdatp.md), dat uitgebreide functionaliteit voor het detecteren en reageren van eind punten biedt. Met Windows Defender ATP-integratie kunt u afwijkingen herkennen. U kunt ook geavanceerde aanvallen detecteren en erop reageren op server-eind punten die worden bewaakt door Security Center.

Bijna alle bedrijfs organisaties hebben een SIEM-systeem (Security Information and Event Management) waarmee nieuwe bedreigingen kunnen worden geïdentificeerd door logboek gegevens te consolideren van verschillende apparaten voor signaal verzameling. De logboeken worden vervolgens geanalyseerd door een gegevens analyse systeem om te helpen bij het identificeren van ' interessant ' van de ruis die onvermijdelijk is in alle logboek verzameling en analyse oplossingen.

[Azure Sentinel](/azure/sentinel/overview) is een schaal bare, Siem-oplossing (Security Information and Event Management) en via (Security Orchestration Automated Response). Azure Sentinel biedt intelligente beveiligings analyses en bedreigings informatie via detectie van waarschuwingen, zicht baarheid van bedreigingen, proactieve jacht en geautomatiseerd antwoord op bedreigingen.

Hier volgen enkele aanbevolen procedures voor het voor komen, detecteren en reageren op bedreigingen:

**Aanbevolen procedure**: Verhoog de snelheid en schaal baarheid van uw SIEM-oplossing met behulp van een SIEM op basis van de Cloud.   
**Details**: Onderzoek de functies en mogelijkheden van [Azure Sentinel](/azure/sentinel/overview) en vergelijk ze met de mogelijkheden van wat u momenteel op locatie gebruikt. Overweeg het vaststellen van Azure Sentinel als deze voldoet aan de SIEM-vereisten van uw organisatie.

**Aanbevolen procedure**: Zoek de meest ernstige beveiligings problemen, zodat u het onderzoek kunt priori teren.   
**Details**: Bekijk uw [beveiligde Azure-Score](../../security-center/security-center-secure-score.md) voor een overzicht van de aanbevelingen die voortkomen uit de Azure-beleids regels en-initiatieven die zijn ingebouwd in azure Security Center. Deze aanbevelingen helpen u bij het oplossen van de belangrijkste Risico's zoals beveiligings updates, Endpoint Protection, versleuteling, beveiligings configuraties, ontbrekende WAF, met internet verbonden Vm's en nog veel meer.

Met de beveiligde Score, die is gebaseerd op de besturings elementen Center voor Internet Security (CIS), kunt u de Azure-beveiliging van uw organisatie vergelijkt met externe bronnen. Externe validatie helpt bij het valideren en verrijken van de beveiligings strategie van uw team.

**Aanbevolen procedure**: Bewaak de beveiligings postuur van machines, netwerken, opslag en gegevens Services en toepassingen om mogelijke beveiligings problemen op te sporen en op te lossen.  
**Details**: Volg de [beveiligings aanbevelingen](../../security-center/security-center-recommendations.md) in Security Center starten, met de items met de hoogste prioriteit.

**Aanbevolen procedure**: Integreer Security Center waarschuwingen in uw SIEM-oplossing (Security Information and Event Management).   
**Details**: De meeste organisaties met een SIEM gebruiken deze als centraal-Clearinghouse voor beveiligings waarschuwingen waarvoor een analisten reactie nodig is. Verwerkte gebeurtenissen die zijn geproduceerd door Security Center worden gepubliceerd in het Azure-activiteiten logboek, een van de logboeken die beschikbaar zijn via Azure Monitor. Azure Monitor biedt een geconsolideerde pijp lijn voor de route ring van uw bewakings gegevens in een SIEM-hulp programma. Zie [beveiligings oplossingen integreren in Security Center](../../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) voor instructies. Als u Azure Sentinel gebruikt, raadpleegt u [verbinding maken Azure Security Center](../../sentinel/connect-azure-security-center.md).

**Aanbevolen procedure**: Integreer Azure-logboeken met uw SIEM.   
**Details**: Gebruik [Azure monitor om gegevens te verzamelen en te exporteren](/azure/azure-monitor/overview#integrate-and-export-data). Deze procedure is essentieel voor het inschakelen van het onderzoek van beveiligings incidenten en het online bewaren van Logboeken is beperkt. Zie [verbinding maken met gegevens bronnen](../../sentinel/connect-data-sources.md)als u Azure Sentinel gebruikt.

**Aanbevolen procedure**: Versnel uw onderzoek en jacht processen en verminder fout-positieven door de functies voor het detecteren van eind punten te integreren in uw aanvals onderzoek.   
**Details**: [Schakel de Windows Defender ATP-integratie in](../../security-center/security-center-wdatp.md#enable-windows-defender-atp-integration) via uw Security Center-beveiligings beleid. Overweeg het gebruik van Azure Sentinel voor het beletten van dreigingen en reacties op incidenten.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>End-to-end op scenario's gebaseerde netwerk bewaking bewaken
Klanten bouwen een end-to-end netwerk in azure door netwerk bronnen te combi neren, zoals een virtueel netwerk, ExpressRoute, Application Gateway en load balancers. Bewaking is beschikbaar op elk van de netwerk bronnen.

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) is een regionale service. Gebruik de diagnostische en visualisatie hulpprogramma's om voor waarden te controleren en te diagnosticeren op het niveau van een netwerk scenario in, naar en van Azure.

Hieronder vindt u de aanbevolen procedures voor netwerk bewaking en beschik bare hulpprogram ma's.

**Aanbevolen procedure**: Automatiseer externe netwerk bewaking met pakket opname.  
**Details**: Bewaak en diagnose netwerk problemen zonder u aan te melden bij uw Vm's met behulp van Network Watcher. Activeer [pakket vastleggen](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) door waarschuwingen in te stellen en toegang te krijgen tot real-time prestatie gegevens op pakket niveau. Wanneer er een probleem wordt vastgesteld, kunt u dat uitgebreid onderzoeken voor een gedetailleerde diagnose.

**Aanbevolen procedure**: Krijg inzicht in uw netwerk verkeer door gebruik te maken van stroom Logboeken.  
**Details**: Bouw een dieper inzicht in uw netwerk verkeer met behulp van [stroom logboeken voor netwerk beveiligings groepen](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). Informatie in stroom logboeken helpt u bij het verzamelen van gegevens voor naleving, controle en controle van uw netwerk beveiligings profiel.

**Aanbevolen procedure**: Problemen met de VPN-verbinding vaststellen.  
**Details**: Gebruik Network Watcher om [uw meest voorkomende VPN gateway en verbindings problemen vast](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md)te stellen. U kunt het probleem niet alleen identificeren, maar ook gedetailleerde logboeken gebruiken om verder te onderzoeken.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Implementatie beveiligen met behulp van bewezen DevOps-hulpprogram ma's
Gebruik de volgende best practices voor DevOps om ervoor te zorgen dat uw onderneming en teams productief en efficiënt zijn.

**Aanbevolen procedure**: Automatiseer het bouwen en implementeren van services.  
**Details**: [Infra structuur als code](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) bestaat uit een reeks technieken en procedures waarmee IT-professionals de last van het dagelijks bouwen en beheren van modulaire infra structuur kunnen opheffen. Zo kunnen IT-professionals hun moderne server omgeving bouwen en onderhouden op een manier zoals hoe software ontwikkelaars toepassings code bouwen en onderhouden.

U kunt [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) gebruiken om uw toepassingen in te richten met behulp van een declaratieve sjabloon. U kunt in één enkele sjabloon meerdere services plus de bijbehorende afhankelijkheden implementeren. U gebruikt dezelfde sjabloon om uw toepassing herhaaldelijk te implementeren in elke fase van de levens cyclus van de toepassing.

**Aanbevolen procedure**: Bouw en implementeer automatisch Azure web apps of Cloud Services.  
**Details**: U kunt uw Azure DevOps Projects configureren om automatisch Azure web apps of Cloud Services te [bouwen en implementeren](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) . Azure DevOps implementeert de binaire bestanden automatisch na het maken van een build naar Azure na elke code inchecken. Het proces voor het bouwen van pakketten is gelijk aan de pakket opdracht in Visual Studio en de publicatie stappen zijn gelijk aan de opdracht publiceren in Visual Studio.

**Aanbevolen procedure**: Automatiseer release beheer.  
**Details**: [Azure-pijp lijnen](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) is een oplossing voor het automatiseren van de implementatie met meerdere stages en het beheren van het release proces. Maak beheerde pijp lijnen voor continue implementatie om snel, eenvoudig en vaak te worden vrijgegeven. Met Azure-pijp lijnen kunt u uw release proces automatiseren en kunt u vooraf gedefinieerde goedkeurings werk stromen hebben. Implementeer on-premises en naar de Cloud, breid en pas ze indien nodig aan.

**Aanbevolen procedure**: Controleer de prestaties van uw app voordat u deze start of installeer updates voor de productie.  
**Details**: Voer in de cloud gebaseerde [belasting tests](/azure/devops/test/load-test/overview#alternatives) uit om:

- Zoek prestatie problemen in uw app.
- Verbeter de implementatie kwaliteit.
- Zorg ervoor dat uw app altijd beschikbaar is.
- Zorg ervoor dat uw app verkeer kan verwerken voor uw volgende start-of marketing campagne.

[Apache JMeter](https://jmeter.apache.org/) is een gratis, populair hulp programma voor open source met een krachtige Community-back-up.

**Aanbevolen procedure**: Bewaak de prestaties van de toepassing.  
**Details**: [Azure-toepassing Insights](../../azure-monitor/app/app-insights-overview.md) is een UITBREID bare apm-service (Application Performance Management) voor webontwikkelaars op meerdere platforms. Gebruik Application Insights om uw Live Web-app te bewaken. Er worden automatisch prestatie afwijkingen gedetecteerd. Het bevat hulpprogram ma's voor analyse waarmee u problemen kunt vaststellen en inzicht kunt krijgen in wat gebruikers daad werkelijk doen met uw app. Het is bedoeld om u te helpen de prestaties en bruikbaarheid continu te verbeteren.

## <a name="mitigate-and-protect-against-ddos"></a>Problemen oplossen en beveiligen tegen DDoS
Distributed Denial of service (DDoS) is een type aanval waarmee toepassings bronnen worden uitgeput. Het doel is de beschik baarheid van de toepassing en de mogelijkheid om legitieme aanvragen af te handelen te beïnvloeden. Deze aanvallen zijn geavanceerder en groter en van invloed op de omvang en de impact. Ze kunnen worden gericht op elk eind punt dat openbaar bereikbaar is via internet.

Voor het ontwerpen en bouwen van DDoS-tolerantie moet u plannen en ontwerpen voor diverse fout modi. Hieronder vindt u de aanbevolen procedures voor het bouwen van DDoS-flexibele services in Azure.

**Aanbevolen procedure**: Zorg ervoor dat beveiliging een prioriteit heeft gedurende de hele levens cyclus van een toepassing, van ontwerp en implementatie tot implementatie en bewerkingen. Toepassingen kunnen fouten hebben waardoor een relatief laag volume aan aanvragen een groot aantal bronnen kan gebruiken, wat resulteert in een service storing.  
**Details**: Als u een service die wordt uitgevoerd op Microsoft Azure wilt beveiligen, moet u een goed beeld hebben van de architectuur van uw toepassing en zich richten op de [vijf pijlers van de software kwaliteit](https://docs.microsoft.com/azure/architecture/guide/pillars). U moet rekening houden met typische verkeers volumes, het verbindings model tussen de toepassing en andere toepassingen en de service-eind punten die beschikbaar worden gesteld aan het open bare Internet.

Ervoor zorgen dat een toepassing robuust genoeg is voor het afhandelen van een denial of service die is gericht op de toepassing zelf, is het belangrijkst. Beveiliging en privacy zijn ingebouwd in het Azure-platform, te beginnen met de [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl). De SDL vertrouwt de beveiliging van elke ontwikkelings fase en zorgt ervoor dat Azure voortdurend wordt bijgewerkt om het nog veiliger te maken.

**Aanbevolen procedure**: Ontwerp uw toepassingen zodanig dat ze [horizon taal](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) kunnen worden geschaald om te voldoen aan de vraag naar een versterkte belasting, met name in het geval van een DDoS-aanval. Als uw toepassing afhankelijk is van één exemplaar van een service, wordt er een Single Point of Failure gemaakt. Door meerdere exemplaren in te richten, zorgt u ervoor dat uw systeem robuuster en schaalbaar is.  
**Details**: Selecteer voor [Azure app service](/azure/app-service/app-service-value-prop-what-is)een [app service-abonnement](../../app-service/overview-hosting-plans.md) dat meerdere exemplaren biedt.

Configureer voor Azure Cloud Services elk van uw rollen om [meerdere exemplaren](../../cloud-services/cloud-services-choose-me.md)te gebruiken.

Zorg ervoor dat uw VM-architectuur meer dan één virtuele machine bevat voor [Azure virtual machines](/azure/virtual-machines/windows/overview)en dat elke virtuele machine is [](/azure/virtual-machines/virtual-machines-windows-manage-availability)opgenomen in een beschikbaarheidsset. We raden u aan virtuele-machine schaal sets te gebruiken voor de mogelijkheden voor automatisch schalen.

**Aanbevolen procedure**: Als u beveiligings beveiliging in een toepassing laagt, vermindert u de kans op een geslaagde aanval. Implementeer beveiligde ontwerpen voor uw toepassingen met behulp van de ingebouwde mogelijkheden van het Azure-platform.  
**Details**: Het risico van een aanval neemt toe met de grootte (surface area) van de toepassing. U kunt de surface area verminderen door White List te gebruiken om de beschik bare IP-adres ruimte te sluiten en poorten te belui Steren die niet nodig zijn op de load balancers ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) en [Azure-toepassing gateway](/azure/application-gateway/application-gateway-create-probe-portal)).

[Netwerk beveiligings groepen](../../virtual-network/security-overview.md) zijn een andere manier om de kwets baarheid voor aanvallen te verminderen. U kunt [service Tags](../../virtual-network/security-overview.md#service-tags) en [toepassings beveiligings groepen](../../virtual-network/security-overview.md#application-security-groups) gebruiken om de complexiteit te minimaliseren voor het maken van beveiligings regels en het configureren van netwerk beveiliging, als een natuurlijke uitbrei ding van de structuur van een toepassing.

Indien mogelijk moet u Azure-Services in een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md) implementeren. Met deze procedure kunnen service resources communiceren via privé-IP-adressen. Azure service-verkeer van een virtueel netwerk maakt standaard gebruik van open bare IP-adressen als bron-IP-adressen.

Het gebruik van [service-eind punten](../../virtual-network/virtual-network-service-endpoints-overview.md) schakelt service verkeer in voor het gebruik van privé adressen van een virtueel netwerk als de IP-bron adressen van het bedrijf wanneer ze toegang krijgen tot de Azure-service vanuit een virtueel netwerk.

Vaak zien we de on-premises resources van klanten die zijn aangevallen samen met hun resources in Azure. Als u een on-premises omgeving verbindt met Azure, minimaliseert u de bloot stelling van on-premises resources naar het open bare Internet.

Azure heeft twee DDoS- [service aanbiedingen](../../virtual-network/ddos-protection-overview.md) die bescherming bieden tegen netwerk aanvallen:

- Basis beveiliging is standaard zonder extra kosten geïntegreerd in Azure. De schaal en de capaciteit van het wereld wijd geïmplementeerde Azure-netwerk bieden bescherming tegen veelvoorkomende aanvallen via netwerk lagen via de controle van het verkeer en de real-time-oplossing. Basic vereist geen gebruikers configuratie of wijzigingen in de toepassing en helpt bij het beveiligen van alle Azure-Services, waaronder PaaS Services als Azure DNS.
- Standaard beveiliging biedt geavanceerde mogelijkheden voor DDoS-beperking tegen netwerk aanvallen. Het is automatisch afgestemd op het beveiligen van uw specifieke Azure-resources. De beveiliging is eenvoudig in te scha kelen tijdens het maken van virtuele netwerken. Dit kan ook worden gedaan na het maken en er hoeven geen toepassings-of resource wijzigingen te worden aangebracht.

## <a name="enable-azure-policy"></a>Azure Policy inschakelen
[Azure Policy](/azure/governance/policy/overview) is een service in azure die u gebruikt om beleids regels te maken, toe te wijzen en te beheren. Met deze beleids regels worden regels en effecten voor uw resources afgedwongen, zodat deze resources compatibel blijven met uw bedrijfs standaarden en service level-overeenkomsten. Azure Policy voorziet in deze behoefte door uw resources met toegewezen beleid te controleren op niet-naleving.

Schakel Azure Policy in om het vastgelegde beleid van uw organisatie te controleren en af te dwingen. Dit zorgt ervoor dat de beveiligings vereisten van uw bedrijf of regelgeving worden nageleefd door het beveiligings beleid voor uw hybride Cloud werkbelastingen centraal te beheren. Meer informatie over het [maken en beheren van beleid om naleving af te dwingen](../../governance/policy/tutorials/create-and-manage.md). Zie [Azure Policy definitie structuur](../../governance/policy/concepts/definition-structure.md) voor een overzicht van de elementen van een beleid.

Hier volgen enkele aanbevolen procedures voor beveiliging na het aannemen van Azure Policy:

**Aanbevolen procedure**: Het beleid ondersteunt verschillende soorten effecten. Meer informatie hierover vindt u in [Azure Policy Definition structure](../../governance/policy/concepts/definition-structure.md#policy-rule). Bedrijfs bewerkingen kunnen een negatieve invloed hebben op het **weigerings** effect en het **herstel** effect, dus begin met het **controle** -effect om het risico van negatieve gevolgen van het beleid te beperken.   
**Details**: [Start beleids implementaties in de controle modus](../../governance/policy/concepts/definition-structure.md#policy-rule) en stel vervolgens de voortgang op **weigeren** of **herstellen**. De resultaten van het controle-effect testen en bekijken voordat u doorgaat met **weigeren** of **herstellen**.

Zie [beleid maken en beheren om naleving af te dwingen](../../governance/policy/tutorials/create-and-manage.md)voor meer informatie.

**Aanbevolen procedure**: Identificeer de rollen die verantwoordelijk zijn voor de bewaking van beleids schendingen en zorg ervoor dat de juiste herstel actie snel wordt uitgevoerd.   
**Details**: De toegewezen Role monitor moet voldoen aan het [Azure Portal](../../governance/policy/how-to/get-compliance-data.md#portal) of via de [opdracht regel](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Aanbevolen procedure**: Azure Policy is een technische weer gave van het schriftelijke beleid van een organisatie. Wijs Alle Azure-beleids regels toe aan het organisatie beleid om Verwar ring te beperken en de consistentie te verg Roten.   
**Details**: Document toewijzing in de documentatie van uw organisatie of in het Azure-beleid zelf door een verwijzing naar het organisatie beleid toe te voegen in de beschrijving van het Azure- [beleid](../../governance/policy/concepts/definition-structure.md#display-name-and-description) of de beschrijving van het Azure Policy [Initiative](../../governance/policy/concepts/definition-structure.md#initiatives) .

## <a name="monitor-azure-ad-risk-reports"></a>Risico rapporten van Azure AD bewaken
Het overgrote deel van de beveiligings Risico's doen zich voor wanneer aanvallers toegang krijgen tot een omgeving door de identiteit van een gebruiker te stelen. Het detecteren van gemanipuleerde identiteiten is geen eenvoudige taak. Azure AD gebruikt adaptieve machine learning algoritmen en heuristiek om verdachte acties te detecteren die betrekking hebben op uw gebruikers accounts. Elke gedetecteerde verdachte actie wordt opgeslagen in een record met de naam [risico detectie](../../active-directory/reports-monitoring/concept-risk-events.md). Risico detecties worden vastgelegd in azure AD-beveiligings rapporten. Lees voor meer informatie over het beveiligings rapport [gebruikers die risico](../../active-directory/reports-monitoring/concept-user-at-risk.md) lopen, en het [beveiligings rapport Risk ante](../../active-directory/reports-monitoring/concept-risky-sign-ins.md)aanmeldingen.

## <a name="next-steps"></a>Volgende stappen
Zie [Aanbevolen procedures en patronen voor Azure-beveiliging](best-practices-and-patterns.md) voor meer aanbevolen procedures voor beveiliging bij het ontwerpen, implementeren en beheren van uw cloud oplossingen met behulp van Azure.

De volgende resources zijn beschikbaar om meer algemene informatie te geven over Azure-beveiliging en gerelateerde micro soft-Services:
* [Blog van het Azure-beveiligings team](https://blogs.msdn.microsoft.com/azuresecurity/) : voor actuele informatie over de nieuwste Azure-beveiliging
* [Micro soft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) : waar micro soft-beveiligings problemen, met inbegrip van problemen met Azure, kunnen worden gerapporteerd of via e-mail worden verzonden naarsecure@microsoft.com
