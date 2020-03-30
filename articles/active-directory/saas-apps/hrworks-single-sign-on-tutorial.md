---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met HRworks Single Sign-On | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en HRworks Single Sign-On.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b20ce5f754333aec78513e32901b0608f8bee3b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75638727"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met HRworks Single Sign-On

In deze zelfstudie leert u hoe u HRworks Single Sign-On integreert met Azure Active Directory (Azure AD). Wanneer u HRworks Single Sign-On integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot HRworks Single Sign-On.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij HRworks Single Sign-On met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* HRworks Single Sign-On single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* HRworks Single Sign-On ondersteunt **SP** geïnitieerde SSO

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>HRworks Single Sign-On toevoegen vanuit de galerie

Als u de integratie van HRworks Single Sign-On in Azure AD wilt configureren, moet u HRworks Single Sign-On vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ In de sectie **Toevoegen in de galerie** **hrworks Single Sign-On** in het zoekvak.
1. Selecteer **HRworks Single Sign-On** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Azure AD-eenmaligaanmelding configureren en testen voor Eenmalig aanmelden van HRworks

Azure AD SSO configureren en testen met HRworks Single Sign-On met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in HRworks Single Sign-On.

Als u Azure AD SSO wilt configureren en testen met HRworks Single Sign-On, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer HRworks Single Sign-On SSO](#configure-hrworks-single-sign-on-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * Maak de gebruiker van **[de Allerwandige aanmelding smaken van HRworks](#create-hrworks-single-sign-on-test-user)** - om een tegenhanger van B.Simon in HRworks Single Sign-On te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina Integratie van toepassingen voor één aanmelding voor Toepassingen voor **HRworks** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [het Ondersteuningsteam voor één aanmelding seinclient](mailto:nadja.sommerfeld@hrworks.de) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **HRworks Single Sign-On** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot HRworks Single Sign-On.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **hrworks single sign-on**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-hrworks-single-sign-on-sso"></a>HRworks Single Sign-On SSO configureren

1. Als u de configuratie binnen HRworks Single Sign-On wilt automatiseren, moet u **de beveiligingsextensie Mijn apps Secure Sign-in** installeren installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **HRworks Single Sign-On instellen** en leidt u naar de HRworks Single Sign-On-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij HRworks Single Sign-On. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-4.

    ![Configuratie instellen](common/setup-sso.png)

1. Als u Handmatig Eenmalige aanmelding voor HRworks wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij uw bedrijf HRworks Single Sign-On en voert u de volgende stappen uit:

1. Klik op **Administrator** > **Basics** > **Security** > **Single Sign-on** vanaf de linkerkant van de menubalk en voer de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Schakel het selectievakje **Eenmalig aanmelden in.**

    b. Selecteer **XML-metametametameta's** als **meta-gegevensinvoermethode**.

    c. Selecteer **De id-id van afzonderlijke naamalsnummer** als **waarde voor nameID**.

    d. Open in Kladblok de XML met ametten die u hebt gedownload van de Azure-portal, kopieer de inhoud ervan en plak deze vervolgens in het tekstvak **Metagegevens.**

    e. Klik op **Opslaan**.

### <a name="create-hrworks-single-sign-on-test-user"></a>HRworks Single Sign-On testgebruiker maken

Als u Azure AD-gebruikers wilt inschakelen, moet u zich aanmelden bij HRworks Single Sign-On, deze moeten zijn ingericht in HRworks Single Sign-On. In HRworks Single Sign-On is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij HRworks Single Sign-On als beheerder.

1. Klik op > **Persons** > **Beheerderspersonen Nieuwe** > **persoon** aan de linkerkant van de menubalk. **Administrator**

     ![Eenmalige aanmelding configureren](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. Klik in de pop-up op **Volgende**.

    ![Eenmalige aanmelding configureren](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. Vul in de pop-up **Nieuwe persoon maken met land voor juridische termen** de desbetreffende details in, zoals **Voornaam**, **Achternaam** en klik op **Maken**.

    ![Eenmalige aanmelding configureren](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Vooréén aanmelding van HRworks klikt in het toegangspaneel, moet u automatisch worden aangemeld bij de URworks Single Sign-On waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer HRworks Single Sign-On met Azure AD](https://aad.portal.azure.com/)