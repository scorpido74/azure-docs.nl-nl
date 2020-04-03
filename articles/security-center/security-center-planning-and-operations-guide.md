---
title: Handleiding voor planning en bedrijfsvoering van het beveiligingscentrum
description: Dit document helpt u met uw planning voordat u overstapt op het Azure Beveiligingscentrum en bevat aandachtspunten voor het dagelijks gebruik.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 8244a0f164c8578bf9f79e4b66beb529b6a15f67
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586002"
---
# <a name="planning-and-operations-guide"></a>Gids planning en bedrijfsvoering
Deze handleiding is voor IT-professionals, IT-architecten, informatiebeveiligingsanalisten en cloudbeheerders die van plan zijn Azure Security Center te gebruiken.


## <a name="planning-guide"></a>Planningsgids
Deze handleiding bevat taken die u volgen om uw gebruik van Security Center te optimaliseren op basis van de beveiligingsvereisten en het cloudbeheermodel van uw organisatie. Als u optimaal wilt profiteren van Security Center, is het belangrijk te begrijpen hoe verschillende personen of teams binnen uw organisatie de service gaan gebruiken om te voldoen aan alle vereisten voor veilige ontwikkeling en gebruik, bewaking, bestuur en reactie op incidenten. De belangrijkste gebieden waarmee u rekening moet houden bij het plannen van het gebruik van Security Center zijn:

* Beveiligingsrollen en toegangsbeheer
* Beveiligingsbeleid en aanbevelingen
* Gegevensverzameling en -opslag
* Continue niet-Azure-resources
* Continue beveiligingsbewaking
* Reageren op incidenten

In de volgende sectie leert u hoe u voor elk van deze gebieden een planning maakt en hoe u deze aanbevelingen toepast op grond van uw vereisten.


> [!NOTE]
> Raadpleeg de [Azure Security Center frequently asked questions (Veelgestelde vragen over het Azure Beveiligingscentrum)](faq-general.md) voor een lijst met veelgestelde vragen die ook nuttig kunnen zijn tijdens de ontwerp- en planningsfase.

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

* Eigenaar/bijdrager resourcegroep

**Ellen (CISO/CIO)**

* Eigenaar van een abonnement/inzender of beveiligingsbeheerder

**David (IT-beveiliging)**

* Eigenaar van een abonnement/inzender of beveiligingsbeheerder

**Judy (beveiligingsbewerkingen)**

* Abonnementslezer of beveiligingslezer voor het bekijken van waarschuwingen
* Eigenaar/inzender van een abonnement of beveiligingsbeheerder vereist om waarschuwingen te verwijderen

**Sam (beveiligingsanalist)**

* Abonnementslezer voor het bekijken van waarschuwingen
* Eigenaar/bijdrager abonnement verplicht om waarschuwingen te verwijderen
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
- [Beveiligingsbeleid:](https://docs.microsoft.com/azure/security-center/security-center-policies)een [Azure-beleid](../governance/policy/overview.md) dat bepaalt welke besturingselementen worden gecontroleerd en aanbevolen door Security Center, of Azure Policy gebruiken om nieuwe definities te maken, aanvullende beleidsregels te definiëren en beleid toe te wijzen voor beheergroepen.
- [E-mailmeldingen](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details): instellingen voor contactpersonen en meldingen voor beveiliging.
- [Prijscategorie](https://docs.microsoft.com/azure/security-center/security-center-pricing): keuze uit de prijscategorie Gratis of Standaard, die bepaalt welke functies van Security Center beschikbaar zijn voor resources binnen het bereik (kan worden opgegeven voor abonnementen, resourcegroepen en werkruimten).

> [!NOTE]
> Het opgeven van een contactpersoon voor beveiliging zorgt ervoor dat Azure de juiste persoon in uw organisatie kan bereiken als er zich een beveiligingsincident voordoet. Lees [Contactgegevens voor beveiliging verstrekken in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) voor meer informatie over het inschakelen van deze mogelijkheid.

### <a name="security-policies-definitions-and-recommendations"></a>Definities en aanbevelingen van beveiligingsbeleid
In Security Center wordt voor elk van uw Azure-abonnementen automatisch een standaardbeveiligingsbeleid gemaakt. U kunt het beleid bewerken in Security Center of Azure Policy gebruiken voor het maken van nieuwe definities, het definiëren van extra beleidsregels en het toewijzen van beleidsregels binnen beheergroepen (die de hele organisatie, een bedrijfseenheid, enzovoort kan vertegenwoordigen), en naleving van deze beleidsregels bewaken binnen deze bereiken.

Voordat u beleidsregels voor veiligheid configureert, moet u elk van de [aanbevelingen voor beveiliging](https://docs.microsoft.com/azure/security-center/security-center-recommendations) controleren en bepalen of deze beleidsregels geschikt zijn voor uw verschillende abonnementen en resourcegroepen. Het is ook belangrijk om te begrijpen welke actie moet worden ondernomen om aan de slag te gaan met beveiligingsaanbevelingen en wie in uw organisatie verantwoordelijk is voor het controleren op nieuwe aanbevelingen en het nemen van de benodigde stappen.

## <a name="data-collection-and-storage"></a>Gegevensverzameling en -opslag
Azure Security Center gebruikt de Log Analytics-agent – dit is dezelfde agent die wordt gebruikt door de Azure Monitor-service – om beveiligingsgegevens van uw virtuele machines te verzamelen. [Gegevens die worden verzameld](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) van deze agent worden opgeslagen in uw Log Analytics-werkruimte(n).

### <a name="agent"></a>Agent

Wanneer automatische inrichting is ingeschakeld in het beveiligingsbeleid, wordt de agent Log Analytics (voor [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) of [Linux)](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)geïnstalleerd op alle ondersteunde Azure VM's en alle nieuwe die zijn gemaakt. Als de VM of computer de loganalytics-agent al heeft geïnstalleerd, maakt Azure Security Center gebruik van de huidige geïnstalleerde agent. Het proces van de agent is ontworpen om niet-invasief te zijn en heeft een zeer minimale impact op de VM-prestaties.

De Log Analytics-agent voor Windows vereist tcp-poort 443. Zie de [Handleiding voor het oplossen van problemen met Azure Security Center](security-center-troubleshooting-guide.md) voor meer informatie.

Als u gegevensverzameling op een bepaald moment wilt uitschakelen, kunt u dat doen in het beveiligingsbeleid. Omdat de Log Analytics-agent echter kan worden gebruikt door andere Azure-beheer- en bewakingsservices, wordt de agent niet automatisch verwijderd wanneer u het verzamelen van gegevens uitschakelt in security center. U kunt de agent indien nodig echter handmatig verwijderen.

> [!NOTE]
> Lees de [veelgestelde vragen over Azure Security Center](faq-vms.md) (Engelstalig) als u een lijst met ondersteunde virtuele machines nodig hebt.

### <a name="workspace"></a>Werkruimte

Een werkruimte is een Azure-resource die als een container voor gegevens fungeert. U of andere leden van uw organisatie kunnen meerdere werkruimten gebruiken om verschillende gegevenssets te beheren die worden verzameld uit de gehele of delen van uw IT-infrastructuur.

Gegevens die worden verzameld van de Log Analytics-agent (namens Azure Security Center) worden opgeslagen in een bestaande Log Analytics-werkruimte(s) die zijn gekoppeld aan uw Azure-abonnement of een nieuwe werkruimte(s), rekening houdend met de geo van de VM.

In Azure Portal kunt u bladeren om een overzicht te zien van uw Log Analytics-werkruimten, inclusief alle werkruimten die door Azure Security Center zijn gemaakt. Er wordt een gerelateerde resourcegroep gemaakt voor nieuwe werkruimten. Beide volgen deze naamconventie:

* Werkruimte: *DefaultWorkspace-[abonnements-id]-[geolocatie]*
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

De sectie **Detectie** is meer reactief en betreft waarschuwingen over problemen die nu plaatsvinden of hebben plaatsgevonden in het verleden en zijn gedetecteerd bij controles door Security Center of door systemen van derden. Op de tegel Beveiligingswaarschuwingen worden staafgrafieken weergegeven die het aantal waarschuwingen weergeven dat elke dag is gevonden en de verdeling ervan over de verschillende ernstcategorieën (laag, gemiddeld, hoog). Lees voor meer informatie over beveiligingswaarschuwingen [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md).

Het is een goed idee om de functie Bedreigingsinformatie iedere dag even te bekijken. U daar bedreigingen van de omgeving identificeren, bijvoorbeeld dat een bepaalde computer deel uitmaakt van een botnet.

### <a name="monitoring-for-new-or-changed-resources"></a>Bewaking voor nieuwe of gewijzigde resources
De meeste Azure-omgevingen zijn dynamisch, waarbij resources regelmatig worden gemaakt, omhoog of omlaag worden gesponnen, opnieuw worden geconfigureerd en gewijzigd. Security Center zorgt ervoor dat de beveiligingsstatus van deze nieuwe resources voor u goed zichtbaar is.

Wanneer u nieuwe resources (virtuele machines, SQL Databases) toevoegt aan uw Azure-omgeving, detecteert Security Center deze resources automatisch en begint het de beveiliging ervan te bewaken. Dit omvat ook PaaS-webrollen en -werkrollen. Als gegevensverzameling wordt ingeschakeld in het [beveiligingsbeleid](tutorial-security-policy.md), worden er automatisch extra bewakingsmogelijkheden ingeschakeld voor uw virtuele machines.

![Belangrijke gebieden](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. Klik voor virtuele machines op **Compute &-apps**onder de sectie **Resource Security Hygiene.** Problemen bij het inschakelen van gegevensverzameling of verwante aanbevelingen worden weergegeven op het tabblad **Overzicht**, in het gedeelte **Aanbevelingen ten aanzien van controle**.
2. Geef de **aanbevelingen** weer om te zien welke beveiligingsrisico's mogelijk zijn geïdentificeerd voor de nieuwe resource.
3. Het is heel gebruikelijk dat wanneer nieuwe virtuele machines worden toegevoegd aan uw omgeving, eerst alleen het besturingssysteem wordt geïnstalleerd. De resource-eigenaar heeft wellicht enige tijd nodig om andere apps te implementeren die door deze VM’s worden gebruikt.  In het ideale geval moet u de uiteindelijke doelstelling van deze workload kennen. Wordt het een toepassingsserver? Op grond van wat deze nieuwe workload gaat worden, kunt u het juiste **beveiligingsbeleid** inschakelen. Dit is de derde stap in deze werkstroom.
4. Als er nieuwe bronnen aan uw Azure-omgeving worden toegevoegd, kunnen er nieuwe waarschuwingen worden weergegeven in de tegel **Beveiligingswaarschuwingen.** Zoek naar nieuwe waarschuwingen in deze tegel en volg de aanbevelingen.

U moet ook regelmatig bestaande resources controleren op configuratiewijzigingen die beveiligingsrisico's, afwijking van aanbevolen basislijnen en beveiligingswaarschuwingen kunnen hebben veroorzaakt. Begin bij het dashboard van Security Center. Van daaruit heb je drie belangrijke gebieden te herzien op een consistente basis.

![Bewerkingen](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. Het venster **Preventie** biedt snel toegang tot uw belangrijkste resources. Gebruik deze optie voor het bewaken van Compute, Netwerken, Opslag en gegevens en Toepassingen.
2. In het venster **Aanbevelingen** kunt u de aanbevelingen van Security Center controleren. Tijdens uw voortdurende controle, u vinden dat u geen aanbevelingen op een dagelijkse basis hebt, wat normaal is aangezien u alle aanbevelingen op de aanvankelijke opstelling van het Veiligheidscentrum gericht. Daarom bevat deze sectie niet elke dag nieuwe informatie en hoeft u deze alleen te raadplegen wanneer dat nodig is.
3. Het venster **Detectie** kan ofwel heel vaak, of zeer incidenteel veranderen. Controleer altijd uw beveiligingswaarschuwingen en neem maatregelen op grond van de aanbevelingen van Security Center.

### <a name="hardening-access-and-applications"></a>Toegang en toepassingen beperken

Als onderdeel van uw beveiligingsbeleid moet u preventieve maatregelen nemen om de toegang tot virtuele machines te beperken en te bepalen welke toepassingen mogen worden uitgevoerd op virtuele machines. Door beperkingen in te stellen voor het inkomende verkeer naar uw VM's in Azure, vermindert u de blootstelling aan aanvallen en kunnen gebruikers tegelijkertijd eenvoudig verbinding maken met virtuele machines wanneer dit nodig is. Gebruik [de just-in-time VM-toegangsfunctie](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) om de toegang tot uw VM's te verharden.

U [Adaptive Application Controls](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) gebruiken om te beperken welke toepassingen kunnen worden uitgevoerd op uw VM's in Azure. Dit helpt onder andere om uw VM's te verharden tegen malware. Met behulp van machine learning analyseert Security Center processen die in de VM worden uitgevoerd om u te helpen whitelistingregels te maken.


## <a name="incident-response"></a>Reageren op incidenten
Security Center detecteert en waarschuwt voor bedreigingen wanneer deze optreden. Organisaties moeten controleren op nieuwe beveiligingswaarschuwingen en zo nodig maatregelen nemen om het probleem verder te onderzoeken of de aanval af te weren. Lees hoe Azure Security Center bedreigingen [detecteert en reageert](security-center-alerts-overview.md#detect-threats)voor meer informatie over hoe beveiligingsbeveiliging van beveiligingscentrum bedreigingen werkt.

Hoewel dit artikel niet de intentie heeft om u te helpen bij het maken van uw eigen Incident Response-abonnement, gaan we Microsoft Azure Security Response gebruiken in de cloudlevenscyclus als basis voor de fasen van incidentrespons. In het volgende diagram ziet u de fasen:

![Verdachte activiteit](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> U kunt de [Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) van het National Institute of Standards and Technology (NIST) gebruiken als richtlijn om uw eigen plan te ontwikkelen.
>

U kunt waarschuwingen van Security Center gebruiken tijdens de volgende fasen:

* **Detecteren**: een verdachte activiteit in een of meer resources identificeren.
* **Beoordelen**: de eerste beoordeling uitvoeren voor meer informatie over de verdachte activiteiten.
* **Diagnose uitvoeren**: de herstelstappen gebruiken voor het uitvoeren van de technische procedure om het probleem op te lossen.

Elke beveiligingswaarschuwing bevat informatie die kan worden gebruikt om beter te begrijpen wat de aard van de aanval is en om mogelijke oplossingen voor te stellen. Sommige waarschuwingen bevatten ook koppelingen naar meer informatie of naar andere informatiebronnen binnen Azure. U kunt de weergegeven informatie gebruiken voor verder onderzoek en om te beginnen met risicobeperking. Bovendien kunt u zoeken in beveiligingsgegevens die zijn opgeslagen in uw werkruimte.

Het volgende voorbeeld betreft een verdachte RDP-activiteit die op dat moment plaatsvindt:

![Verdachte activiteit](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Deze pagina bevat informatie over het tijdstip waarop de aanval plaatsvond, de hostnaam van de bron, de VM die het doelwit was en aanbevolen maatregelen. In sommige omstandigheden kan de broninformatie van de aanval leeg zijn. Lees [Missing Source Information in Azure Security Center Alerts](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) voor meer informatie over dit type gedrag.

Vanaf deze pagina kunt u ook een onderzoek starten om de tijdlijn van de aanval beter te begrijpen, een goed beeld te krijgen van de aanval, te zien welke systemen er mogelijk in gevaar zijn en welke referenties er zijn gebruikt. Daarnaast kunt u een grafische weergave bekijken van de hele aanvalsketen.

Zodra u het gecompromitteerde systeem hebt geïdentificeerd, u een [werkstroomautomatisering](workflow-automation.md) uitvoeren die eerder is gemaakt. Dit zijn een verzameling procedures die kunnen worden uitgevoerd vanuit Security Center zodra deze is geactiveerd door een waarschuwing.

In de video [Hoe u het Azure Security Center gebruiken & Microsoft Operations Management Suite voor een incidentresponsevideo,](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) u enkele demonstraties zien die u kunnen helpen te begrijpen hoe Beveiligingscentrum in elk van deze fasen kan worden gebruikt.

> [!NOTE]
> Lees [Beheren en reageren op beveiligingswaarschuwingen in Azure Security Center](security-center-managing-and-responding-alerts.md) voor meer informatie over het gebruik van beveiligingscentrummogelijkheden om u te helpen tijdens uw incidentresponsproces.
>
>

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u kunnen lezen hoe u een planning kunt maken voor het overstappen op Security Center. Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md): meer informatie over het bewaken van de status van uw Azure-resources.
* [Partneroplossingen bewaken met Azure Security Center](security-center-partner-solutions.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt bewaken.
* [Veelgestelde vragen over azure security center](faq-general.md) : zoek veelgestelde vragen over het gebruik van de service.
* [Azure Security-blog](https://blogs.msdn.com/b/azuresecurity/) : zoek blogberichten over Azure-beveiliging en -naleving.
