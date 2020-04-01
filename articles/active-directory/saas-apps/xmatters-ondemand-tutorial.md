---
title: 'Zelfstudie: Azure Active Directory-integratie met xMatters OnDemand | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: e8ae31122d59238ac104d7d873cf56f32977c9af
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086505"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Zelfstudie: Azure Active Directory-integratie met xMatters OnDemand

In deze zelfstudie leert u hoe u xMatters OnDemand integreert met Azure Active Directory (Azure AD).
De integratie van xMatters OnDemand met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot xMatters OnDemand.
* U uw gebruikers automatisch laten inlogen bij xMatters OnDemand (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie wilt configureren met xMatters OnDemand, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/)krijgen.
* xMatters OnDemand-abonnement met één aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* xMatters OnDemand ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>XMatters OnDemand toevoegen vanuit de galerie

Als u de integratie van xMatters OnDemand in Azure AD wilt configureren, moet u xMatters OnDemand uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om xMatters OnDemand toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **xMatters**OnDemand in het zoekvak , selecteer **xMatters OnDemand** in het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![xMatters OnDemand in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met xMatters OnDemand op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in xMatters OnDemand.

Als u Azure AD single sign-on wilt configureren en testen met xMatters OnDemand, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer xMatters OnDemand Single Sign-On](#configure-xmatters-ondemand-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak xMatters OnDemand-testgebruiker](#create-xmatters-ondemand-test-user)** - om een tegenhanger van Britta Simon te hebben in xMatters OnDemand die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met xMatters OnDemand te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **xMatters OnDemand-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![xMatters OnDemand-domein en URL's meteenaanmeldingsinformatie](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: 

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|
    | |

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: 

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|
    | |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met [het ondersteuningsteam van xMatters OnDemand Client](https://www.xmatters.com/company/contact-us/) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

    > [!IMPORTANT]
    > U dient het certificaat door te sturen naar het [xMatters OnDemand support team.](https://www.xmatters.com/company/contact-us/) Het certificaat moet worden geüpload door het ondersteuningsteam van xMatters voordat u de configuratie met één aanmelding afronden.

6. Kopieer in de sectie **XMatters OnDemand instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-xmatters-ondemand-single-sign-on"></a>XMatters OnDemand-aanmelding configureren

1. Meld u in een ander browservenster aan bij uw XMatters OnDemand-bedrijfssite als beheerder.

2. Klik op de werkbalk bovenaan op **Beheerder**en klik vervolgens op **Bedrijfsgegevens** in de navigatiebalk aan de linkerkant.

    ![Beheerder](./media/xmatters-ondemand-tutorial/IC776795.png "Beheerder")

3. Voer de volgende stappen uit op de pagina **SAML Configuration**:

    ![SAML-configuratie](./media/xmatters-ondemand-tutorial/IC776796.png "SAML-configuratie")

    a. Selecteer **SAML inschakelen**.

    b. Plak in het tekstvak **Identiteitsprovider-id** de waarde van **Azure AD-id** plakken die u hebt gekopieerd van de Azure-portal.

    c. Plak in het tekstvak **URL-aan-account** **de URL-waarde van aanmelding** die u hebt gekopieerd uit de Azure-portal.

    d. Plak in het tekstvak **URL van één afmelding** **de URL van afmelden**, die u hebt gekopieerd uit de Azure-portal.

    e. Klik op de pagina Bedrijfsgegevens bovenaan op **Wijzigingen opslaan**.

    ![Bedrijfsgegevens](./media/xmatters-ondemand-tutorial/IC776797.png "Bedrijfsgegevens")

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In **User name** het veld brittasimon@yourcompanydomain.extensiontype gebruikersnaam . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot xMatters OnDemand.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **vervolgens xMatters OnDemand**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **xMatters OnDemand**in de lijst met toepassingen .

    ![De xMatters OnDemand-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-xmatters-ondemand-test-user"></a>XMatters OnDemand-testgebruiker maken

Het doel van deze sectie is het creëren van een gebruiker genaamd Britta Simon in xMatters OnDemand.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

1. Meld u aan bij uw **XMatters OnDemand-huurder.**

2. Klik op het tabblad **Gebruikers** en klik vervolgens op **Gebruiker toevoegen**.

    ![Gebruikers](./media/xmatters-ondemand-tutorial/IC781048.png "Gebruikers")

3. Voer in de sectie **Een gebruiker toevoegen** de volgende stappen uit:

    ![Een gebruiker toevoegen](./media/xmatters-ondemand-tutorial/IC781049.png "Een gebruiker toevoegen")

    a. Selecteer **Active**.

    b. Typ in het tekstvak **gebruikersnaam** de gebruikersnaam Brittasimon@contoso.comvan de gebruiker als .

    c. Typ in het tekstvak **First Name** de voornaam van de gebruiker, bijvoorbeeld Britta.

    d. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, bijvoorbeeld Simon.

    e. Voer in het tekstvak **Site** de geldige site in van een geldig Azure AD-account dat u wilt inrichten.

    f. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel xMatters OnDemand in het access panel klikt, moet u automatisch worden aangemeld bij de xMatters OnDemand waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

