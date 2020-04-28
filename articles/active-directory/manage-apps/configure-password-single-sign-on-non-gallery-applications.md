---
title: Eenmalige aanmelding met een wacht woord configureren voor Azure AD-apps | Microsoft Docs
description: Eenmalige aanmelding met een wacht woord configureren voor uw Azure AD-zakelijke toepassingen in micro soft Identity platform (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563bda275b73f76b042b5e57a9909ca78c504bb3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063523"
---
# <a name="configure-password-single-sign-on"></a>Eenmalige aanmelding met een wacht woord configureren

Wanneer u [een galerie-app](add-gallery-app.md) of een [niet-galerij web-app](add-non-gallery-app.md) toevoegt aan uw Azure AD-toepassingen voor ondernemingen, is een van de opties voor eenmalige aanmelding op basis van een [wacht woord gebaseerd](what-is-single-sign-on.md#password-based-sso)op een eenmalige aanmelding. Deze optie is beschikbaar voor elk web met een HTML-aanmeldings pagina. Met SSO op basis van wacht woorden, ook wel wachtwoord kluizen genoemd, kunt u gebruikers toegang en-wacht woorden beheren voor webtoepassingen die geen identiteits Federatie ondersteunen. Het is ook handig voor scenario's waarbij meerdere gebruikers een enkel account moeten delen, zoals de accounts voor sociale media-apps van uw organisatie. 

SSO op basis van wacht woorden is een uitstekende manier om snel toepassingen te integreren in azure AD en biedt de volgende voor delen:

-   **Eenmalige aanmelding inschakelen voor uw gebruikers** door gebruikers namen en wacht woorden veilig op te slaan en opnieuw af te spelen voor de toepassing die u hebt geïntegreerd met Azure AD

-   **Ondersteunings toepassingen waarvoor meerdere aanmeldings velden zijn vereist** voor toepassingen waarvoor meer dan alleen gebruikers naam en wacht woord zijn vereist om zich aan te melden

-   **De labels aanpassen** van de invoer velden gebruikers naam en wacht woord die uw gebruikers zien op het [toegangs paneel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) van de toepassing wanneer ze hun referenties invoeren

-   **Gebruikers** in staat stellen hun eigen gebruikers namen en wacht woorden op te geven voor bestaande toepassings accounts die ze hand matig typen in het [toegangs paneel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) van de toepassing

-   Een **lid van de bedrijfs groep** toestaan om de gebruikers namen en wacht woorden op te geven die aan een gebruiker zijn toegewezen met behulp van de [self-service toepassings toegangs](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) functie

-   Een **beheerder** toestaan om een gebruikers naam en wacht woord op te geven die moeten worden gebruikt door individuen of groepen bij het aanmelden bij de toepassing met behulp van de functie voor het bijwerken van referenties 

## <a name="before-you-begin"></a>Voordat u begint

Als de toepassing niet is toegevoegd aan uw Azure AD-Tenant, raadpleegt u [een galerie-app toevoegen](add-gallery-app.md) of [een niet-galerij-app toevoegen](add-non-gallery-app.md).

## <a name="open-the-app-and-select-password-single-sign-on"></a>Open de app en selecteer eenmalige aanmelding met wacht woord

1. Meld u bij [Azure Portal](https://portal.azure.com) aan als beheerder van de cloudtoepassing of als toepassingsbeheerder voor uw Azure Active Directory-tenant.

2. Ga naar **Azure Active Directory** > **bedrijfs toepassingen**. Een wille keurig voor beeld van de toepassingen in uw Azure AD-Tenant wordt weer gegeven. 

3. Selecteer in het menu **toepassings type** **alle toepassingen**en selecteer vervolgens **Toep assen**.

4. Voer de naam van de toepassing in het zoekvak in en selecteer vervolgens de toepassing in de resultaten.

5. Selecteer onder de sectie **beheren** de optie **eenmalige aanmelding**. 

6. Selecteer **op wacht woord gebaseerd**.

7. Voer de URL in van de webgebaseerde aanmeldings pagina van de toepassing. Deze teken reeks moet de pagina zijn die het invoergegevens veld van de gebruikers naam bevat.

   ![Eenmalige aanmelding op basis van wacht woorden](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Selecteer **Opslaan**. Azure AD probeert de aanmeldings pagina te parseren voor een gebruikersnaam invoer en een wachtwoord invoer. Als de poging slaagt, bent u klaar. 
 
> [!NOTE]
> De volgende stap is het [toewijzen van gebruikers of groepen aan de toepassing](methods-for-assigning-users-and-groups.md). Nadat u gebruikers en groepen hebt toegewezen, kunt u referenties opgeven die namens een gebruiker moeten worden gebruikt wanneer ze zich aanmelden bij de toepassing. Selecteer **gebruikers en groepen**, schakel het selectie vakje voor de rij van de gebruiker of groep in en klik vervolgens op **referenties bijwerken**. Voer vervolgens de gebruikers naam en het wacht woord in die namens de gebruiker of groep moeten worden gebruikt. Anders wordt gebruikers gevraagd de referenties zelf bij het starten in te voeren.
 

## <a name="manual-configuration"></a>Handmatige configuratie

Als het parseren van de Azure AD-poging mislukt, kunt u hand matig aanmelden configureren.

1. Selecteer onder ** \<toepassings naam> configuratie**de **optie \<toepassings naam> wacht woord instellingen voor eenmalige aanmelding configureren** om de **aanmeldings** pagina te openen. 

2. Selecteer **aanmeldings velden hand matig detecteren**. Aanvullende instructies voor het beschrijven van de hand matige detectie van aanmeldings velden worden weer gegeven.

   ![Hand matige configuratie van op wacht woord gebaseerde eenmalige aanmelding](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Selecteer **aanmeldings velden voor vastleggen**. De pagina vastleg status wordt geopend op een nieuw tabblad, waarin wordt weer gegeven **dat het vastleggen van meta gegevens op het bericht wordt uitgevoerd**.

4. Als het vak **uitbrei ding van het toegangs paneel vereist** wordt weer gegeven op een nieuw tabblad, selecteert u **nu installeren** om de extensie browser extensies voor **beveiligde aanmelding van mijn apps** te installeren. (Voor de browser extensie is micro soft Edge, Chrome of Firefox vereist.) Vervolgens installeert, start en activeert u de extensie en vernieuwt u de pagina vastleg status.

   De browser uitbreiding opent vervolgens een ander tabblad met de ingevoerde URL.
5. Ga op het tabblad met de ingevoerde URL naar het aanmeldings proces. Vul de velden gebruikers naam en wacht woord in en probeer u aan te melden. (U hoeft het juiste wacht woord niet op te geven.)

   U wordt gevraagd om de vastgelegde aanmeldings velden op te slaan.
6. Selecteer **OK**. De browser uitbreiding werkt de pagina vastleg status bij met de **meta gegevens van het bericht voor de toepassing**. Het tabblad browser wordt gesloten.

7. Selecteer op de pagina Azure AD **-aanmelding configureren** de optie **OK, ik kan me aanmelden bij de app**.

8. Selecteer **OK**.

Nadat u de aanmeldings pagina hebt vastgelegd, kunt u gebruikers en groepen toewijzen en kunt u referentie beleidsregels instellen, net als gewone [wacht woord-SSO-toepassingen](what-is-single-sign-on.md).

> [!NOTE]
> U kunt een tegel logo voor de toepassing uploaden met behulp van de knop **logo uploaden** op het tabblad **configureren** voor de toepassing.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers of groepen toewijzen aan de toepassing](methods-for-assigning-users-and-groups.md)
- [Automatisch inrichten van gebruikers accounts configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
