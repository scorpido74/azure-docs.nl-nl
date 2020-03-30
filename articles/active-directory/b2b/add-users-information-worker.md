---
title: B2B-samenwerkingsgebruikers toevoegen als informatiewerker - Azure AD
description: Met B2B-samenwerking kunnen informatiewerkers en app-eigenaren gastgebruikers toevoegen aan Azure AD voor toegang | Microsoft Documenten
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: abb5c6939d8c88db35a776aa8f2c075a4bdcc609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565414"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Hoe gebruikers in uw organisatie gastgebruikers kunnen uitnodigen voor een app

Nadat een gastgebruiker is toegevoegd aan de directory in Azure AD, kan een eigenaar van de toepassing de gastgebruiker een directe link sturen naar de app die hij wil delen. Azure AD-beheerders kunnen ook selfservicebeheer instellen voor galerie- of SAML-apps in hun Azure AD-tenant. Op deze manier kunnen eigenaren van toepassingen hun eigen gastgebruikers beheren, zelfs als de gastgebruikers nog niet aan de directory zijn toegevoegd. Wanneer een app is geconfigureerd voor selfservice, gebruikt de eigenaar van de toepassing het toegangspaneel om een gastgebruiker uit te nodigen voor een app of een gastgebruiker toe te voegen aan een groep die toegang heeft tot de app. Selfservice-appbeheer voor galerij- en SAML-gebaseerde apps vereist een eerste installatie door een beheerder. Het volgende is een overzicht van de installatiestappen (zie [Voorwaarden later](#prerequisites) op deze pagina voor meer gedetailleerde instructies):

 - Selfservicegroepsbeheer inschakelen voor uw tenant
 - Een groep maken die aan de app moet worden toegewezen en van de gebruiker een eigenaar maken
 - De app configureren voor selfservice en de groep toewijzen aan de app

> [!NOTE]
> In dit artikel wordt beschreven hoe u selfservicebeheer instelt voor galerie- en SAML-apps die u hebt toegevoegd aan uw Azure AD-tenant. U ook [zelfbedieningsgroepen voor Office 365 instellen,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) zodat uw gebruikers de toegang tot hun eigen Office 365-groepen kunnen beheren. Zie [Gasttoegang in Office 365-groepen](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) en [SharePoint-bestanden of -mappen](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c)voor meer manieren waarop ze Office-bestanden en -apps kunnen delen met gastgebruikers.

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Een gastgebruiker uitnodigen voor een app vanuit het Toegangspaneel

Nadat een app is geconfigureerd voor selfservice, kunnen eigenaren van toepassingen hun eigen toegangspaneel gebruiken om een gastgebruiker uit te nodigen voor de app die hij wil delen. De gastgebruiker hoeft niet per se vooraf aan Azure AD te worden toegevoegd. 

1. Open uw toegangspaneel `https://myapps.microsoft.com`door naar .
2. Wijs de app aan, selecteer de ellipsen (**...**) en selecteer **App beheren**.
 
   ![Schermafbeelding van het submenu App beheren voor de Salesforce-app](media/add-users-iw/access-panel-manage-app.png)
 
3. Selecteer boven aan de gebruikerslijst . **+**
   
   ![Schermafbeelding van het plussymbool voor het toevoegen van leden aan de app](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Typ in het zoekvak **Leden toevoegen** het e-mailadres voor de gastgebruiker. U kunt optioneel een welkomstbericht toevoegen.
   
   ![Schermafbeelding van het venster Leden toevoegen voor het toevoegen van een gast](media/add-users-iw/access-panel-invitation.png)
   
5. Selecteer **Toevoegen** om een uitnodiging naar de gastgebruiker te verzenden. Nadat u de uitnodiging verzendt, wordt het gebruikersaccount automatisch toegevoegd aan de map als gast.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Iemand uitnodigen om lid te worden van een groep die toegang heeft tot de app
Nadat een app is geconfigureerd voor selfservice, kunnen eigenaren van toepassingen gastgebruikers uitnodigen voor de groepen die ze beheren en die toegang hebben tot de apps die ze willen delen. De gastgebruikers hoeven nog niet in de directory te staan. De eigenaar van de toepassing volgt deze stappen om een gastgebruiker uit te nodigen voor de groep, zodat deze toegang heeft tot de app.

1. Zorg ervoor dat u eigenaar bent van de selfservicegroep die toegang heeft tot de app die u wilt delen.
2. Open uw toegangspaneel `https://myapps.microsoft.com`door naar .
3. Selecteer de app **Groepen.**
   
   ![Schermafbeelding van de app Groepen in het toegangspaneel](media/add-users-iw/access-panel-groups.png)
   
4. Selecteer **onder Groepen die ik bezit**de groep die toegang heeft tot de app die u wilt delen.
   
   ![Schermafbeelding van de plaats waarin wordt weergegeven waar u een groep selecteren onder de groepen die ik bezit](media/add-users-iw/access-panel-groups-i-own.png)
   
5. Selecteer boven aan de lijst **+** met groepsleden .
   
   ![Schermafbeelding van het plussymbool voor het toevoegen van leden aan de groep](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Typ in het zoekvak **Leden toevoegen** het e-mailadres voor de gastgebruiker. U kunt optioneel een welkomstbericht toevoegen.
   
   ![Schermafbeelding van het venster Leden toevoegen voor het toevoegen van een gast](media/add-users-iw/access-panel-invitation.png)
   
7. Selecteer **Toevoegen** om de uitnodiging automatisch naar de gastgebruiker te verzenden. Nadat u de uitnodiging verzendt, wordt het gebruikersaccount automatisch toegevoegd aan de map als gast.


## <a name="prerequisites"></a>Vereisten

Selfservice-appbeheer vereist een eerste installatie door een globale beheerder en een Azure AD-beheerder. Als onderdeel van deze instelling configureert u de app voor selfservice en wijst u een groep toe aan de app die de eigenaar van de toepassing kan beheren. U de groep ook zo configureren dat iedereen lidmaatschap kan aanvragen, maar de goedkeuring van een groepseigenaar vereist. (Meer informatie over [selfservicegroepsbeheer](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management).) 

> [!NOTE]
> U gastgebruikers niet toevoegen aan een dynamische groep of aan een groep die is gesynchroniseerd met on-premises Active Directory.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Selfservicegroepsbeheer inschakelen voor uw tenant
1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als globale beheerder.
2. Selecteer Azure Active **Directory**in het navigatiedeelvenster .
3. Selecteer **Groepen**.
4. Selecteer **Onder Instellingen**de optie **Algemeen**.
5. Onder **Self Service Group Management**kunnen naast Eigenaren **groepslidmaatschapsaanvragen beheren in het access panel**, selecteer **Ja**.
6. Selecteer **Opslaan**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Een groep maken die aan de app moet worden toegewezen en van de gebruiker een eigenaar maken
1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als Azure AD-beheerder of globale beheerder.
2. Selecteer Azure Active **Directory**in het navigatiedeelvenster .
3. Selecteer **Groepen**.
4. Selecteer **Nieuwe groep**.
5. Selecteer Onder **Groepstype**de optie **Beveiliging**.
6. Geef een **groepsnaam** en een **beschrijving** voor de groep op.
7. Selecteer Toegewezen onder **lidmaatschapstype** **.**
8. Selecteer **Maken**en sluit de pagina **Groep.**
9. Open de groep op de pagina **Groepen - Alle groepen.** 
10. **Owners** > Selecteer Eigenaren Eigenaren toevoegen onder **Beheren.****Add owners** Zoek naar de gebruiker die de toegang tot de toepassing moet beheren. Selecteer de gebruiker en klik op **Selecteren**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>De app configureren voor selfservice en de groep toewijzen aan de app
1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als Azure AD-beheerder of globale beheerder.
2. Selecteer Azure Active **Directory**in het navigatiedeelvenster .
3. Selecteer **onder Beheren**de optie Alle toepassingen **van** > **All applications**Ondernemingen selecteren .
4. Zoek en open de app in de lijst met toepassingen.
5. Selecteer **onder Beheren**de optie Eén **aanmelding**en configureer de toepassing voor eenmalige aanmelding. (Zie voor meer informatie [hoe u eenmalige aanmelding voor bedrijfsapps beheert](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).)
6. Selecteer **onder Beheren**de optie **Selfservice**en stel zelfservice-app-toegang in. (Zie voor meer informatie [hoe u toegang tot de selfservice-app gebruiken](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).) 

    > [!NOTE]
    > Voor de instelling **Aan welke groep moeten gebruikers worden toegewezen?**
7. Selecteer **onder Beheren**de optie Gebruikers en **groepen**en controleer of de zelfservicegroep die u hebt gemaakt, wordt weergegeven in de lijst.
8. Als u de app wilt toevoegen aan het toegangspaneel van de groepseigenaar, selecteert u **Gebruikersgebruikers** > **en -groepen**toevoegen . Zoek naar de groepseigenaar en selecteer de gebruiker, klik op **Selecteren**en klik vervolgens op **Toewijzen** om de gebruiker aan de app toe te voegen.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen over Azure AD B2B-samenwerking:

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Hoe voegen Azure Active Directory-beheerders B2B-samenwerkingsgebruikers toe?](add-users-administrator.md)
- [B2B-uitnodigingsuitnodiging en inwisseling](redemption-experience.md)
- [Licentie voor Azure AD B2B-samenwerking](licensing-guidance.md)
