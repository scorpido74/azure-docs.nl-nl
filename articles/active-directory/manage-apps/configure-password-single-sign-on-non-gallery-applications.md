---
title: Eenmalige aanmelding voor Azure AD-apps configureren | Microsoft Documenten
description: Wachtwoordsingle sign-on (SSO) configureren voor uw Azure AD enterprise-toepassingen in Microsoft-identiteitsplatform (Azure AD)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063523"
---
# <a name="configure-password-single-sign-on"></a>Eenmalige aanmelding voor wachtwoorden configureren

Wanneer u [een galerie-app](add-gallery-app.md) of een [web-app zonder galerie](add-non-gallery-app.md) toevoegt aan uw Azure AD Enterprise-toepassingen, is een van de opties voor één aanmelding die voor u beschikbaar [is, een enkele aanmelding op basis](what-is-single-sign-on.md#password-based-sso)van een wachtwoord. Deze optie is beschikbaar voor elk web met een HTML-aanmeldingspagina. Met sso op basis van wachtwoorden, ook wel wachtwoordkluis genoemd, u gebruikerstoegang en wachtwoorden beheren voor webtoepassingen die identiteitsfederatie niet ondersteunen. Het is ook handig voor scenario's waarin meerdere gebruikers één account moeten delen, zoals de sociale media-app-accounts van uw organisatie. 

SSO op basis van wachtwoorden is een geweldige manier om snel aan de slag te gaan met het integreren van toepassingen in Azure AD en stelt u in staat om:

-   **Eén aanmelding voor uw gebruikers** inschakelen door gebruikersnamen en wachtwoorden veilig op te slaan en opnieuw af te spelen voor de toepassing die u hebt geïntegreerd met Azure AD

-   **Ondersteuningstoepassingen waarvoor meerdere aanmeldingsvelden nodig zijn** voor toepassingen waarvoor meer nodig is dan alleen gebruikersnamen en wachtwoordvelden om zich aan te melden

-   **De labels van** de invoervelden gebruikersnaam en wachtwoord aanpassen die uw gebruikers in het [deelvenster Toepassingstoegangs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) zien wanneer ze hun referenties invoeren

-   Laat uw **gebruikers** hun eigen gebruikersnamen en wachtwoorden verstrekken voor bestaande toepassingsaccounts die ze handmatig intypen in het [Application Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Een **lid van de bedrijfsgroep** toestaan de gebruikersnamen en wachtwoorden op te geven die aan een gebruiker zijn toegewezen met behulp van de functie [Selfservice-toepassingstoegang](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Een **beheerder** toestaan een gebruikersnaam en wachtwoord op te geven die door personen of groepen moeten worden gebruikt wanneer hij zich aanmeldt bij de toepassing met de functie Referenties bijwerken 

## <a name="before-you-begin"></a>Voordat u begint

Zie [Een galerie-app toevoegen](add-gallery-app.md) of [Een niet-galerie-app toevoegen](add-non-gallery-app.md)als de toepassing niet is toegevoegd aan uw Azure AD-tenant.

## <a name="open-the-app-and-select-password-single-sign-on"></a>De app openen en een enkele aanmelding voor wachtwoorden selecteren

1. Meld u bij [Azure Portal](https://portal.azure.com) aan als beheerder van de cloudtoepassing of als toepassingsbeheerder voor uw Azure Active Directory-tenant.

2. Navigeer naar **Azure Active Directory** > **Enterprise-toepassingen**. Er verschijnt een willekeurig voorbeeld van de toepassingen in uw Azure AD-tenant. 

3. Selecteer in het menu **Toepassingstype** **alle toepassingen**en selecteer **Vervolgens Toepassen**.

4. Voer de naam van de toepassing in het zoekvak in en selecteer de toepassing in de resultaten.

5. Selecteer onder de sectie **Beheren** de optie **Eén aanmelding**. 

6. Selecteer **Op een wachtwoord gebaseerd**.

7. Voer de URL in van de op het web gebaseerde aanmeldingspagina van de toepassing. Deze tekenreeks moet de pagina zijn die het invoerveld gebruikersnaam bevat.

   ![Single sign-on op basis van wachtwoord](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Selecteer **Opslaan**. Azure AD probeert de aanmeldingspagina te ontzien voor een gebruikersnaaminvoer en een wachtwoordinvoer. Als de poging slaagt, bent u klaar. 
 
> [!NOTE]
> Uw volgende stap is het [toewijzen van gebruikers of groepen aan de toepassing.](methods-for-assigning-users-and-groups.md) Nadat u gebruikers en groepen hebt toegewezen, u referenties verstrekken die namens een gebruiker kunnen worden gebruikt wanneer deze zich aanmeldt bij de toepassing. Schakel **Gebruikers en groepen**in, schakel het selectievakje in voor de rij van de gebruiker of groep en klik op Referenties **bijwerken**. Voer vervolgens de gebruikersnaam en het wachtwoord in die namens de gebruiker of groep moeten worden gebruikt. Anders worden gebruikers gevraagd om de referenties zelf in te voeren bij de lancering.
 

## <a name="manual-configuration"></a>Handmatige configuratie

Als de parsing-poging van Azure AD mislukt, u aanmelden handmatig configureren.

1. Selecteer ** \<onder toepassingsnaam> Configuratie**de optie **Toepassingsnaam \<configureren> Instellingen voor één aanmelding wachtwoord** om de **aanmeldingspagina configureren** weer te geven. 

2. Selecteer **Aanmeldingsvelden handmatig detecteren**. Er worden aanvullende instructies weergegeven waarin de handmatige detectie van aanmeldingsvelden wordt beschreven.

   ![Handmatige configuratie van eenmalige aanmelding op basis van wachtwoorden](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Selecteer **Aanmeldingsvelden vastleggen**. Er wordt een pagina met de vastleggenvan status geopend op een nieuw tabblad, waarin de **vastleggen van metagegevens van**het bericht wordt weergegeven.

4. Als het vak **Vereiste extensie voor toegangspaneelextensie** wordt weergegeven op een nieuw tabblad, selecteert u **Nu installeren** om de browserextensie Mijn apps **Secure Sign-in Extension** te installeren. (De browserextensie vereist Microsoft Edge, Chrome of Firefox.) Installeer, start en schakel de extensie in en vernieuw de pagina met de opnamestatus.

   De browserextensie opent vervolgens een ander tabblad met de ingevoerde URL.
5. Ga op het tabblad met de ingevoerde URL door het aanmeldingsproces. Vul de velden gebruikersnaam en wachtwoord in en probeer je aan te melden. (U hoeft niet het juiste wachtwoord op te geven.)

   Met een prompt wordt u gevraagd de vastgelegde aanmeldingsvelden op te slaan.
6. Selecteer **OK**. De browserextensie werkt de capture status pagina bij met het bericht **Metadata is bijgewerkt voor de toepassing.** Het browsertabblad wordt gesloten.

7. Selecteer op de **aanmeldingspagina van** Azure AD Configureren de optie **Ok, ik kon me met succes aanmelden bij de app**.

8. Selecteer **OK**.

Na het vastleggen van de aanmeldingspagina u gebruikers en groepen toewijzen en u referentiebeleid instellen, net als gewone [SSO-toepassingen voor wachtwoorden.](what-is-single-sign-on.md)

> [!NOTE]
> U een tegellogo voor de toepassing uploaden met de knop **Logo uploaden** op het tabblad **Configureren** voor de toepassing.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers of groepen toewijzen aan de toepassing](methods-for-assigning-users-and-groups.md)
- [Automatische gebruikersaccountinrichting configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
