---
title: Plannings- en bedieningsgids voor het Beveiligingscentrum | Microsoft Docs
description: Dit document helpt u met uw planning voordat u overstapt op het Azure Beveiligingscentrum en bevat aandachtspunten voor het dagelijks gebruik.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: b731c5fe6b6c7055b7397386b1e9fd4bed47db8a
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910596"
---
# <a name="azure-security-center-planning-and-operations-guide"></a>Plannings- en bedieningsgids voor Azure Security Center
Deze hand leiding is voor IT-professionals (IT), IT-architecten, gegevens beveiligings analisten en Cloud beheerders plannen om Azure Security Center te gebruiken.


## <a name="planning-guide"></a>Planningsgids
In deze hand leiding worden de taken beschreven die u kunt volgen om uw gebruik van Security Center te optimaliseren op basis van de beveiligings vereisten van uw organisatie en het model voor Cloud beheer. Als u optimaal wilt profiteren van Security Center, is het belangrijk te begrijpen hoe verschillende personen of teams binnen uw organisatie de service gaan gebruiken om te voldoen aan alle vereisten voor veilige ontwikkeling en gebruik, bewaking, bestuur en reactie op incidenten. De belangrijkste gebieden waarmee u rekening moet houden bij het plannen van het gebruik van Security Center zijn:

* Beveiligingsrollen en toegangsbeheer
* Beveiligingsbeleid en aanbevelingen
* Gegevensverzameling en -opslag
* Continue niet-Azure-resources
* Continue beveiligingsbewaking
* Reageren op incidenten

In de volgende sectie leert u hoe u voor elk van deze gebieden een planning maakt en hoe u deze aanbevelingen toepast op grond van uw vereisten.


> [!NOTE]
> Raadpleeg de [Azure Security Center frequently asked questions (Veelgestelde vragen over het Azure Beveiligingscentrum)](security-center-faq.md) voor een lijst met veelgestelde vragen die ook nuttig kunnen zijn tijdens de ontwerp- en planningsfase.
>

## <a name="security-roles-and-access-controls"></a>Beveiligingsrollen en toegangsbeheer
Afhankelijk van de grootte en de structuur van uw organisatie kunnen meerdere individuen en teams het Beveiligingscentrum gebruiken om verschillende beveiligingstaken uit te voeren. In het volgende diagram vindt u een voorbeeld met fictieve personen en hun respectieve rollen en beveiligingsverantwoordelijkheden:

![Rollen](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Met Security Center kunnen deze personen voldoen aan deze verschillende verantwoordelijkheden. Bijvoorbeeld:

**Jeff (eigenaar van workload)**

* Een cloud-workload en de bijbehorende resources beheren
* Verantwoordelijk voor het implementeren en onderhouden van beveiliging volgens het beveiligingsbeleid van het bedrijf

**Ellen (CISO/CIO)**

* Verantwoordelijk voor alle beveiligingsaspecten van het bedrijf
* Wil de beveiliging van het bedrijf in alle cloud-workloads begrijpen
* Moet worden geïnformeerd over belangrijke aanvallen en risico 's

**David (IT-beveiliging)**

* Stelt beveiligingsbeleid in om er zeker van te zijn dat de juiste beveiliging aanwezig is
* Bewaakt naleving van beleid
* Genereert rapporten voor leidinggevenden of auditors

**Judy (beveiligingsbewerkingen)**

* Bewaakt en reageert 24/7 op beveiligingswaarschuwingen
* Escaleert naar eigenaar cloud-workload of IT-beveiligingsanalist

**Sam (beveiligingsanalist)**

* Onderzoekt aanvallen
* Werkt samen met de eigenaar van de workload in de cloud om herstelstappen toe te passen

Security Center gebruikt [op rollen gebaseerd toegangsbeheer (RBAC)](../role-based-access-control/role-assignments-portal.md), dat [ingebouwde rollen](../role-based-access-control/built-in-roles.md) biedt die kunnen worden toegewezen aan gebruikers, groepen en services in Azure. Wanneer gebruikers Security Center openen, zien ze alleen informatie met betrekking tot resources waartoe ze toegang hebben. Dit betekent dat aan de gebruiker de rol van de eigenaar, bijdrager of lezer is toegewezen voor het abonnement of de resourcegroep waartoe een resource behoort. Naast deze rollen zijn er twee specifieke Security Center-rollen:

- **Beveiligingslezer**: een gebruiker die deel uitmaakt van deze rol kan alleen configuraties van Security Center bekijken, en kan dus onder andere aanbevelingen, waarschuwingen, beleid en statusinformatie bekijken, maar kan geen wijzigingen aanbrengen.
- **Beveiligingsbeheerder**: dezelfde machtigingen als de rol Beveiligingslezer, maar kan ook het beveiligingsbeleid bijwerken, en aanbevelingen en waarschuwingen verwijderen.

De hierboven beschreven Security Center-rollen hebben geen toegang tot andere servicegebieden van Azure, zoals Storage, Web & Mobile of Internet of Things.

Wanneer gebruik wordt gemaakt van de personen in het vorige diagram, zou de volgende RBAC nodig zijn:

**Jeff (eigenaar van workload)**

* Eigenaar/bijdrager van resource groep

**David (IT-beveiliging)**

* Abonnements eigenaar/mede werker of beveiligings beheerder

**Judy (beveiligingsbewerkingen)**

* Abonnementslezer of beveiligingslezer voor het bekijken van waarschuwingen
* Abonnements eigenaar/mede werker of beveiligings beheerder vereist voor het negeren van waarschuwingen

**Sam (beveiligingsanalist)**

* Abonnementslezer voor het bekijken van waarschuwingen
* Abonnements eigenaar/mede werker die vereist is voor het negeren van waarschuwingen
* Toegang tot de werkruimte is mogelijk vereist

Andere belangrijke informatie om rekening mee te houden:

* Alleen abonnementseigenaren/medewerkers en beveiligingsbeheerders kunnen een beveiligingsbeleid bewerken.
* Alleen abonnements- en resourcegroepeigenaren en bijdragers kunnen beveiligingsaanbevelingen voor een resource toepassen.

Bij het plannen van het toegangsbeheer met het RBAC voor Security Center moet u zeker weten wie in uw organisatie het Security Center gaat gebruiken. Ook moet u weten welke typen taken zij gaan uitvoeren en RBAC vervolgens dienovereenkomstig configureren.

> [!NOTE]
> We raden u aan de rol toe te wijzen die gebruikers minimaal nodig hebben om hun taken uit te voeren. Wijs bijvoorbeeld gebruikers die alleen informatie hoeven te bekijken over de beveiligingstoestand van resources, maar geen maatregelen hoeven te nemen zoals het toepassen van aanbevelingen of het bewerken van beleid, de rol Lezer toe.
>
>

## <a name="security-policies-and-recommendations"></a>Beveiligingsbeleid en aanbevelingen
Een beveiligingsbeleid definieert de gewenste configuratie van uw workloads en helpt ervoor te zorgen dat aan de beveiligingsvereisten van het bedrijf of aan regelgeving wordt voldaan. In Security Center kunt u beleidsregels definiëren voor uw Azure-abonnementen, die kunnen worden afgestemd op het type workload of de vertrouwelijkheid van gegevens.

Beleidsregels van Security Center bevatten de volgende onderdelen:
- [Gegevensverzameling](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection): instellingen voor configuratie van agent en verzamelen van gegevens.
- [Beveiligings beleid](https://docs.microsoft.com/azure/security-center/security-center-policies): een [Azure Policy](../governance/policy/overview.md) dat bepaalt welke besturings elementen worden bewaakt en aanbevolen door Security Center, of gebruik Azure Policy om nieuwe definities te maken, aanvullende beleids regels te definiëren en beleid toe te wijzen aan verschillende beheer groepen.
- [E-mailmeldingen](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details): instellingen voor contactpersonen en meldingen voor beveiliging.
- [Prijscategorie](https://docs.microsoft.com/azure/security-center/security-center-pricing): keuze uit de prijscategorie Gratis of Standaard, die bepaalt welke functies van Security Center beschikbaar zijn voor resources binnen het bereik (kan worden opgegeven voor abonnementen, resourcegroepen en werkruimten).

> [!NOTE]
> Het opgeven van een contactpersoon voor beveiliging zorgt ervoor dat Azure de juiste persoon in uw organisatie kan bereiken als er zich een beveiligingsincident voordoet. Lees [Contactgegevens voor beveiliging verstrekken in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) voor meer informatie over het inschakelen van deze mogelijkheid.

### <a name="security-policies-definitions-and-recommendations"></a>Definities en aanbevelingen van beveiligingsbeleid
In Security Center wordt voor elk van uw Azure-abonnementen automatisch een standaardbeveiligingsbeleid gemaakt. U kunt het beleid bewerken in Security Center of Azure Policy gebruiken voor het maken van nieuwe definities, het definiëren van extra beleidsregels en het toewijzen van beleidsregels binnen beheergroepen (die de hele organisatie, een bedrijfseenheid, enzovoort kan vertegenwoordigen), en naleving van deze beleidsregels bewaken binnen deze bereiken.

Voordat u beleidsregels voor veiligheid configureert, moet u elk van de [aanbevelingen voor beveiliging](https://docs.microsoft.com/azure/security-center/security-center-recommendations) controleren en bepalen of deze beleidsregels geschikt zijn voor uw verschillende abonnementen en resourcegroepen. Het is ook belangrijk om te begrijpen welke actie moet worden ondernomen om aan de slag te gaan met beveiligingsaanbevelingen en wie in uw organisatie verantwoordelijk is voor het controleren op nieuwe aanbevelingen en het nemen van de benodigde stappen.

## <a name="data-collection-and-storage"></a>Gegevensverzameling en -opslag
Azure Security Center maakt gebruik van micro soft monitoring agent. Dit is dezelfde agent die wordt gebruikt door de Azure Monitor-service: om beveiligings gegevens van uw virtuele machines te verzamelen. [Gegevens die worden verzameld](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) van deze agent worden opgeslagen in uw Log Analytics-werkruimte(n).

### <a name="agent"></a>Agent

Als automatisch inrichten is ingeschakeld in het beveiligingsbeleid, wordt de Microsoft Monitoring Agent (voor [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) of [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)) geïnstalleerd op alle ondersteunde virtuele machines van Azure en op nieuwe virtuele machines die worden gemaakt. Als de Microsoft Monitoring Agent al op de virtuele machine of computer is geïnstalleerd, maakt Azure Security Center gebruik van de huidige geïnstalleerde agent. Het proces van de agent is ontworpen om niet-invasief te zijn en minimale invloed te hebben op de prestaties van de virtuele machine.

De Microsoft Monitoring Agent voor Windows vereist TCP-poort 443. Zie de [Handleiding voor het oplossen van problemen met Azure Security Center](security-center-troubleshooting-guide.md) voor meer informatie.

Als u gegevensverzameling op een bepaald moment wilt uitschakelen, kunt u dat doen in het beveiligingsbeleid. Omdat de micro soft monitoring agent echter door andere Azure-beheer-en bewakings Services kan worden gebruikt, wordt de agent niet automatisch verwijderd wanneer u het verzamelen van gegevens in Security Center uitschakelt. U kunt de agent indien nodig echter handmatig verwijderen.

> [!NOTE]
> Lees de [veelgestelde vragen over Azure Security Center](security-center-faq.md) (Engelstalig) als u een lijst met ondersteunde virtuele machines nodig hebt.
>

### <a name="workspace"></a>Werkruimte

Een werkruimte is een Azure-resource die als een container voor gegevens fungeert. U of andere leden van uw organisatie kunnen meerdere werkruimten gebruiken om verschillende gegevenssets te beheren die worden verzameld uit de gehele of delen van uw IT-infrastructuur.

Gegevens die (namens Azure Security Center) via de Microsoft Monitoring Agent worden verzameld, worden opgeslagen in een bestaande Log Analytics-werkruimte die is gekoppeld aan uw Azure-abonnement, of in een nieuwe werkruimte, rekening houdend met de geolocatie van de virtuele machine.

In Azure Portal kunt u bladeren om een overzicht te zien van uw Log Analytics-werkruimten, inclusief alle werkruimten die door Azure Security Center zijn gemaakt. Er wordt een gerelateerde resourcegroep gemaakt voor nieuwe werkruimten. Beide volgen deze naamconventie:

* Werk ruimte *DefaultWorkspace-[abonnement-ID]-[geo]*
* Resourcegroep: *DefaultResourceGroup-[geo]*

Voor werkruimten die zijn gemaakt door Azure Security Center worden gegevens 30 dagen bewaard. Voor bestaande werkruimten is de bewaarperiode gebaseerd op de prijscategorie van de werkruimte. Als u wilt, kunt u ook een bestaande werkruimte gebruiken.

> [!NOTE]
> Microsoft doet er alles aan om de privacy van gegevens te beschermen en deze gegevens te beveiligen. Microsoft voldoet aan strikte nalevings- en beveiligingsrichtlijnen - van het schrijven van code tot de uitvoering van een service. Lees [Gegevensbeveiliging van Azure Security Center](security-center-data-security.md) voor meer informatie over de verwerking van gegevens en privacy.
>

## <a name="onboarding-non-azure-resources"></a>Onboarding van niet-Azure-resources

Security Center kan de beveiligingsstatus van uw niet-Azure-computers controleren, maar u moet deze resources dan eerst onboarden. Lees [Onboarding to Azure Security Center Standard for enhanced security](https://docs.microsoft.com/azure/security-center/security-center-onboarding#onboard-non-azure-computers) (Onboarding bij Azure Security Center Standard voor een betere beveiliging) voor meer informatie over het onboarden van niet-Azure-resources.

## <a name="ongoing-security-monitoring"></a>Continue beveiligingsbewaking
Na de eerste configuratie en toepassing van aanbevelingen voor Security Center, komen in de volgende stap de operationele processen voor Security Center ter sprake.

In het Security Center-overzicht ziet u beknopte informatie over de beveiliging van alle Azure-resources en van eventuele niet-Azure-resources die u hebt gekoppeld. In het onderstaande voorbeeld ziet u een omgeving waarin heel wat problemen moeten worden opgelost:

![dashboard](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Security Center heeft geen invloed op uw normale operationele procedures. Het bewaakt uw implementaties passief en doet aanbevelingen op grond van het beveiligingsbeleid dat u hebt ingeschakeld.

Wanneer u Security Center voor het eerst inschakelt voor uw huidige Azure-omgeving, moet u alle aanbevelingen doornemen. Dit kunt u doen op de tegel **Aanbevelingen** of per resource (**Compute**, **Netwerken**, **Opslag en gegevens** en **Toepassingen**).

Zodra u alle aanbevelingen hebt toegepast, wordt de sectie **Preventie** groen voor alle toegepaste resources. Continue bewaking wordt op dit punt eenvoudiger omdat u alleen acties onderneemt op basis van wijzigingen in de resourcebeveiligingsstatus en aanbevelingstegels.

De sectie **Detectie** is meer reactief en betreft waarschuwingen over problemen die nu plaatsvinden of hebben plaatsgevonden in het verleden en zijn gedetecteerd bij controles door Security Center of door systemen van derden. Met de tegel Beveiligingswaarschuwingen wordt een staafdiagram weergegeven dat het aantal dagelijks geconstateerde waarschuwingen voor gedetecteerde bedreigingen en de verdeling ervan over verschillende ernstigheidscategorieën (laag, gemiddeld, hoog) aangeeft. Lees voor meer informatie over beveiligingswaarschuwingen [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md).

Het is een goed idee om de functie [Bedreigingsinformatie](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) iedere dag even te bekijken. U daar bedreigingen van de omgeving identificeren, bijvoorbeeld dat een bepaalde computer deel uitmaakt van een botnet.

### <a name="monitoring-for-new-or-changed-resources"></a>Bewaking voor nieuwe of gewijzigde resources
De meeste Azure-omgevingen zijn dynamisch, met nieuwe resources die regelmatig omhoog en omlaag worden verplaatst, met configuraties of wijzigingen enz. Security Center zorgt ervoor dat de beveiligingsstatus van deze nieuwe resources voor u goed zichtbaar is.

Wanneer u nieuwe resources (virtuele machines, SQL Databases) toevoegt aan uw Azure-omgeving, detecteert Security Center deze resources automatisch en begint het de beveiliging ervan te bewaken. Dit omvat ook PaaS-webrollen en -werkrollen. Als gegevensverzameling wordt ingeschakeld in het [beveiligingsbeleid](tutorial-security-policy.md), worden er automatisch extra bewakingsmogelijkheden ingeschakeld voor uw virtuele machines.

![Belangrijke gebieden](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. Voor virtuele machines klikt u op **Compute**, in het gedeelte **Preventie**. Problemen bij het inschakelen van gegevensverzameling of verwante aanbevelingen worden weergegeven op het tabblad **Overzicht**, in het gedeelte **Aanbevelingen ten aanzien van controle**.
2. Geef de **aanbevelingen** weer om te zien welke beveiligingsrisico's mogelijk zijn geïdentificeerd voor de nieuwe resource.
3. Het is heel gebruikelijk dat wanneer nieuwe virtuele machines worden toegevoegd aan uw omgeving, eerst alleen het besturingssysteem wordt geïnstalleerd. De resource-eigenaar heeft wellicht enige tijd nodig om andere apps te implementeren die door deze VM’s worden gebruikt.  In het ideale geval moet u de uiteindelijke doelstelling van deze workload kennen. Wordt het een toepassingsserver? Op grond van wat deze nieuwe workload gaat worden, kunt u het juiste **beveiligingsbeleid** inschakelen. Dit is de derde stap in deze werkstroom.
4. Wanneer er nieuwe resources worden toegevoegd aan uw Azure-omgeving, kunnen er nieuwe waarschuwingen worden weer gegeven in de tegel **beveiligings waarschuwingen** . Zoek naar nieuwe waarschuwingen in deze tegel en volg de aanbevelingen.

U moet ook regel matig bestaande resources controleren op wijzigingen in de configuratie waarvoor beveiligings Risico's zijn ontstaan, van aanbevolen basis lijnen en beveiligings waarschuwingen. Begin bij het dashboard van Security Center. Daar hebt u drie belang rijke gebieden die consistent kunnen worden beoordeeld.

![Bewerkingen](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. Het venster **Preventie** biedt snel toegang tot uw belangrijkste resources. Gebruik deze optie voor het bewaken van Compute, Netwerken, Opslag en gegevens en Toepassingen.
2. In het venster **Aanbevelingen** kunt u de aanbevelingen van Security Center controleren. Tijdens de continue bewaking is het mogelijk dat u niet dagelijks aanbevelingen hebt. Dit is normaal omdat u alle aanbevelingen voor de eerste Security Center-installatie hebt behandeld. Daarom bevat deze sectie niet elke dag nieuwe informatie en hoeft u deze alleen te raadplegen wanneer dat nodig is.
3. Het venster **Detectie** kan ofwel heel vaak, of zeer incidenteel veranderen. Controleer altijd uw beveiligingswaarschuwingen en neem maatregelen op grond van de aanbevelingen van Security Center.

### <a name="hardening-access-and-applications"></a>Toegang en toepassingen beperken

Als onderdeel van uw beveiligingsbeleid moet u preventieve maatregelen nemen om de toegang tot virtuele machines te beperken en te bepalen welke toepassingen mogen worden uitgevoerd op virtuele machines. Door beperkingen in te stellen voor het inkomende verkeer naar uw VM's in Azure, vermindert u de blootstelling aan aanvallen en kunnen gebruikers tegelijkertijd eenvoudig verbinding maken met virtuele machines wanneer dit nodig is. Gebruik de functie [Just-In-Time-VM-toegang](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) om de toegang tot uw virtuele machines te beperken.

U kunt [adaptieve toepassings besturings elementen](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) gebruiken om te beperken welke toepassingen kunnen worden uitgevoerd op uw virtuele machines die zich in azure bevinden. Met andere voor delen kunt u uw Vm's beschermen tegen malware. Security Center analyseert processen die worden uitgevoerd in de VM met behulp van machine learning, om u te helpen bij het maken van white list-regels.


## <a name="incident-response"></a>Reageren op incidenten
Security Center detecteert en waarschuwt voor bedreigingen wanneer deze optreden. Organisaties moeten controleren op nieuwe beveiligingswaarschuwingen en zo nodig maatregelen nemen om het probleem verder te onderzoeken of de aanval af te weren. Lees [hoe Azure Security Center detecteert en reageert op bedreigingen](security-center-alerts-overview.md#detect-threats)voor meer informatie over de werking van Security Center detectie van bedreigingen.

Hoewel dit artikel niet de bedoeling heeft om u te helpen uw eigen plan voor het reageren op incidenten te maken, maken we gebruik van Microsoft Azure Security Response in de cloud-levenscyclus als de basis voor fasen voor het reageren op incidenten. In het volgende diagram ziet u de fasen:

![Verdachte activiteiten](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> U kunt de [Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) van het National Institute of Standards and Technology (NIST) gebruiken als richtlijn om uw eigen plan te ontwikkelen.
>

U kunt waarschuwingen van Security Center gebruiken tijdens de volgende fasen:

* **Detecteren**: een verdachte activiteit in een of meer resources identificeren.
* **Beoordelen**: de eerste beoordeling uitvoeren voor meer informatie over de verdachte activiteiten.
* **Diagnose uitvoeren**: de herstelstappen gebruiken voor het uitvoeren van de technische procedure om het probleem op te lossen.

Elke beveiligingswaarschuwing bevat informatie die kan worden gebruikt om beter te begrijpen wat de aard van de aanval is en om mogelijke oplossingen voor te stellen. Sommige waarschuwingen bevatten ook koppelingen naar meer informatie of naar andere informatiebronnen binnen Azure. U kunt de weergegeven informatie gebruiken voor verder onderzoek en om te beginnen met risicobeperking. Bovendien kunt u zoeken in beveiligingsgegevens die zijn opgeslagen in uw werkruimte.

Het volgende voorbeeld betreft een verdachte RDP-activiteit die op dat moment plaatsvindt:

![Verdachte activiteiten](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Deze pagina bevat informatie over het tijdstip waarop de aanval plaatsvond, de hostnaam van de bron, de VM die het doelwit was en aanbevolen maatregelen. In sommige gevallen kan de bron informatie van de aanval leeg zijn. Lees [Missing Source Information in Azure Security Center Alerts](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) voor meer informatie over dit type gedrag.

Vanaf deze pagina kunt u ook een [onderzoek](https://docs.microsoft.com/azure/security-center/security-center-investigation) starten om de tijdlijn van de aanval beter te begrijpen, een goed beeld te krijgen van de aanval, te zien welke systemen er mogelijk in gevaar zijn en welke referenties er zijn gebruikt. Daarnaast kunt u een grafische weergave bekijken van de hele aanvalsketen.

Wanneer u het geïnfecteerde systeem hebt geïdentificeerd, kunt u [beveiligingsplaybooks](https://docs.microsoft.com/azure/security-center/security-center-playbooks) uitvoeren die eerder zijn gemaakt. Beveiligingsplaybooks bestaan uit een verzameling procedures die in Security Center worden uitgevoerd nadat een bepaalde playbook is geactiveerd door de geselecteerde waarschuwing.

In de video [over het gebruik van de Azure Security Center & Microsoft Operations Management Suite voor een reactie op incidenten](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) , ziet u enkele demonstraties die u kunnen helpen om te begrijpen hoe Security Center in elk van deze fasen kan worden gebruikt.

> [!NOTE]
> Lees de [beveiligings waarschuwingen beheren en erop reageren in azure Security Center](security-center-managing-and-responding-alerts.md) voor meer informatie over het gebruik van Security Center mogelijkheden om u te helpen tijdens het respons proces van uw incident.
>
>

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u kunnen lezen hoe u een planning kunt maken voor het overstappen op Security Center. Zie de volgende onderwerpen voor meer informatie over het Beveiligingscentrum:

* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md): meer informatie over het bewaken van de status van uw Azure-resources.
* [Partneroplossingen bewaken met Azure Security Center](security-center-partner-solutions.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt bewaken.
* [Azure Security Center FAQ](security-center-faq.md): raadpleeg veelgestelde vragen over het gebruik van de service.
* [Azure-beveiligingsblog](https://blogs.msdn.com/b/azuresecurity/): lees blogberichten over de beveiliging en naleving van Azure.
