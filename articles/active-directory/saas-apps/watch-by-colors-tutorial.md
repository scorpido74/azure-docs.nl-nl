---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Watch by Colors | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Watch by Colors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 586a029c-fb8d-4233-b280-103b9ba7102d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d8fdc8ba2337a3be49a4645e48a45120fb0ccec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026087"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Watch by Colors

In deze zelfstudie leert u hoe u Watch by Colors integreert met Azure Active Directory (Azure AD). Wanneer u Watch by Colors integreert met Azure AD, u het als:

* Beheer in Azure AD wie toegang heeft tot Watch by Colors.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Watch by Colors met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Watch by Colors single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Watch by Colors ondersteunt **SP en IDP** geïnitieerde SSO

## <a name="adding-watch-by-colors-from-the-gallery"></a>Horloge op kleuren toevoegen vanuit de galerie

Als u de integratie van Watch by Colors in Azure AD wilt configureren, moet u Watch by Colors uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ In de sectie **Toevoegen in de galerie** het **optiek Aantal kleuren** in het zoekvak.
1. Selecteer **Watch by Colors** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Azure AD-aanmelding voor Watch by Colors configureren en testen

Azure AD SSO configureren en testen met Watch by Colors met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Watch by Colors.

Als u Azure AD SSO wilt configureren en testen met Watch by Colors, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Watch by Colors SSO](#configure-watch-by-colors-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Testgebruiker Watch by Colors maken](#create-watch-by-colors-test-user)** - om een tegenhanger van B.Simon in Watch by Colors te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina De toepassingsintegratie **van Watch by Colors** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd in de **idp-gestarte** modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://app.colorscorporation.com/login`

1. Klik op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** op de knop Kopiëren om **de url van de appfederatie-metagegevens** te kopiëren en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Watch by Colors.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer In de lijst met toepassingen de optie **Bekijken op kleuren**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-watch-by-colors-sso"></a>Horloge configureren op kleuren SSO

1. Als u de configuratie binnen Watch by Colors wilt automatiseren, moet u de **beveiligingsextensie Mijn apps secure sign-in** installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **setup Watch by Colors** en leidt u naar de toepassing Watch by Colors. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Watch by Colors. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-5.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Watch by Colors handmatig wilt instellen, opent u een nieuw webbrowservenster en meldt u zich als beheerder aan bij uw bedrijfssite Watch by Colors en voert u de volgende stappen uit:

4. Klik in de rechterbovenhoek van de pagina op > **profielaccountinstellingen** > **SSO (Single Sign On).** **profile**

    ![Configuratie bekijken op kleuren](./media/watch-by-colors-tutorial/config01.png)

5. Voer op de pagina **SSO (Enkel aanmelding)** de volgende stappen uit:

    ![Configuratie bekijken op kleuren](./media/watch-by-colors-tutorial/config02.png)

    a. Saml **inschakelen** naar **AAN**.

    b. Plak in het **tekstvak URL van** de URL van de url van de **federatie,** die u hebt gekopieerd van Azure-portal.

    c. Klik **op Importeren**en vervolgens worden de volgende velden automatisch ingevuld op de pagina.

    d. Klik op **Opslaan**.

### <a name="create-watch-by-colors-test-user"></a>Testgebruiker Watch by Colors maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij Watch by Colors, moeten ze zijn ingericht in Watch by Colors. In Watch by Colors is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij Watch by Colors als beveiligingsbeheerder.

1. Klik in de rechterbovenhoek van de pagina op **profiel** > **gebruikers** > **toevoegen.**

    ![Configuratie bekijken op kleuren](./media/watch-by-colors-tutorial/config03.png)

1. Voer op de pagina **Gebruikersgegevens** de volgende stappen uit:

    ![Configuratie bekijken op kleuren](./media/watch-by-colors-tutorial/config04.png)

    a. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **B.**

    b. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals **Simon**.

    c. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, zoals `B.Simon@contoso.com`.

    d. Voer **in het** tekstvak Wachtwoord het wachtwoord in.

    e. Selecteer **Accountmachtigingen** per uw organisatie.

    f. Klik op **Opslaan**.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Horloge op kleuren klikt in het toegangspaneel, moet u automatisch worden aangemeld bij het horloge op kleuren waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Bekijk op kleuren proberen met Azure AD](https://aad.portal.azure.com/)

