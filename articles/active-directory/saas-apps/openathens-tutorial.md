---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met openathene | Microsoft Docs'
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
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053201"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met openathene

In deze zelf studie leert u hoe u openathene integreert met Azure Active Directory (Azure AD). Wanneer u openathene integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot openathene.
* Stel in dat uw gebruikers zich automatisch kunnen aanmelden bij openathene met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Openstaande abonnement voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* OpenAthens ondersteunt door **IDP** geïnitieerde eenmalige aanmelding
* OpenAthens biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers

## <a name="adding-openathens-from-the-gallery"></a>OpenAthens toevoegen vanuit de galerie

Om de integratie van OpenAthens te configureren in Azure AD, moet u OpenAthens vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ **openathene** in het zoekvak in de sectie **toevoegen vanuit de galerie** .
1. Selecteer **openathene** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Eenmalige aanmelding voor Azure AD voor openathene configureren en testen

Azure AD SSO met openathene configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in openathene.

Als u Azure AD SSO wilt configureren en testen met openathene, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. Open **[Athene SSO configureren](#configure-openathens-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak](#create-openathens-test-user)** een openstaande gebruiker van het soort openliggende test, zodat deze een equivalent van B. Simon in openathene heeft dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **openathene** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Upload in de sectie **Standaard SAML-configuratie** het **bestand met metagegevens van de serviceprovider**. De instructies hiervoor vindt u verderop in deze zelfstudie.

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevens uploaden voor OpenAthens](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Bestand met metagegevens selecteren en uploaden voor OpenAthens](common/browse-upload-metadata.png)

    c. Zodra het bestand met metagegevens is geüpload, wordt de waarde voor **Id** automatisch ingevuld in de sectie **Standaard SAML-configuratie**:

    ![Gegevens van domein en URL's voor eenmalige aanmelding van OpenAthens](common/idp-identifier.png)

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie Open **Athene instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extension in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan openathene.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Openathene**in de lijst toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-openathens-sso"></a>Openathene SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw openathene-bedrijfs site.

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

    g. Selecteer **Save changes**.

    h. Ga naar het tabblad **</> Relying Party**, kopieer de waarde van **Metadata URL** en open deze URL in de browser om het **XML-bestand met metagegevens van de SP** te downloaden. Upload dit bestand met metagegevensbestand van de serviceprovider naar de sectie **Standaard SAML-configuratie** in Azure AD.

    ![Eenmalige aanmelding configureren](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>Een testgebruiker maken voor OpenAthens

In dit gedeelte wordt een gebruiker met de naam Britta Simon gemaakt in OpenAthens. OpenAthens biedt ondersteuning voor **Just-In-Time-inrichting van gebruikers**. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in OpenAthens bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel OpenAthens klikt, wordt u automatisch aangemeld bij de instantie van OpenAthens waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer openathene met Azure AD](https://aad.portal.azure.com/)