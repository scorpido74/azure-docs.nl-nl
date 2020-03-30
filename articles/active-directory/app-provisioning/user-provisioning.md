---
title: Geautomatiseerde Gebruikersvoorziening van saas-apps in Azure AD | Microsoft Documenten
description: Een inleiding over hoe u Azure AD gebruiken om gebruikersaccounts automatisch in te richten, te de-provisionen en continu bij te werken voor meerdere SaaS-toepassingen van derden.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e828fd9c2561007c332db67bfd0b20dda9b845f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454530"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Gebruikersinrichting en deprovisioning automatiseren voor toepassingen met Azure Active Directory

In Azure Active Directory (Azure AD) verwijst de term **app-inrichting** naar het automatisch maken van gebruikersidentiteiten en -rollen in de cloud[(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)waartoe gebruikers toegang nodig hebben. Naast het maken van gebruikersidentiteiten, omvat automatische inrichting het onderhoud en verwijderen van gebruikersidentiteiten als status of rollen veranderen. Veelvoorkomende scenario's zijn het inrichten van een Azure AD-gebruiker in toepassingen zoals [Dropbox,](../saas-apps/dropboxforbusiness-provisioning-tutorial.md) [Salesforce,](../saas-apps/salesforce-provisioning-tutorial.md) [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)en meer.

![Overzichtsdiagram inrichten](./media/user-provisioning/provisioning-overview.png)

Met deze functie u:

- **Provisioning automatiseren:** maak automatisch nieuwe accounts aan in de juiste systemen voor nieuwe mensen wanneer ze lid worden van uw team of organisatie.
- **Deprovisioning automatiseren:** Deactiveer accounts automatisch in de juiste systemen wanneer mensen het team of de organisatie verlaten.
- **Gegevens synchroniseren tussen systemen:** Zorg ervoor dat de identiteiten in uw apps en systemen up-to-date worden gehouden op basis van wijzigingen in de directory of uw personeelssysteem.
- **Voorzieningsgroepen:** Groepen inrichten op toepassingen die hen ondersteunen.
- **Toegang tot de regels:** Controleer en controleer wie er is ingericht in uw toepassingen.
- **Naadloos implementeren in bruin veldscenario's:** Match bestaande identiteiten tussen systemen en zorgen voor eenvoudige integratie, zelfs wanneer gebruikers al bestaan in het doelsysteem.
- **Gebruik uitgebreide aanpassingen:** Profiteer van aanpasbare kenmerktoewijzingen die bepalen welke gebruikersgegevens van het bronsysteem naar het doelsysteem moeten stromen.
- **Ontvang waarschuwingen voor kritieke gebeurtenissen:** De inrichtingsservice biedt waarschuwingen voor kritieke gebeurtenissen en biedt integratie met Log Analytics mogelijk, waarbij u aangepaste waarschuwingen definiëren om aan uw bedrijfsbehoeften te voldoen.

## <a name="benefits-of-automatic-provisioning"></a>Voordelen van automatische voorziening

Naarmate het aantal toepassingen dat in moderne organisaties wordt gebruikt blijft groeien, worden IT-beheerders belast met toegangsbeheer op schaal. Met standaarden zoals Beveiligingsbeweringen Opmaaktaal (SAML) of Open ID Connect (OIDC) kunnen beheerders snel eenmalige aanmelding (SSO) instellen, maar toegang vereist ook dat gebruikers in de app worden ingericht. Voor veel beheerders betekent inrichten elke week handmatig elk gebruikersaccount maken of CSV-bestanden uploaden, maar deze processen zijn tijdrovend, duur en foutgevoelig. Oplossingen zoals SAML just-in-time (JIT) zijn overgenomen om provisioning te automatiseren, maar bedrijven hebben ook een oplossing nodig om gebruikers te deprovisioneren wanneer ze de organisatie verlaten of geen toegang meer nodig hebben tot bepaalde apps op basis van rolverandering.

Enkele gemeenschappelijke motivaties voor het gebruik van automatische provisioning zijn:

- Het maximaliseren van de efficiëntie en nauwkeurigheid van inrichtingsprocessen.
- Bespaar op kosten in verband met hosting en het onderhouden van op maat ontwikkelde inrichtingsoplossingen en scripts.
- Uw organisatie beveiligen door de identiteit van gebruikers direct te verwijderen uit belangrijke SaaS-apps wanneer ze de organisatie verlaten.
- Eenvoudig een groot aantal gebruikers importeren in een bepaalde SaaS-toepassing of -systeem.
- Met één set beleidsregels om te bepalen wie is ingericht en wie zich kan aanmelden bij een app.

Azure AD-gebruikersinrichting kan helpen deze uitdagingen aan te pakken. Als u meer wilt weten over hoe klanten Azure AD-gebruikersinrichting hebben gebruikt, u de [ASOS-casestudy](https://aka.ms/asoscasestudy)lezen. De video hieronder geeft een overzicht van de gebruikersinrichting in Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Welke toepassingen en systemen kan ik gebruiken met azure AD-automatische gebruikersinrichting?

Azure AD beschikt over vooraf geïntegreerde ondersteuning voor veel populaire SaaS-apps en personeelssystemen en generieke ondersteuning voor apps die specifieke onderdelen van de [SCIM 2.0-standaard](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)implementeren.

* **Vooraf geïntegreerde toepassingen (galerij SaaS-apps)**. U alle toepassingen waarvoor Azure AD een vooraf geïntegreerde inrichtingsconnector ondersteunt, vinden in de [lijst met toepassingszelfstudies voor gebruikersinrichting.](../saas-apps/tutorial-list.md) De vooraf geïntegreerde toepassingen in de galerie maken over het algemeen gebruik van SCIM 2.0-gebaseerde gebruikersbeheer API's voor provisioning. 

   ![Salesforce-logo](./media/user-provisioning/gallery-app-logos.png)

   Als u een nieuwe aanvraag voor inrichten wilt aanvragen, u [vragen om uw toepassing te integreren met onze app-galerij.](../develop/howto-app-gallery-listing.md) Voor een aanvraag voor het inrichten van een gebruiker vereisen we dat de toepassing een SCIM-compatibel eindpunt heeft. Vraag de leverancier van de toepassing de SCIM-standaard te volgen, zodat we de app snel naar ons platform kunnen appen.

* **Toepassingen die SCIM 2.0 ondersteunen**. Zie [Een SCIM-eindpunt maken en gebruikersinrichting configureren](use-scim-to-provision-users-and-groups.md)voor informatie over het generiek verbinden van toepassingen die op SCIM 2.0 gebaseerde gebruikersbeheer-API's implementeren.

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Wat is System for Cross-domain Identity Management (SCIM)?

Om het inrichten en deprovisioneren te automatiseren, stellen apps eigen gebruikers- en groeps-API's bloot. Iedereen die heeft geprobeerd gebruikers in meer dan één app te beheren, vertelt u echter dat elke app probeert dezelfde eenvoudige acties uit te voeren, zoals het maken of bijwerken van gebruikers, het toevoegen van gebruikers aan groepen of het deprovisionen van gebruikers. Toch worden al deze eenvoudige acties net iets anders geïmplementeerd, met behulp van verschillende eindpuntpaden, verschillende methoden om gebruikersinformatie op te geven en een ander schema om elk element van informatie weer te geven.

Om deze uitdagingen het hoofd te bieden, biedt de SCIM-specificatie een gemeenschappelijk gebruikersschema om gebruikers te helpen in, uit en rond apps te gaan. SCIM wordt de de facto standaard voor provisioning en biedt beheerders, wanneer het wordt gebruikt in combinatie met federatiestandaarden zoals SAML of OpenID Connect, beheerders een end-to-end oplossing op basis van standaarden voor toegangsbeheer.

Zie [Een SCIM-eindpunt](use-scim-to-provision-users-and-groups.md)bouwen en gebruikersinrichting configureren voor gedetailleerde richtlijnen voor het ontwikkelen van een SCIM-eindpunt om de inrichting en deprovisioning van gebruikers en groepen te automatiseren. Voor vooraf geïntegreerde toepassingen in de galerie (Slack, Azure Databricks, Snowflake, enz.), u de documentatie van ontwikkelaars overslaan en de [hier](../saas-apps/tutorial-list.md)meegeleverde zelfstudies gebruiken.

## <a name="manual-vs-automatic-provisioning"></a>Handmatige en automatische inrichting

Toepassingen in de Azure AD-galerie ondersteunen een van de twee inrichtingsmodi:

* **Handmatiginrichten** betekent dat er nog geen automatische Azure AD-inrichtingsconnector voor de app is. Gebruikersaccounts moeten handmatig worden gemaakt, bijvoorbeeld door gebruikers rechtstreeks toe te voegen aan de beheerdersportal van de app of door een spreadsheet met gebruikersaccountdetails te uploaden. Raadpleeg de documentatie van de app of neem contact op met de app-ontwikkelaar om te bepalen welke mechanismen beschikbaar zijn.

* **Automatisch** betekent dat voor deze toepassing een Azure AD-inrichtingsconnector is ontwikkeld. U moet de installatie-zelfstudie volgen die specifiek is voor het instellen van provisioning voor de toepassing. App-zelfstudies zijn te vinden in [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory.](../saas-apps/tutorial-list.md)

In de Azure AD-galerie worden toepassingen die automatische inrichting ondersteunen, aangeduid met een **lijstmetingpictogram.** Overschakelen naar de nieuwe galerij preview ervaring om deze pictogrammen te zien (in de banner aan de bovenkant van de **pagina Een toepassing toevoegen,** selecteert u de link die zegt **Klik hier om uit te proberen de nieuwe en verbeterde app galerij**).

![Inrichtingspictogram in de toepassingsgalerie](./media/user-provisioning/browse-gallery.png)

De inrichtingsmodus die door een toepassing wordt ondersteund, is ook zichtbaar op het tabblad **Provisioning** zodra u de toepassing aan uw **Enterprise-apps**hebt toegevoegd.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hoe stel ik automatische inrichting in voor een toepassing?

Voor vooraf geïntegreerde toepassingen die in de galerie worden vermeld, is stapsgewijze richtlijnen beschikbaar voor het opzetten van automatische inrichting. Zie de [lijst met zelfstudies voor geïntegreerde galerij-apps](../saas-apps/tutorial-list.md). In de volgende video wordt uitgelegd hoe u automatische gebruikersinrichting voor SalesForce instellen.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Volg de stappen in het artikel [Een SCIM-eindpunt bouwen en gebruikersinrichting configureren](use-scim-to-provision-users-and-groups.md)voor andere toepassingen die SCIM 2.0 ondersteunen.


## <a name="related-articles"></a>Verwante artikelen:

- [Lijst met zelfstudies over het integreren van SaaS-apps](../saas-apps/tutorial-list.md)
- [Kenmerkentoewijzingen aanpassen voor gebruikersinrichting](customize-application-attributes.md)
- [Expressies schrijven voor kenmerktoewijzingen](../app-provisioning/functions-for-customizing-application-data.md)
- [Scopingfilters voor gebruikersinrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Een SCIM-eindpunt bouwen en gebruikersinrichting configureren](use-scim-to-provision-users-and-groups.md)
- [Overzicht van Azure AD-synchronisatie-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
