---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met OpenAthens | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en OpenAthens configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43fc2272a81672ea613bdcbe17c5381e99cafbff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73053201"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met OpenAthens

In deze zelfstudie leert u hoe u OpenAthens integreert met Azure Active Directory (Azure AD). Wanneer u OpenAthens integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot OpenAthens.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij OpenAthens met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* OpenAthens single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* OpenAthens ondersteunt door **IDP** geïnitieerde eenmalige aanmelding
* OpenAthens biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers

## <a name="adding-openathens-from-the-gallery"></a>OpenAthens toevoegen vanuit de galerie

Om de integratie van OpenAthens te configureren in Azure AD, moet u OpenAthens vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **OpenAthene** in het zoekvak in de sectie **Toevoegen in de galerijsectie.**
1. Selecteer **OpenAthene in** het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Azure AD-aanmelding voor OpenAthens configureren en testen

Azure AD SSO configureren en testen met OpenAthens met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in OpenAthens.

Als u Azure AD SSO wilt configureren en testen met OpenAthens, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer OpenAthens SSO](#configure-openathens-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[OpenAthens-testgebruiker maken](#create-openathens-test-user)** - om een tegenhanger van B.Simon in OpenAthens te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **OpenAthens-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Upload in de sectie **Standaard SAML-configuratie** het **bestand met metagegevens van de serviceprovider**. De instructies hiervoor vindt u verderop in deze zelfstudie.

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevens uploaden voor OpenAthens](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Bestand met metagegevens selecteren en uploaden voor OpenAthens](common/browse-upload-metadata.png)

    c. Zodra het bestand met metagegevens is geüpload, wordt de waarde voor **Id** automatisch ingevuld in de sectie **Standaard SAML-configuratie**:

    ![Gegevens van domein en URL's voor eenmalige aanmelding van OpenAthens](common/idp-identifier.png)

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **OpenAthens instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot OpenAthens.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **OpenAthene**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-openathens-sso"></a>OpenAthens SSO configureren

1. Meld u in een ander browservenster aan bij uw bedrijfssite van OpenAthens als beheerder.

1. Selecteer **Connections** in de lijst onder het tabblad **Management**.

    ![Eenmalige aanmelding configureren](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Selecteer **SAML 1.1/2.0** en selecteer vervolgens de knop **Configure**.

    ![Eenmalige aanmelding configureren](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. Als u de configuratie wilt toevoegen, selecteert u de knop **Browse** om het XML-bestand met metagegevens te uploaden dat u hebt gedownload uit de Azure-portal en selecteert u vervolgens **Add**.

    ![Eenmalige aanmelding configureren](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Voer de volgende stappen uit op het tabblad **Details**.

    ![Eenmalige aanmelding configureren](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. Selecteer **Use attribute** bij **Display name mapping**.

    b. Typ de waarde `http://schema.microsoft.com/identity/claims/displayname` in het tekstvak **Display name attribute**.

    c. Selecteer **Use attribute** bij **Unique user mapping**.

    d. Typ de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` in het tekstvak **Unique user attribute**.

    e. SchakeI bij **Status** alle drie de selectievakjes in.

    f. Selecteer **automatically** bij **Create local accounts**.

    g. Selecteer **Wijzigingen opslaan**.

    h. Ga naar het tabblad **</> Relying Party**, kopieer de waarde van **Metadata URL** en open deze URL in de browser om het **XML-bestand met metagegevens van de SP** te downloaden. Upload dit bestand met metagegevensbestand van de serviceprovider naar de sectie **Standaard SAML-configuratie** in Azure AD.

    ![Eenmalige aanmelding configureren](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>Een testgebruiker maken voor OpenAthens

In dit gedeelte wordt een gebruiker met de naam Britta Simon gemaakt in OpenAthens. OpenAthens biedt ondersteuning voor **Just-In-Time-inrichting van gebruikers**. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in OpenAthens bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel OpenAthens klikt, wordt u automatisch aangemeld bij de instantie van OpenAthens waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer OpenAthens met Azure AD](https://aad.portal.azure.com/)