---
title: Wat is Azure Security Center?| Microsoft Docs
description: Op deze pagina worden de belangrijkste voor delen van Security Center beschreven, waarbij u de beveiligings status kunt detecteren en deze kunt verbeteren met een dekking van Cloud-en on-premises resources.
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
ms.openlocfilehash: 759cc3f22d8b57036bad48326ffec1d6fca42696
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939440"
---
# <a name="what-is-azure-security-center"></a>Wat is Azure Beveiligingscentrum?

Azure Security Center is een geïntegreerd beveiligingsbeheersysteem voor de infrastructuur dat de beveiligingsstatus van uw datacenters verbetert en geavanceerde beveiliging tegen bedreigingen biedt voor uw hybride workloads in de cloud, of ze nu plaatsvinden in Azure of niet, en on-premises.

Het beveiligen van uw resources wordt in een samenwerking tussen uw cloudprovider, Azure en u, de klant, bewerkstelligd. U moet ervoor zorgen dat uw workloads beveiligd zijn wanneer u gebruik gaat maken van de cloud en tegelijkertijd heeft de klant meer verantwoordelijkheid wanneer u overstapt naar IaaS (infrastructuur als een dienst), in vergelijking met PaaS (platform als een dienst) en SaaS (software als een dienst). Azure Security Center biedt u de hulpprogramma's die nodig zijn om uw netwerk beter af te schermen, uw services te beveiligen en ervoor te zorgen dat uw beveiligingsstatus op peil blijft.

Azure Security Center biedt een oplossing voor de drie dringendste beveiligingsproblemen:

-   **Snel veranderende workloads**: dit is zowel een sterke kant als een uitdaging van de cloud. Enerzijds kunnen eindgebruikers zelf meer doen. Maar hoe zorgt u ervoor dat de voortdurend veranderende services die gebruikers gebruiken en maken, voldoen aan uw beveiligingsnormen en in overeenstemming zijn met de best practices voor beveiliging?

-   **Steeds meer geavanceerde aanvallen** : wanneer u uw werk belastingen uitvoert, blijven de aanvallen meer geavanceerd. U moet uw workloads in de openbare cloud beveiligen. Dit zijn immers workloads die gebruikmaken van internet, zodat u nog kwetsbaarder bent als u zich niet houdt aan de best practices voor beveiliging.

-   **Beveiligings vaardigheden bevinden zich in de korte voeding** . het aantal beveiligings waarschuwingen en waarschuwings systemen dat in handen is, is het aantal beheerders met de benodigde achtergrond en ervaring om ervoor te zorgen dat uw omgevingen worden beschermd. Het is een hele uitdaging om constant op de hoogte te blijven van de meest recente aanvallen. Stilstand is geen optie in de wereld van de beveiliging die altijd in beweging is.

Met Security Center kunt u zich tegen deze bedreigingen beveiligen. Het biedt namelijk hulpprogramma's waarmee u het volgende kunt doen:

-   **Postuur versterking van beveiliging**: Security Center beoordeling van uw omgeving en stelt u in staat om de status van uw resources te begrijpen en te bepalen of ze veilig zijn.

-   **Beveiligen tegen bedreigingen**: met Security Center worden uw workloads beoordeeld en worden aanbevelingen voor het voor komen van bedreigingen en waarschuwingen voor detectie van bedreigingen gegenereerd.

-   Snel **veilig**werken: in Security Center wordt alles uitgevoerd in de Cloud snelheid. Omdat Security Center in het eigen systeem is geïntegreerd, is het eenvoudig te implementeren en kan het u automatische inrichting en beveiliging bieden met Azure-services.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="architecture"></a>Architectuur

Omdat Security Center standaard deel uitmaakt van Azure, worden PaaS-services in Azure, zoals Service Fabric, SQL-databases en opslagaccounts, gecontroleerd en beveiligd door Security Center zonder dat er een implementatie aan te pas komt.

Security Center beveiligt bovendien andere servers dan Azure-servers en virtuele machines in de cloud of on-premises, voor zowel Windows- als Linux-servers, door hierop de Microsoft Monitoring Agent te installeren. Azure-VM's worden automatisch ingericht in Security Center.

De gebeurtenissen die worden verzameld van de agents en van Azure worden aan elkaar gerelateerd in de engine voor beveiligingsanalyses. Op basis hiervan worden u aanbevelingen op maat (beveiligingstaken), die u moet opvolgen om ervoor te zorgen dat uw workloads veilig zijn, en waarschuwingen bij de detectie van bedreigingen geboden. U moet zulke waarschuwingen zo snel mogelijk onderzoeken om ervoor te zorgen dat uw workloads niet het doelwit worden van schadelijke aanvallen.

Wanneer u Security Center inschakelt, wordt het beveiligingsbeleid dat is ingebouwd in Security Center in Azure Policy als een ingebouwd initiatief onder de Security Center-categorie weergegeven. Het ingebouwde initiatief wordt automatisch toegewezen aan alle abonnementen die bij Security Center zijn geregistreerd (gratis of Standard-categorie). Het ingebouwde initiatief bevat alleen controlebeleid. Zie [Werken met beveiligingsbeleid](tutorial-security-policy.md) voor meer informatie over Security Center-beleid in Azure Policy.

## <a name="strengthen-security-posture"></a>Beveiligingspostuur verbeteren

Azure Security Center helpt u bij het verbeteren van de beveiligingsstatus. Dit houdt in dat u met Azure Security Center de beveiligingstaken kunt vaststellen en uitvoeren die worden aanbevolen als best practices voor de beveiliging en deze kunt implementeren op uw machines, en in uw gegevensservices en apps. Het houdt ook in dat uw beveiligingsbeleid wordt beheerd en afgedwongen, en dat ervoor wordt gezorgd dat de beleidsregels worden nageleefd op uw Azure-VM's, andere servers dan Azure-servers en in uw Azure PaaS-services. Security Center biedt u de hulpprogramma's waarmee u een totaaloverzicht hebt van de workloads en waarmee u de netwerkbeveiligingsomgeving goed in de gaten kunt houden. 

### <a name="manage-organization-security-policy-and-compliance"></a>Het beveiligingsbeleid en de naleving voor een organisatie beheren

Voor een goede beveiliging moet u natuurlijk weten of de workloads veilig zijn en deze hierop controleren. Hiervoor past u een beveiligingsbeleid toe dat op uw organisatie is afgestemd. Omdat alle beleids regels in Security Center boven op de besturings elementen van Azure-beleid zijn gebouwd, krijgt u het volledige bereik en de flexibiliteit van een **beleids oplossing van wereld klasse**. U kunt in Security Center uw beleid zo instellen dat dit wordt uitgevoerd in beheergroepen, in verschillende abonnementen en zelfs voor een hele tenant.

![Dashboard van Security Center](media/security-center-intro/sc-dashboard.png)

Security Center helpt u bij **het identificeren van schaduw-it-abonnementen**. Wanneer u in uw dashboard abonnementen ziet met het label **valt niet onder beleid**, weet u meteen wanneer er recent gemaakte abonnementen zijn en kunt u ervoor zorgen dat deze onder uw beleid vallen en door Azure Security Center worden beveiligd.

![Beleidsdashboard van Security Center](media/security-center-intro/sc-policy-dashboard.png)

Met de geavanceerde bewakings mogelijkheden in Security Center kunt u ook naleving en beheer in de loop van de **tijd bijhouden en beheren**. De **algemene naleving** voorziet in een meting van de mate waarin uw abonnementen voldoen aan het beleid dat is gekoppeld aan uw werk belasting. 

![Security Center-beleid in de loop van de tijd](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Doorlopende beoordelingen

Security Center detecteert doorlopend nieuwe resources die voor uw workloads worden geïmplementeerd en beoordeelt of deze zijn geconfigureerd volgens de best practices voor de beveiliging. Als dit niet het geval is, worden ze gemarkeerd en ontvangt u een lijst met prioriteit die aanbevelingen bevat voor de problemen die moeten worden opgelost zodat uw machines kunnen worden beveiligd.

Een van de krach tigste hulpprogram ma's Security Center voorziet in het voortdurend controleren van de beveiligings status van uw netwerk is de **netwerk kaart**. In het overzicht kunt u de topologie van uw workloads bekijken, zodat u kunt zien of elk knooppunt goed is geconfigureerd. U kunt bekijken hoe uw knooppunten zijn verbonden, zodat u ongewenste verbindingen kunt blokkeren die een aanvaller in staat kunnen stellen om in uw netwerk in te breken.

![Security Center-netwerkoverzicht](media/security-center-intro/sc-net-map.png)

Security Center verkleint uw beveiligings waarschuwingen één stap eenvoudiger door een **beveiligde Score**toe te voegen. De beveiligingsscores worden nu gekoppeld aan elke aanbeveling die u ontvangt, zodat u weet hoe belangrijk elke aanbeveling is voor uw algehele beveiligingsstatus. Dit is van cruciaal belang bij het instellen van **de prioriteit van uw beveiligings werk**.

![Security Center-beveiligingsscore](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>De beveiliging optimaliseren en verbeteren door aanbevolen beheeropties te configureren

De kracht van Azure Security Center ligt in de aanbevelingen die het geeft. De aanbevelingen zijn afgestemd op de specifieke beveiligingsproblemen die in uw workloads worden aangetroffen. Security Center doet voor u het beveiligingsbeheer; niet alleen door uw beveiligingsproblemen op te sporen, maar ook door u van specifieke instructies te voorzien om deze problemen op te lossen.

![Security Center-aanbevelingen](media/security-center-intro/sc-recommendations.png)

Security Center stelt u op deze manier niet alleen in staat om het beveiligingsbeleid in te stellen, maar ook om veilige configuratienormen toe te passen voor uw resources.

Met de aanbevelingen kunt u de kwetsbaarheid voor aanvallen verminderen voor al uw resources. Dit geldt voor onder andere Azure-VM's, andere servers dan Azure-servers en Azure PaaS-services, zoals SQL- en Azure Storage-accounts. Hierbij wordt elk type resource anders beoordeeld en kent elk type eigen normen.

![Voorbeeld van een Security Center-aanbeveling](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Beschermen tegen bedreigingen

Met de beveiliging tegen bedreigingen van Security Center kunt u bedreigingen detecteren en voorkomen op de IaaS-laag (infrastructuur als een dienst), andere servers dan Azure-servers en PaaS-services (platform als een dienst) in Azure.

De beveiliging tegen bedreigingen van Security Center bevat een complete analyse van de aanvalsketen, waarbij automatisch waarschuwingen in uw omgeving aan elkaar worden gerelateerd op basis van de cyberaanvalsketenanalyse, zodat u een beter inzicht hebt in de hele geschiedenis van een aanval; waar deze is gestart en welke impact deze had op uw resources.



![Aanbevelingen voor aanvallen in Security Center](media/security-center-intro/sc-attack-recommendation.png)

### <a name="advanced-threat-protection"></a>Geavanceerde beveiliging tegen bedreigingen

Met Security Center krijgt u standaard systeemeigen integratie met Windows Defender Advanced Threat Protection. Dit betekent dat uw Windows-VM's en -servers zonder enige configuratie volledig zijn geïntegreerd met de aanbevelingen en beoordelingen van Security Center. Geavanceerde beveiliging tegen bedreigingen wordt ook standaard geboden voor Linux-VM's en -servers.

Bovendien kunt u met Security Center het beleid voor toepassingsbeheer automatiseren in serveromgevingen. Met de adaptieve toepassingsregelaars in Security Center kunt u end-to-end apps in een whitelist opnemen voor al uw Windows-servers. U hoeft geen regels te maken en op schendingen te controleren. Dit wordt allemaal automatisch voor u gedaan.

### <a name="protect-paas"></a>PaaS beveiligen

Security Center helpt u bij het detecteren van bedreigingen voor Azure PaaS-services. U kunt bedreigingen detecteren die zijn gericht op Azure-services, zoals Azure App Service, Azure SQL, Azure Storage Account en andere gegevensservices. U kunt ook gebruikmaken van de systeemeigen integratie met UEBA (User and Entity Behavioral Analytics) van Microsoft Cloud App Security om anomaliedetectie uit te voeren voor uw Azure-activiteitenlogboeken.

### <a name="block-brute-force-attacks"></a>Beveiligingsaanvallen blokkeren

Security Center zorgt ervoor dat u minder wordt blootgesteld aan beveiligingsaanvallen. Door de toegang tot poorten van virtuele machines te beperken met Just-In-Time-toegang tot VM's, kunt u de beveiliging van uw netwerk verbeteren door onnodige toegang te voorkomen. U kunt beleid voor beveiligde toegang instellen voor de geselecteerde poorten, voor alleen geautoriseerde gebruikers, toegestane bron-IP-adresbereiken of IP-adressen en voor een beperkt tijdbestek.

![Security Center en beveiligingsaanvallen](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Gegevensservices beveiligen

Security Center beschikt over mogelijkheden waarmee u de gegevens in Azure SQL automatisch kunt classificeren. U kunt ook beoordelingen krijgen van mogelijke beveiligingsproblemen in Azure SQL- en Azure Storage-services en aanbevelingen ontvangen voor de wijze waarop u deze kunt verhelpen.

### <a name="protect-iot-and-hybrid-cloud-workloads"></a>IoT-en hybride Cloud-workloads beveiligen

Azure Security Center voor IoT (Internet of Things) vereenvoudigt de bescherming van hybride werk belastingen door eenvormige zicht baarheid en controle, adaptieve preventie van bedreigingen en detectie van intelligente bedreigingen en respons te bieden voor werk belastingen die op Edge, on-premises worden uitgevoerd, in Azure en in andere Clouds. Zie [Azure Security Center voor IOT voor](https://docs.microsoft.com/azure/asc-for-iot/)meer informatie.

## <a name="get-secure-faster"></a>Uw infrastructuur sneller beveiligen

Systeem eigen Azure-integratie (inclusief Azure Policy-en Azure Monitor-Logboeken) in combi natie met naadloze integratie met andere micro soft-beveiligings oplossingen, zoals Microsoft Cloud App Security en Windows Defender Advanced Threat Protection helpen ervoor te zorgen dat uw de beveiligings oplossing is uitgebreid, maar ook eenvoudig te implementeren en uit te breiden.

Bovendien kunt u de volledige oplossing tot buiten Azure uitbreiden naar workloads die worden uitgevoerd in andere clouds en on-premises datacenters.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Automatisch Azure-resources detecteren en toevoegen

Security Center biedt naadloze en systeemeigen integratie met Azure en Azure-resources. Dit betekent dat u een volledige beveiligingsgeschiedenis kunt ophalen met betrekking tot Azure Policy en ingebouwd Security Center-beleid voor al uw Azure-resources en ervoor kunt zorgen dat alles automatisch wordt toegepast op recent gedetecteerde resources wanneer u deze in Azure maakt.

Uitgebreide logboekverzameling: logboeken van Windows en Linux worden gebruikt door de engine voor beveiligingsanalyses en geanalyseerd om aanbevelingen te doen en waarschuwingen te geven.

## <a name="next-steps"></a>Volgende stappen

- U moet over een Microsoft Azure-abonnement beschikken om met Security Center aan de slag te gaan. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/).
- De prijscategorie Gratis van Security Center wordt met uw Azure-abonnement ingeschakeld. Als u wilt profiteren van geavanceerd beveiligingsbeheer en mogelijkheden voor het detecteren van bedreigingen, moet u een upgrade uitvoeren naar de prijscategorie Standard. De prijscategorie Standard kan gratis worden uitgeprobeerd. Zie de [pagina met prijzen van Security Center](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.
- Als u nu Security Center Standard wilt inschakelen, raadpleegt u de [Quickstart: Onboard your Azure subscription to Security Center Standard](security-center-get-started.md) (Snelstartgids: uw Azure-abonnement registreren voor Security Center Standard) voor meer informatie.

