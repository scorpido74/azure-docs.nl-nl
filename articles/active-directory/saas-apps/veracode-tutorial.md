---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Veracode | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcec326ddab1e74f43e1bb7ef446998a40799fd0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73043559"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Veracode

In deze zelfstudie leert u hoe u Veracode integreert met Azure Active Directory (Azure AD). Wanneer u Veracode integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Veracode.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Veracode met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Een Veracode-abonnement (Met Eenmalige aanmeldingsoptie (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. Veracode ondersteunt identity provider geïnitieerde SSO en just-in-time user provisioning.

## <a name="add-veracode-from-the-gallery"></a>Veracode toevoegen vanuit de galerie

Als u de integratie van Veracode in Azure AD wilt configureren, voegt u Veracode uit de galerie toe aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ 'Veracode' in de sectie **Toevoegen in de galerijsectie** in het zoekvak.
1. Selecteer **Veracode** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Azure AD-aanmelding voor Veracode configureren en testen

Azure AD SSO configureren en testen met Veracode met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppeling maken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Veracode.

Als u Azure AD SSO wilt configureren en testen met Veracode, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met B.Simon te testen.
    * **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Veracode SSO](#configure-veracode-sso)** om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak een Veracode-testgebruiker](#create-veracode-test-user)** om een tegenhanger van B.Simon in Veracode te hebben gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Veracode-toepassingsintegratie** de sectie **Beheren.** Selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Selecteer op de pagina **Eén aanmelding instellen met SAML** het potloodpictogram voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Schermafbeelding van Eenmalige aanmelding instellen met SAML, met potloodpictogram gemarkeerd](common/edit-urls.png)

1. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. Selecteer **Opslaan**.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** op **Certificaat (Base64)**. Selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![Schermafbeelding van de sectie SAML-ondertekeningscertificaat, met koppeling Downloaden gemarkeerd](common/certificatebase64.png)

1. Veracode verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Schermafbeelding van de sectie Gebruikerskenmerken & claims](common/default-attributes.png)

1. Veracode verwacht ook nog een paar attributen terug te worden gegeven in de SAML-reactie. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name | Bronkenmerk|
    | ---------------| --------------- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | e-mail |User.mail |

1. Kopieer in de sectie **Veracode instellen** de juiste URL(s) op basis van uw vereiste.

    ![Schermafbeelding van de sectie Veracode instellen, met configuratie-URL's gemarkeerd](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Veracode SSO configureren

1. Meld u in een ander browservenster aan bij uw Veracode-bedrijfssite als beheerder.

1. Selecteer > **Instellingenbeheerder**in het **Settings**menu bovenaan .
   
    ![Schermafbeelding van Veracode-beheer, met pictogram Instellingen en Beheerder gemarkeerd](./media/veracode-tutorial/ic802911.png "Beheer")

1. Selecteer het **tabblad SAML.**

1. Voer in de sectie **SAML-instellingen** voor organisatie de volgende stappen uit:

    ![Schermafbeelding van de sectie SAML-instellingen van de organisatie](./media/veracode-tutorial/ic802912.png "Beheer")

    a.  Plak **bij Uitgever**de waarde van de Azure **AD-id** die u hebt gekopieerd van de Azure-portal.

    b. Selecteer **Bestand kiezen** om uw gedownloade certificaat te uploaden vanuit de Azure-portal voor certificaat **voor bevestigingsondertekening.**

    c. Selecteer **Zelfregistratie inschakelen**voor **zelfregistratie**.

1. Voer in de sectie **Zelfregistratie-instellingen** de volgende stappen uit en selecteer **Opslaan:**

    ![Schermafbeelding van de sectie Zelfregistratie-instellingen, met verschillende opties gemarkeerd](./media/veracode-tutorial/ic802913.png "Beheer")

    a. Selecteer geen activering **nodig**voor **nieuwe gebruikersactivering**.

    b. Selecteer Voor **updates van gebruikersgegevens**de **optie Voorkeursveracode-gebruikersgegevens**.

    c. Selecteer voor **SAML-kenmerkdetails**het volgende:
      * **Gebruikersrollen**
      * **Beleidsbeheerder**
      * **Revisor**
      * **Beveiligingslead**
      * **Leidinggevende**
      * **Inzender**
      * **Schepper**
      * **Alle scantypen**
      * **Teamlidmaatschappen**
      * **Standaardteam**

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory** >**Users** > **All users .**
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:

   1. Voer **Name**voor `B.Simon`Naam .  
   1. Voer **voor gebruikersnaam** username@companydomain.extensionde . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Selecteer **Wachtwoord weergeven**en noteer de getoonde waarde.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

Schakel B.Simon in deze sectie in om Azure single sign-on te gebruiken door toegang te verlenen tot Veracode.

1. Selecteer in de Azure-portal Alle**bedrijfstoepassingen** **.** > 
1. Selecteer **Veracode**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Schermafbeelding van de sectie Beheren, met gemarkeerde gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoogvenster **Toewijzing toevoegen** de optie Gebruikers **en groepen**.

    ![Schermafbeelding van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer **B.Simon**in het dialoogvenster **Gebruikers en groepen** van **Gebruikers**. Kies vervolgens **Selecteer** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **Selecteer** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-veracode-test-user"></a>Veracode-testgebruiker maken

Als u zich wilt aanmelden bij Veracode, moeten Azure AD-gebruikers zijn ingericht in Veracode. Deze taak is geautomatiseerd en u hoeft niets handmatig te doen. Gebruikers worden automatisch gemaakt indien nodig tijdens de eerste enkele aanmeldingspoging.

> [!NOTE]
> U alle andere hulpprogramma's voor het maken van Veracode-gebruikersaccounts of API's die door Veracode worden verstrekt, gebruiken om Azure AD-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Test SSO

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u **Veracode** selecteert in het Toegangspaneel, moet u automatisch worden aangemeld bij de Veracode waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Veracode uitproberen met Azure AD](https://aad.portal.azure.com/)