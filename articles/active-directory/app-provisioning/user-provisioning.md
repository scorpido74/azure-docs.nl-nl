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
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2d061d830df9cef305982ff54e91a886d4406d
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066081"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Gebruikers inrichten en het ongedaan maken van de inrichting van toepassingen automatiseren met Azure Active Directory

In Azure Active Directory (Azure AD) verwijst de inrichting van de term- **app** naar het automatisch maken van gebruikers-id's en-rollen in de Cloud toepassingen ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) waartoe gebruikers toegang nodig hebben. Naast het maken van gebruikers identiteiten, omvat automatische inrichting het onderhoud en de verwijdering van gebruikers identiteiten als status of rollen worden gewijzigd. Veelvoorkomende scenario's zijn onder andere het inrichten van een Azure AD-gebruiker in toepassingen zoals [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Sales Force](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)en meer.

![Overzichts diagram van inrichting](./media/user-provisioning/provisioning-overview.png)

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

   ![Sales Force-logo](./media/user-provisioning/gallery-app-logos.png)

   Als u een nieuwe toepassing wilt aanvragen voor inrichting, kunt u een [aanvraag indienen om uw toepassing te integreren in de app-galerie](../develop/howto-app-gallery-listing.md). Voor een aanvraag voor gebruikers inrichting moet de toepassing een SCIM-compatibel eind punt hebben. Vraag de leverancier van de toepassing de SCIM-standaard te volgen zodat de app snel kan worden geïmplementeerd op ons platform.

* **Toepassingen die ondersteuning bieden voor SCIM 2,0**. Zie [een scim-eind punt bouwen en gebruikers inrichten configureren](use-scim-to-provision-users-and-groups.md)voor meer informatie over het algemeen verbinden van toepassingen die gebruikmaken van scim 2,0-api's voor gebruikers beheer.

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Wat is systeem voor Cross-Domain Identity Management (SCIM)?

Apps bieden specifieke gebruikers-en groeps-Api's om het inrichten en ongedaan maken van de inrichting te automatiseren. Iedereen die probeert gebruikers te beheren in meer dan één app, vertelt u dat elke app probeert dezelfde eenvoudige acties uit te voeren, zoals het maken of bijwerken van gebruikers, het toevoegen van gebruikers aan groepen of het ongedaan maken van de inrichting van gebruikers. Al deze eenvoudige acties worden nog net zo eenvoudig geïmplementeerd, met behulp van verschillende eindpunt paden, verschillende methoden voor het opgeven van gebruikers gegevens en een ander schema om elk informatie-element weer te geven.

Om deze uitdagingen aan te pakken, biedt de SCIM-specificatie een gemeen schappelijk gebruikers schema om gebruikers te helpen bij het verplaatsen naar, van en rond apps. SCIM wordt de facto-standaard voor het inrichten en, wanneer dit wordt gebruikt in combi natie met Federatie standaarden zoals SAML of OpenID Connect Connect, biedt beheerders een end-to-end, op standaarden gebaseerde oplossing voor toegangs beheer.

Zie [een scim-eind punt bouwen en gebruikers inrichten configureren](use-scim-to-provision-users-and-groups.md)voor meer informatie over het gebruik van scim om het inrichten en ongedaan maken van de inrichting van gebruikers en groepen naar een toepassing te automatiseren.

## <a name="manual-vs-automatic-provisioning"></a>Handmatige en automatische inrichting

Toepassingen in de Azure AD-galerie ondersteunen een van de twee inrichtings modi:

* **Hand matige** inrichting betekent dat er nog geen automatische Azure AD-inrichtings connector is voor de app. Gebruikers accounts moeten hand matig worden gemaakt, bijvoorbeeld door gebruikers rechtstreeks toe te voegen aan de beheer portal van de app of een werk blad met details van een gebruikers account te uploaden. Raadpleeg de documentatie van de app of neem contact op met de app-ontwikkelaar om te bepalen welke mechanismen beschikbaar zijn.

* **Automatische** betekent dat een Azure AD-inrichtings connector is ontwikkeld voor deze toepassing. U moet de zelf studie voor Setup volgen om het inrichten in te stellen voor de toepassing. App-zelf studies vindt u in een [lijst met zelf studies over het integreren van SaaS-apps met Azure Active Directory](../saas-apps/tutorial-list.md).

In de Azure AD-galerie worden toepassingen die ondersteuning bieden voor automatische inrichting, aangeduid met een **inrichtings** pictogram. Schakel over naar de nieuwe preview-ervaring van de galerie om deze pictogrammen te zien (Klik in de banner boven aan de **pagina een toepassing toevoegen**op de koppeling die hier wordt weer gegeven **om de nieuwe en verbeterde app-galerie uit te proberen**).

![Inrichtings pictogram in de toepassings galerie](./media/user-provisioning/browse-gallery.png)

De inrichtings modus die door een toepassing wordt ondersteund, wordt ook weer gegeven op het tabblad **inrichten** zodra u de toepassing aan uw **zakelijke apps**hebt toegevoegd.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hoe kan ik automatische inrichting instellen voor een toepassing?

Voor vooraf geïntegreerde toepassingen die in de galerie worden vermeld, is stapsgewijze instructies beschikbaar voor het instellen van automatische inrichting. Zie de [lijst met zelf studies voor geïntegreerde galerie-apps](../saas-apps/tutorial-list.md). De volgende video laat zien hoe u automatische gebruikers inrichting instelt voor Sales Force.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Voor andere toepassingen die SCIM 2,0 ondersteunen, volgt u de stappen in het artikel [een scim-eind punt bouwen en gebruikers inrichten configureren](use-scim-to-provision-users-and-groups.md).


## <a name="related-articles"></a>Verwante artikelen:

- [Lijst met zelf studies voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)
- [Kenmerk toewijzingen aanpassen voor het inrichten van gebruikers](customize-application-attributes.md)
- [Expressies schrijven voor kenmerk toewijzingen](../app-provisioning/functions-for-customizing-application-data.md)
- [Filters voor het inrichten van gebruikers in bereik](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Een SCIM-eind punt bouwen en gebruikers inrichten configureren](use-scim-to-provision-users-and-groups.md)
- [Overzicht van Azure AD-synchronisatie-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
