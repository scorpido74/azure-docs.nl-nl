---
title: Wat is Azure Security Center?| Microsoft Docs
description: 'Op deze pagina worden de belangrijkste voordelen van Security Center beschreven : uw beveiligingsstatus ontdekken en verbeteren met dekking van cloud- en on-prem-bronnen.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2019
ms.author: memildin
ms.openlocfilehash: 84ed5db54874231d8bde4d892eedbc60c42a3430
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77918754"
---
# <a name="what-is-azure-security-center"></a>Wat is Azure Security Center?

Azure Security Center is een geïntegreerd beveiligingsbeheersysteem voor de infrastructuur dat de beveiligingsstatus van uw datacenters verbetert en geavanceerde beveiliging tegen bedreigingen biedt voor uw hybride workloads in de cloud, of ze nu plaatsvinden in Azure of niet, en on-premises.

Het beveiligen van uw resources wordt in een samenwerking tussen uw cloudprovider, Azure en u, de klant, bewerkstelligd. U moet ervoor zorgen dat uw workloads beveiligd zijn wanneer u gebruik gaat maken van de cloud en tegelijkertijd heeft de klant meer verantwoordelijkheid wanneer u overstapt naar IaaS (infrastructuur als een dienst), in vergelijking met PaaS (platform als een dienst) en SaaS (software als een dienst). Azure Security Center biedt u de hulpprogramma's die nodig zijn om uw netwerk beter af te schermen, uw services te beveiligen en ervoor te zorgen dat uw beveiligingsstatus op peil blijft.

Azure Security Center biedt een oplossing voor de drie dringendste beveiligingsproblemen:

-   **Snel veranderende workloads**: dit is zowel een sterke kant als een uitdaging van de cloud. Enerzijds kunnen eindgebruikers zelf meer doen. Maar hoe zorgt u ervoor dat de voortdurend veranderende services die gebruikers gebruiken en maken, voldoen aan uw beveiligingsnormen en in overeenstemming zijn met de best practices voor beveiliging?

-   **Aanvallen die steeds geavanceerder worden**: overal waar u de workloads uitvoert, worden de aanvallen steeds geavanceerder. U moet uw workloads in de openbare cloud beveiligen. Dit zijn immers workloads die gebruikmaken van internet, zodat u nog kwetsbaarder bent als u zich niet houdt aan de best practices voor beveiliging.

-   **Beveiligingsspecialisten zijn schaars**: het aantal beveiligingswaarschuwingen en waarschuwingssystemen is veel groter dan het aantal beheerders met de benodigde achtergrond en ervaring die ervoor kunnen zorgen dat uw omgevingen goed zijn beveiligd. Het is een hele uitdaging om constant op de hoogte te blijven van de meest recente aanvallen. Stilstand is geen optie in de wereld van de beveiliging die altijd in beweging is.

Met Security Center kunt u zich tegen deze bedreigingen beveiligen. Het biedt namelijk hulpprogramma's waarmee u het volgende kunt doen:

-   **Beveiligingshouding versterken**: Security Center beoordeelt uw omgeving en stelt u in staat om de status van uw resources te begrijpen en of ze veilig zijn.

-   **Bescherm tegen bedreigingen:** Security Center beoordeelt uw workloads en verhoogt aanbevelingen voor bedreigingspreventie en beveiligingswaarschuwingen.

-   **Sneller veilig:** in Security Center gebeurt alles in cloudsnelheid. Omdat Security Center in het eigen systeem is geïntegreerd, is het eenvoudig te implementeren en kan het u automatische inrichting en beveiliging bieden met Azure-services.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="architecture"></a>Architectuur

Omdat Security Center standaard deel uitmaakt van Azure, worden PaaS-services in Azure, zoals Service Fabric, SQL-databases en opslagaccounts, gecontroleerd en beveiligd door Security Center zonder dat er een implementatie aan te pas komt.

Security Center beveiligt bovendien andere servers dan Azure-servers en virtuele machines in de cloud of on-premises, voor zowel Windows- als Linux-servers, door hierop de Microsoft Monitoring Agent te installeren. Azure-VM's worden automatisch ingericht in Security Center.

De gebeurtenissen die worden verzameld bij de agents en van Azure zijn gecorreleerd in de engine voor beveiligingsanalyse om u op maat gemaakte aanbevelingen te bieden (verhardingstaken), die u moet volgen om ervoor te zorgen dat uw workloads veilig zijn en beveiligingswaarschuwingen. U moet zulke waarschuwingen zo snel mogelijk onderzoeken om ervoor te zorgen dat uw workloads niet het doelwit worden van schadelijke aanvallen.

Wanneer u Security Center inschakelt, wordt het beveiligingsbeleid dat is ingebouwd in Security Center in Azure Policy als een ingebouwd initiatief onder de Security Center-categorie weergegeven. Het ingebouwde initiatief wordt automatisch toegewezen aan alle abonnementen die bij Security Center zijn geregistreerd (gratis of Standard-categorie). Het ingebouwde initiatief bevat alleen controlebeleid. Zie [Werken met beveiligingsbeleid](tutorial-security-policy.md) voor meer informatie over Security Center-beleid in Azure Policy.

## <a name="strengthen-security-posture"></a>Beveiligingspostuur verbeteren

Azure Security Center helpt u bij het verbeteren van de beveiligingsstatus. Dit houdt in dat u met Azure Security Center de beveiligingstaken kunt vaststellen en uitvoeren die worden aanbevolen als best practices voor de beveiliging en deze kunt implementeren op uw machines, en in uw gegevensservices en apps. Het houdt ook in dat uw beveiligingsbeleid wordt beheerd en afgedwongen, en dat ervoor wordt gezorgd dat de beleidsregels worden nageleefd op uw Azure-VM's, andere servers dan Azure-servers en in uw Azure PaaS-services. Security Center biedt u de hulpprogramma's waarmee u een totaaloverzicht hebt van de workloads en waarmee u de netwerkbeveiligingsomgeving goed in de gaten kunt houden. 

### <a name="manage-organization-security-policy-and-compliance"></a>Het beveiligingsbeleid en de naleving voor een organisatie beheren

Voor een goede beveiliging moet u natuurlijk weten of de workloads veilig zijn en deze hierop controleren. Hiervoor past u een beveiligingsbeleid toe dat op uw organisatie is afgestemd. Omdat alle beleidsregels in Security Center zijn gebouwd bovenop Azure-beleidsbesturingselementen, krijgt u het volledige bereik en de flexibiliteit van een beleidsoplossing van **wereldklasse.** U kunt in Security Center uw beleid zo instellen dat dit wordt uitgevoerd in beheergroepen, in verschillende abonnementen en zelfs voor een hele tenant.

![Dashboard van Security Center](media/security-center-intro/sc-dashboard.png)

Security Center helpt u **bij het identificeren van Shadow IT-abonnementen.** Wanneer u in uw dashboard abonnementen ziet met het label **valt niet onder beleid**, weet u meteen wanneer er recent gemaakte abonnementen zijn en kunt u ervoor zorgen dat deze onder uw beleid vallen en door Azure Security Center worden beveiligd.

![Beleidsdashboard van Security Center](media/security-center-intro/sc-policy-dashboard.png)

Met de geavanceerde bewakingsmogelijkheden in Security Center u ook compliance en governance in de **loop van de tijd bijhouden en beheren.** De **algemene naleving** biedt u een maat voor de mate van naleving van uw abonnementen met beleid dat is gekoppeld aan uw werkbelasting. 

![Security Center-beleid in de loop van de tijd](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Doorlopende beoordelingen

Security Center detecteert doorlopend nieuwe resources die voor uw workloads worden geïmplementeerd en beoordeelt of deze zijn geconfigureerd volgens de best practices voor de beveiliging. Als dit niet het geval is, worden ze gemarkeerd en ontvangt u een lijst met prioriteit die aanbevelingen bevat voor de problemen die moeten worden opgelost zodat uw machines kunnen worden beveiligd.

Een van de krachtigste tools die Security Center biedt voor het continu bewaken van de beveiligingsstatus van uw netwerk is de **netwerkkaart.** In het overzicht kunt u de topologie van uw workloads bekijken, zodat u kunt zien of elk knooppunt goed is geconfigureerd. U kunt bekijken hoe uw knooppunten zijn verbonden, zodat u ongewenste verbindingen kunt blokkeren die een aanvaller in staat kunnen stellen om in uw netwerk in te breken.

![Security Center-netwerkoverzicht](media/security-center-intro/sc-net-map.png)

Security Center maakt het beperken van uw beveiligingswaarschuwingen een stap eenvoudiger door een **secure score**toe te voegen. De secure scores zijn nu gekoppeld aan elke aanbeveling die u ontvangt om u te helpen begrijpen hoe belangrijk elke aanbeveling is voor uw algehele beveiligingshouding. Dit is cruciaal om u in staat te stellen **uw beveiligingswerk te prioriteren.**

![Secure Score beveiligingscentrum](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>De beveiliging optimaliseren en verbeteren door aanbevolen beheeropties te configureren

De kracht van Azure Security Center ligt in de aanbevelingen die het geeft. De aanbevelingen zijn afgestemd op de specifieke beveiligingsproblemen die in uw workloads worden aangetroffen. Security Center doet voor u het beveiligingsbeheer; niet alleen door uw beveiligingsproblemen op te sporen, maar ook door u van specifieke instructies te voorzien om deze problemen op te lossen.

![Security Center-aanbevelingen](media/security-center-intro/sc-recommendations.png)

Security Center stelt u op deze manier niet alleen in staat om het beveiligingsbeleid in te stellen, maar ook om veilige configuratienormen toe te passen voor uw resources.

Met de aanbevelingen kunt u de kwetsbaarheid voor aanvallen verminderen voor al uw resources. Dit geldt voor onder andere Azure-VM's, andere servers dan Azure-servers en Azure PaaS-services, zoals SQL- en Azure Storage-accounts. Hierbij wordt elk type resource anders beoordeeld en kent elk type eigen normen.

![Voorbeeld van een Security Center-aanbeveling](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Beveiligen tegen bedreigingen

Met de beveiliging tegen bedreigingen van Security Center kunt u bedreigingen detecteren en voorkomen op de IaaS-laag (infrastructuur als een dienst), andere servers dan Azure-servers en PaaS-services (platform als een dienst) in Azure.

De beveiliging tegen bedreigingen van Security Center bevat een complete analyse van de aanvalsketen, waarbij automatisch waarschuwingen in uw omgeving aan elkaar worden gerelateerd op basis van de cyberaanvalsketenanalyse, zodat u een beter inzicht hebt in de hele geschiedenis van een aanval; waar deze is gestart en welke impact deze had op uw resources.



![Aanbevelingen voor aanvallen in Security Center](media/security-center-intro/sc-attack-recommendation.png)

### <a name="integration-with-microsoft-defender-advanced-threat-protection"></a>Integratie met Microsoft Defender Advanced threat protection

Security Center bevat automatische, native integratie met Microsoft Defender Advanced Threat Protection. Dit betekent dat zonder enige configuratie uw Windows- en Linux-machines volledig zijn geïntegreerd met de aanbevelingen en beoordelingen van Security Center.

Bovendien kunt u met Security Center het beleid voor toepassingsbeheer automatiseren in serveromgevingen. Met de adaptieve toepassingsregelaars in Security Center kunt u end-to-end apps in een whitelist opnemen voor al uw Windows-servers. U hoeft geen regels te maken en op schendingen te controleren. Dit wordt allemaal automatisch voor u gedaan.

### <a name="protect-paas"></a>PaaS beveiligen

Security Center helpt u bij het detecteren van bedreigingen voor Azure PaaS-services. U kunt bedreigingen detecteren die zijn gericht op Azure-services, zoals Azure App Service, Azure SQL, Azure Storage Account en andere gegevensservices. U kunt ook gebruikmaken van de systeemeigen integratie met UEBA (User and Entity Behavioral Analytics) van Microsoft Cloud App Security om anomaliedetectie uit te voeren voor uw Azure-activiteitenlogboeken.

### <a name="block-brute-force-attacks"></a>Beveiligingsaanvallen blokkeren

Security Center zorgt ervoor dat u minder wordt blootgesteld aan beveiligingsaanvallen. Door de toegang tot poorten van virtuele machines te beperken met Just-In-Time-toegang tot VM's, kunt u de beveiliging van uw netwerk verbeteren door onnodige toegang te voorkomen. U kunt beleid voor beveiligde toegang instellen voor de geselecteerde poorten, voor alleen geautoriseerde gebruikers, toegestane bron-IP-adresbereiken of IP-adressen en voor een beperkt tijdbestek.

![Security Center en beveiligingsaanvallen](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Gegevensservices beveiligen

Security Center beschikt over mogelijkheden waarmee u de gegevens in Azure SQL automatisch kunt classificeren. U kunt ook beoordelingen krijgen van mogelijke beveiligingsproblemen in Azure SQL- en Azure Storage-services en aanbevelingen ontvangen voor de wijze waarop u deze kunt verhelpen.

### <a name="protect-iot-and-hybrid-cloud-workloads"></a>IoT- en hybride cloudworkloads beschermen

Azure Security Center for IoT (Internet of Things) vereenvoudigt de bescherming van hybride workloads door uniforme zichtbaarheid en controle, adaptieve bedreigingspreventie en intelligente bescherming en respons van bedreigingen te bieden voor workloads die op edge, on-premises worden uitgevoerd, in Azure en in andere clouds. Zie [Azure Security Center for IoT voor](https://docs.microsoft.com/azure/asc-for-iot/)meer informatie.

## <a name="get-secure-faster"></a>Uw infrastructuur sneller beveiligen

Native Azure-integratie (inclusief Azure Policy en Azure Monitor-logboeken) in combinatie met naadloze integratie met andere Microsoft-beveiligingsoplossingen, zoals Microsoft Cloud App Security en Windows Defender Advanced Threat Protection, zorgen ervoor dat u beveiligingsoplossing is uitgebreid en eenvoudig aan boord en uit te rollen.

Bovendien kunt u de volledige oplossing tot buiten Azure uitbreiden naar workloads die worden uitgevoerd in andere clouds en on-premises datacenters.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Automatisch Azure-resources detecteren en toevoegen

Security Center biedt naadloze en systeemeigen integratie met Azure en Azure-resources. Dit betekent dat u een volledige beveiligingsgeschiedenis kunt ophalen met betrekking tot Azure Policy en ingebouwd Security Center-beleid voor al uw Azure-resources en ervoor kunt zorgen dat alles automatisch wordt toegepast op recent gedetecteerde resources wanneer u deze in Azure maakt.

Uitgebreide logboekverzameling: logboeken van Windows en Linux worden gebruikt door de engine voor beveiligingsanalyses en geanalyseerd om aanbevelingen te doen en waarschuwingen te geven.

## <a name="next-steps"></a>Volgende stappen

- U moet over een Microsoft Azure-abonnement beschikken om met Security Center aan de slag te gaan. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/).
- De gratis prijscategorie van Security Center is ingeschakeld met uw Azure-abonnement. Als u wilt profiteren van geavanceerde mogelijkheden voor beveiligingsbeheer en bedreigingsbeveiliging, moet u upgraden naar de standaardprijslaag. De standaardlaag kan 30 dagen gratis worden uitgeprobeerd. Zie de [prijspagina van het Beveiligingscentrum](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie.
- Als u nu de Standard van het Beveiligingscentrum wilt inschakelen, leidt het [Quickstart: Aan boord van uw Azure-abonnement bij Security Center Standard](security-center-get-started.md) u door de stappen.