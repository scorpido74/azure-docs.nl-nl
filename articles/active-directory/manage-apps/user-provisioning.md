---
title: Automatische SaaS-app-gebruikers inrichten in azure AD | Microsoft Docs
description: Een inleiding tot het gebruik van Azure AD voor het automatisch inrichten, het inrichten en voortdurend bijwerken van gebruikers accounts in meerdere SaaS-toepassingen van derden.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f661aa67f04de23c7b4871e78d3628c639e7567
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144543"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Gebruikers inrichten en het ongedaan maken van de inrichting van toepassingen automatiseren met Azure Active Directory

In Azure Active Directory (Azure AD) verwijst de inrichting van de term- **app** naar het automatisch maken van gebruikers-id's en-rollen in de Cloud toepassingen ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) waartoe gebruikers toegang nodig hebben. Naast het maken van gebruikers identiteiten, omvat automatische inrichting het onderhoud en de verwijdering van gebruikers identiteiten als status of rollen worden gewijzigd. Veelvoorkomende scenario's zijn onder andere het inrichten van een Azure AD-gebruiker in toepassingen zoals [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), [Sales Force](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)en meer.

![Overzichts diagram van inrichting](media/user-provisioning/provisioning-overview.png)

Met deze functie kunt u:

- **Inrichting automatiseren**: automatisch nieuwe accounts maken in de juiste systemen voor nieuwe personen wanneer ze deel nemen aan uw team of organisatie.
- Ongedaan maken van de **inrichting automatiseren:** Accounts automatisch uitschakelen in de juiste systemen wanneer mensen het team of de organisatie verlaten.
- **Gegevens synchroniseren tussen systemen:** Zorg ervoor dat de identiteiten in uw apps en systemen up-to-date blijven op basis van wijzigingen in de map of uw personeels systeem.
- **Inrichtings groepen:** Richt groepen in voor toepassingen die deze ondersteunen.
- **Toegang beheren:** Bewaak en controleer wie is ingericht in uw toepassingen.
- **Naadloos implementeren in bruine veld scenario's:** Vergelijk bestaande identiteiten tussen systemen en sta eenvoudige integratie toe, zelfs wanneer gebruikers al bestaan in het doel systeem.
- **Uitgebreide aanpassing gebruiken:** Profiteer van aanpas bare kenmerk toewijzingen waarmee wordt gedefinieerd welke gebruikers gegevens moeten stromen van het bron systeem naar het doel systeem.
- **Ontvang waarschuwingen voor kritieke gebeurtenissen:** De inrichtings service biedt waarschuwingen voor kritieke gebeurtenissen en biedt Log Analytics integratie waarmee u aangepaste waarschuwingen kunt definiëren voor de behoeften van uw bedrijf.

## <a name="benefits-of-automatic-provisioning"></a>Voor delen van automatische inrichting

Naarmate het aantal toepassingen dat in moderne organisaties wordt gebruikt, blijft groeien, worden IT-beheerders op schaal gewerkt met toegangs beheer. Met standaarden zoals Security Assertion Markup Language (SAML) of Open ID Connect (OIDC) kunnen beheerders snel eenmalige aanmelding (SSO) instellen, maar in Access moeten gebruikers ook worden ingericht in de app. Bij veel beheerders betekent het inrichten dat elke week hand matig elk gebruikers account maakt of CSV-bestanden uploadt, maar deze processen zijn tijdrovend, duur en fout gevoelig. Oplossingen zoals SAML just-in-time (JIT) zijn goedgekeurd voor het automatiseren van het inrichten, maar ondernemingen hebben ook een oplossing nodig om gebruikers te deinrichten wanneer ze de organisatie verlaten of niet langer toegang tot bepaalde apps nodig hebben op basis van de wijziging van de rol.

Enkele veelvoorkomende motivaties voor het gebruik van automatische inrichting zijn onder andere:

- Maximale efficiëntie en nauw keurigheid van het inrichten van processen.
- Besparen op kosten die zijn gekoppeld aan het hosten en onderhouden van aangepast ontwikkelde inrichtings oplossingen en scripts.
- Beveilig uw organisatie door de identiteit van gebruikers direct te verwijderen uit Key SaaS-apps wanneer ze de organisatie verlaten.
- Eenvoudig een groot aantal gebruikers importeren in een bepaalde SaaS-toepassing of-systeem.
- Met één set beleids regels kunt u bepalen wie er is ingericht en wie zich kan aanmelden bij een app.

Azure AD-gebruikers inrichten kan helpen bij het oplossen van deze uitdagingen. Voor meer informatie over hoe klanten Azure AD-gebruikers inrichten gebruiken, kunt u de [ASOS](https://aka.ms/asoscasestudy)-casestudy lezen. In de onderstaande video vindt u een overzicht van het inrichten van gebruikers in azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Welke toepassingen en systemen kan ik gebruiken met automatische gebruikers inrichting van Azure AD?

Azure AD bevat vooraf geïntegreerde ondersteuning voor veel populaire SaaS-apps en Human Resources-systemen en algemene ondersteuning voor apps die specifieke onderdelen van de [SCIM 2,0-standaard](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)implementeren.

* **Vooraf geïntegreerde toepassingen (Galerie SaaS-apps)** . U kunt alle toepassingen vinden waarvoor Azure AD een vooraf geïntegreerde inrichtings connector ondersteunt in de [lijst met zelf studies voor gebruikers inrichting](../saas-apps/tutorial-list.md). De vooraf geïntegreerde toepassingen die in de galerie worden weer gegeven, gebruiken doorgaans SCIM 2,0-based User Management-Api's voor het inrichten. 

   ![Sales Force-logo](media/user-provisioning/gallery-app-logos.png)

   Als u een nieuwe toepassing wilt aanvragen voor inrichting, kunt u een [aanvraag indienen om uw toepassing te integreren in de app-galerie](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing). Voor een aanvraag voor gebruikers inrichting moet de toepassing een SCIM-compatibel eind punt hebben. Vraag de leverancier van de toepassing de SCIM-standaard te volgen zodat de app snel kan worden geïmplementeerd op ons platform.

* **Toepassingen die ondersteuning bieden voor SCIM 2,0**. Voor informatie over het algemeen verbinden van toepassingen die SCIM 2,0-based User Management-Api's implementeren, raadpleegt [u scim gebruiken voor het automatisch inrichten van gebruikers en groepen van Azure Active Directory naar toepassingen](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-scim"></a>Wat is SCIM?

Apps bieden specifieke gebruikers-en groeps-Api's om het inrichten en ongedaan maken van de inrichting te automatiseren. Iedereen die probeert gebruikers te beheren in meer dan één app, vertelt u dat elke app probeert dezelfde eenvoudige acties uit te voeren, zoals het maken of bijwerken van gebruikers, het toevoegen van gebruikers aan groepen of het ongedaan maken van de inrichting van gebruikers. Al deze eenvoudige acties worden nog net zo eenvoudig geïmplementeerd, met behulp van verschillende eindpunt paden, verschillende methoden voor het opgeven van gebruikers gegevens en een ander schema om elk informatie-element weer te geven.

Om deze uitdagingen aan te pakken, biedt de SCIM-specificatie een gemeen schappelijk gebruikers schema om gebruikers te helpen bij het verplaatsen naar, van en rond apps. SCIM wordt de facto-standaard voor het inrichten en, wanneer dit wordt gebruikt in combi natie met Federatie standaarden zoals SAML of OpenID Connect Connect, biedt beheerders een end-to-end, op standaarden gebaseerde oplossing voor toegangs beheer.

Zie [scim User Provisioning with Azure Active Directory](use-scim-to-provision-users-and-groups.md)(Engelstalig) voor meer informatie over het gebruik van scim om het inrichten en ongedaan maken van de inrichting van gebruikers en groepen naar een toepassing te automatiseren.

## <a name="how-does-automatic-provisioning-work"></a>Hoe werkt automatische inrichting?

De **Azure AD-inrichtings service** voorziet gebruikers van SaaS-apps en andere systemen door verbinding te maken met gebruikers beheer-API-eind punten die door elke leverancier van de toepassing worden geleverd. Met deze gebruikers beheer-API-eind punten kan Azure AD programmatisch gebruikers maken, bijwerken en verwijderen. Voor geselecteerde toepassingen kan de inrichtings service ook extra identiteits-gerelateerde objecten, zoals groepen en rollen, maken, bijwerken en verwijderen.

![Azure AD Provisioning Service](./media/user-provisioning/provisioning0.PNG)
*afbeelding 1: de Azure AD-inrichtings service*

![werk stroom voor uitgaand gebruikers inrichten](./media/user-provisioning/provisioning1.PNG)
*afbeelding 2: ' uitgaande ' werk stroom voor gebruikers inrichting van Azure AD naar populaire SaaS-toepassingen*

![werk stroom voor het inrichten van inkomend gebruikers](./media/user-provisioning/provisioning2.PNG)
*afbeelding 3: ' Inkomend ' werk stroom voor gebruikers inrichting van populaire HCM-toepassingen (Human Capital Management) tot Azure Active Directory en Windows Server Active Directory*

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hoe kan ik automatische inrichting instellen voor een toepassing?

Voor vooraf geïntegreerde toepassingen die in de galerie worden vermeld, is stapsgewijze instructies beschikbaar voor het instellen van automatische inrichting. Zie de [lijst met zelf studies voor geïntegreerde galerie-apps](https://docs.microsoft.com/azure/active-directory/saas-apps/). De volgende video laat zien hoe u automatische gebruikers inrichting instelt voor Sales Force.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Voor andere toepassingen die SCIM 2,0 ondersteunen, volgt u de stappen in het artikel [scim gebruikers inrichten met Azure Active Directory](use-scim-to-provision-users-and-groups.md).

## <a name="what-happens-during-provisioning"></a>Wat gebeurt er tijdens het inrichten?

Wanneer Azure AD het bron systeem is, gebruikt de inrichtings service de [differentiële query functie van de Azure AD-Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) om gebruikers en groepen te bewaken. De inrichtings service voert een eerste cyclus uit op het bron systeem en het doel systeem, gevolgd door periodieke incrementele cycli.

### <a name="initial-cycle"></a>Eerste cyclus

Wanneer de inrichtings service wordt gestart, zal de eerste synchronisatie ooit worden uitgevoerd:

1. Query's uitvoeren voor alle gebruikers en groepen van het bron systeem en alle kenmerken ophalen die zijn gedefinieerd in de [kenmerk toewijzingen](customize-application-attributes.md).
1. Filter de gebruikers en groepen die zijn geretourneerd met behulp van de geconfigureerde [toewijzingen](assign-user-or-group-access-portal.md) of [bereik filters op basis van kenmerken](define-conditional-rules-for-provisioning-user-accounts.md).
1. Wanneer een gebruiker is toegewezen of in het bereik van het inrichten is, vraagt de service het doel systeem om een overeenkomende gebruiker met behulp van de opgegeven [overeenkomende kenmerken](customize-application-attributes.md#understanding-attribute-mapping-properties). Voor beeld: als de naam van de userPrincipal in het bron systeem het overeenkomende kenmerk is en toewijst aan de gebruikers naam in het doel systeem, voert de inrichtings service een query uit op het doel systeem voor gebruikers namen die overeenkomen met de userPrincipal naam waarden in het bron systeem.
1. Als er geen overeenkomende gebruiker wordt gevonden in het doel systeem, wordt deze gemaakt met behulp van de kenmerken die worden geretourneerd door het bron systeem. Nadat het gebruikers account is gemaakt, detecteert en slaat de inrichtings service de ID van het doel systeem op voor de nieuwe gebruiker. dit wordt gebruikt om alle toekomstige bewerkingen op die gebruiker uit te voeren.
1. Als er een overeenkomende gebruiker wordt gevonden, wordt deze bijgewerkt met behulp van de kenmerken van het bron systeem. Nadat het gebruikers account is gevonden, detecteert en slaat de inrichtings service de ID van het doel systeem op voor de nieuwe gebruiker. dit wordt gebruikt om alle toekomstige bewerkingen op die gebruiker uit te voeren.
1. Als de kenmerk toewijzingen ' verwijzings kenmerken ' bevatten, biedt de service extra updates op het doel systeem om de objecten waarnaar wordt verwezen, te maken en te koppelen. Een gebruiker kan bijvoorbeeld een kenmerk ' Manager ' hebben in het doel systeem, dat is gekoppeld aan een andere gebruiker die is gemaakt in het doel systeem.
1. Een water merk aan het einde van de eerste cyclus persistent maken, waarmee het start punt voor de latere incrementele cycli wordt geleverd.

Sommige toepassingen, zoals ServiceNow, G suite en Box ondersteunen niet alleen het inrichten van gebruikers, maar ook voor het inrichten van groepen en hun leden. Als groeps inrichting is ingeschakeld in de [toewijzingen](customize-application-attributes.md), synchroniseert de inrichtings service de gebruikers en de groepen en worden de groepslid maatschappen vervolgens op een later tijdstip gesynchroniseerd.

### <a name="incremental-cycles"></a>Incrementele cycli

Na de eerste cyclus zullen alle andere cycli:

1. Zoek het bron systeem op voor alle gebruikers en groepen die zijn bijgewerkt sinds het laatste water merk is opgeslagen.
1. Filter de gebruikers en groepen die zijn geretourneerd met behulp van de geconfigureerde [toewijzingen](assign-user-or-group-access-portal.md) of [bereik filters op basis van kenmerken](define-conditional-rules-for-provisioning-user-accounts.md).
1. Wanneer een gebruiker is toegewezen of in het bereik van het inrichten is, vraagt de service het doel systeem om een overeenkomende gebruiker met behulp van de opgegeven [overeenkomende kenmerken](customize-application-attributes.md#understanding-attribute-mapping-properties).
1. Als er geen overeenkomende gebruiker wordt gevonden in het doel systeem, wordt deze gemaakt met behulp van de kenmerken die worden geretourneerd door het bron systeem. Nadat het gebruikers account is gemaakt, detecteert en slaat de inrichtings service de ID van het doel systeem op voor de nieuwe gebruiker. dit wordt gebruikt om alle toekomstige bewerkingen op die gebruiker uit te voeren.
1. Als er een overeenkomende gebruiker wordt gevonden, wordt deze bijgewerkt met behulp van de kenmerken van het bron systeem. Als het een nieuw toegewezen account is dat overeenkomt, detecteert en slaat de inrichtings service de ID van het doel systeem op voor de nieuwe gebruiker. dit wordt gebruikt om alle toekomstige bewerkingen op die gebruiker uit te voeren.
1. Als de kenmerk toewijzingen ' verwijzings kenmerken ' bevatten, biedt de service extra updates op het doel systeem om de objecten waarnaar wordt verwezen, te maken en te koppelen. Een gebruiker kan bijvoorbeeld een kenmerk ' Manager ' hebben in het doel systeem, dat is gekoppeld aan een andere gebruiker die is gemaakt in het doel systeem.
1. Als een gebruiker die voorheen in het bereik van het inrichten was verwijderd uit het bereik (inclusief niet-toegewezen), wordt de gebruiker door de service in het doel systeem uitgeschakeld via een update.
1. Als een gebruiker die voorheen in het bereik van de inrichting stond, is uitgeschakeld of zacht verwijderd in het bron systeem, wordt de gebruiker door de service in het doel systeem uitschakel via een update.
1. Als een gebruiker die voorheen bij het inrichten was, permanent wordt verwijderd in het bron systeem, wordt de gebruiker door de service in het doel systeem verwijderd. In azure AD worden gebruikers permanent verwijderd 30 dagen nadat ze zacht zijn verwijderd.
1. Een nieuw water merk aan het einde van de incrementele cyclus persistent maken, dat het start punt voor de latere incrementele cycli levert.

> [!NOTE]
> U kunt de bewerkingen **maken**, **bijwerken**of **verwijderen** eventueel uitschakelen met behulp van de selectie vakjes voor de acties van het **doel object** in de sectie [toewijzingen](customize-application-attributes.md) . De logica voor het uitschakelen van een gebruiker tijdens een update wordt ook bepaald via een kenmerk toewijzing van een veld zoals ' accountEnabled '.

De inrichtings service blijft een incrementele cyclus van back-to-back uitvoeren, op intervallen die in de [zelf studie specifiek voor elke toepassing](../saas-apps/tutorial-list.md)zijn gedefinieerd, totdat een van de volgende gebeurtenissen zich voordoet:

- De service wordt hand matig gestopt met de Azure Portal of met de juiste Graph API opdracht 
- Er wordt een nieuwe eerste cyclus geactiveerd met de optie **wissen en opnieuw starten** in het Azure Portal, of met behulp van de juiste Graph API opdracht. Met deze actie wordt een opgeslagen water merk gewist en worden alle bron objecten opnieuw geëvalueerd.
- Er wordt een nieuwe eerste cyclus geactiveerd als gevolg van een wijziging in kenmerk toewijzingen of filter bereik. Met deze actie wordt ook een opgeslagen water merk gewist en worden alle bron objecten opnieuw geëvalueerd.
- Het inrichtings proces gaat in quarantaine (zie hieronder) als gevolg van een hoge fout frequentie en blijft meer dan vier weken in quarantaine. In dit geval wordt de service automatisch uitgeschakeld.

### <a name="errors-and-retries"></a>Fouten en pogingen

Als een afzonderlijke gebruiker niet kan worden toegevoegd, bijgewerkt of verwijderd in het doel systeem vanwege een fout in het doel systeem, wordt de bewerking tijdens de volgende synchronisatie cyclus opnieuw geprobeerd. Als de gebruiker niet meer kan worden uitgevoerd, worden de nieuwe pogingen op een lagere frequentie uitgevoerd en wordt de schaal geleidelijk teruggezet op slechts één poging per dag. Om het probleem op te lossen, moeten beheerders de [inrichtings logboeken](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) controleren om de hoofd oorzaak te bepalen en de juiste actie ondernemen. Veelvoorkomende fouten kunnen het volgende omvatten:

- Gebruikers die geen kenmerk hebben dat is ingevuld in het bron systeem dat vereist is in het doel systeem
- Gebruikers met een kenmerk waarde in het bron systeem waarvoor zich een unieke beperking in het doel systeem bevindt, en dezelfde waarde is aanwezig in een andere gebruikers record

Deze fouten kunnen worden opgelost door de kenmerk waarden voor de betrokken gebruiker in het bron systeem aan te passen of door de kenmerk toewijzingen aan te passen om conflicten niet te veroorzaken.

### <a name="quarantine"></a>Voorschriften

Als de meeste of alle aanroepen voor het doel systeem consistent zijn als gevolg van een fout (zoals voor ongeldige beheerders referenties), wordt de inrichtings taak de status ' quarantaine '. Deze status wordt aangegeven in het [overzichts rapport](check-status-user-account-provisioning.md) van de inrichting en via e-mail als e-mail meldingen zijn geconfigureerd in de Azure Portal.

In quarantaine wordt de frequentie van incrementele cycli geleidelijk per dag gereduceerd.

De inrichtings taak wordt uit quarantaine gehaald nadat alle foutieve fouten zijn opgelost en de volgende synchronisatie cyclus wordt gestart. Als de inrichtings taak langer dan vier weken in quarantaine blijft, wordt de inrichtings taak uitgeschakeld. Hier vindt u meer informatie over de [quarantaine status.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="how-long-will-it-take-to-provision-users"></a>Hoe lang duurt het om gebruikers in te richten?

De prestaties zijn afhankelijk van of uw inrichtings taak een initiële inrichtings cyclus of een incrementele cyclus uitvoert. Zie [de status van gebruikers inrichten controleren](application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie over hoe lang het inrichten duurt en het controleren van de status van de inrichtings service.

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Hoe kan ik zien of gebruikers correct worden ingericht?

Alle bewerkingen die worden uitgevoerd door de User Provisioning Service worden vastgelegd in de Azure AD [-inrichtings Logboeken (preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Dit omvat alle Lees-en schrijf bewerkingen die zijn aangebracht op de bron-en doel systemen en de gebruikers gegevens die tijdens elke bewerking zijn gelezen of geschreven.

Voor informatie over het lezen van de inrichtings Logboeken in de Azure Portal, raadpleegt u de [hand leiding](check-status-user-account-provisioning.md)voor het inrichten van de rapportage.

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Hoe kan ik problemen met het inrichten van de gebruiker oplossen?

Zie problemen met het [configureren en inrichten van gebruikers voor een toepassing](application-provisioning-config-problem.md)voor op scenario's gebaseerde richt lijnen voor het oplossen van problemen met automatische gebruikers inrichting.

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Wat zijn de aanbevolen procedures voor het implementeren van automatische gebruikers inrichting?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Voor een voor beeld van een stapsgewijze implementatie plan voor het inrichten van een uitgaande gebruiker naar een toepassing raadpleegt u de [hand leiding voor identiteits implementatie voor gebruikers inrichting](https://aka.ms/deploymentplans/userprovisioning).

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>Werkt automatische gebruikers inrichting voor SaaS-apps met B2B-gebruikers in azure AD?

Ja, het is mogelijk de Azure AD User Provisioning Service te gebruiken om B2B-gebruikers (of gast) in te richten in azure AD naar SaaS-toepassingen.

Om B2B-gebruikers zich echter aan te melden bij de SaaS-toepassing met behulp van Azure AD, moet voor de SaaS-toepassing de mogelijkheid voor eenmalige aanmelding op basis van SAML op een specifieke manier zijn geconfigureerd. Zie voor meer informatie over het configureren van SaaS-toepassingen voor het ondersteunen van aanmeldingen van B2B-gebruikers [SaaS-apps configureren voor B2B-samen werking]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>Werkt automatische gebruikers inrichting voor SaaS-apps met dynamische groepen in azure AD?

Ja. Wanneer het is geconfigureerd voor ' alleen toegewezen gebruikers en groepen synchroniseren ', kan de Azure AD-gebruikers Provisioning-Service gebruikers inrichten of ongedaan maken in een SaaS-toepassing op basis van het feit of ze lid zijn van een [dynamische groep](../users-groups-roles/groups-create-rule.md). Dynamische groepen werken ook met de optie ' alle gebruikers en groepen synchroniseren '.

Het gebruik van dynamische groepen kan echter van invloed zijn op de algehele prestaties van end-to-end gebruikers inrichting van Azure AD naar SaaS-toepassingen. Houd bij het gebruik van dynamische groepen deze aanvullende opmerkingen en aanbevelingen in acht:

- Hoe snel een gebruiker in een dynamische groep wordt ingericht of onbeschikbaar wordt gemaakt in een SaaS-toepassing, is afhankelijk van de snelheid waarmee de dynamische groep lidmaatschaps wijzigingen kan evalueren. Zie [verwerkings status voor een lidmaatschaps regel controleren](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)voor meer informatie over het controleren van de verwerkings status van een dynamische groep.

- Wanneer u dynamische groepen gebruikt, moeten de regels zorgvuldig worden gezien bij het inrichten en het ongedaan maken van de inrichting van de gebruiker, omdat het lidmaatschap van een onherstelbare gebeurtenis wordt veroorzaakt.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>Werkt automatische gebruikers inrichting voor SaaS-apps met geneste groepen in azure AD?

Nee. De Azure AD User Provisioning Service kan de gebruikers die zich in geneste groepen bevinden, niet lezen of inrichten wanneer ze zijn geconfigureerd voor ' alleen toegewezen gebruikers en groepen '. Het is alleen mogelijk om gebruikers die direct lid zijn van de groep expliciet toegewezen te lezen en in te richten.

Dit is een beperking van "groeps toewijzingen voor toepassingen", die ook van invloed zijn op eenmalige aanmelding, en wordt beschreven in [een groep gebruiken om de toegang tot SaaS-toepassingen te beheren](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

Als tijdelijke oplossing moet u de groepen met de gebruikers die moeten worden ingericht, expliciet toewijzen (of een andere [Scope in](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)).

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>Wordt het inrichten tussen Azure AD en een doel toepassing met behulp van een versleuteld kanaal?

Ja. We gebruiken HTTPS SSL-versleuteling voor het server doel.

## <a name="related-articles"></a>Verwante artikelen:

- [Lijst met zelf studies voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)
- [Kenmerk toewijzingen aanpassen voor het inrichten van gebruikers](customize-application-attributes.md)
- [Expressies schrijven voor kenmerk toewijzingen](functions-for-customizing-application-data.md)
- [Filters voor het inrichten van gebruikers in bereik](define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM gebruiken om automatisch inrichten van gebruikers en groepen vanuit Azure AD naar toepassingen in te scha kelen](use-scim-to-provision-users-and-groups.md)
- [Overzicht van Azure AD-synchronisatie-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
