---
title: 'Zelfstudie: Azure Active Directory-integratie met Zoho One | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zoho One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 0a37789e7c7efeb71770ff0e8061d57e6603b6c4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086238"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Zelfstudie: Azure Active Directory-integratie met Zoho One

In deze zelfstudie leert u hoe u Zoho One integreert met Azure Active Directory (Azure AD).
De integratie van Zoho One met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Zoho One.
* U uw gebruikers automatisch laten aanmelden bij Zoho One (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u de AD-integratie met Zoho One wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Zoho One-abonnement met aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zoho One ondersteunt **SP** en **IDP** geïnitieerd sso

## <a name="adding-zoho-one-from-the-gallery"></a>Zoho One toevoegen vanuit de galerie

Als u de integratie van Zoho One in Azure AD wilt configureren, moet u Zoho One vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Zoho One uit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Zoho One**in het zoekvak, selecteer **Zoho One** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Zoho One in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Zoho One op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Zoho One.

Als u Azure AD-singlesign-aan met Zoho One wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Zoho One Sign-On configureren](#configure-zoho-one-single-sign-on)** om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Zoho One-testgebruiker](#create-zoho-one-test-user)** - om een tegenhanger van Britta Simon in Zoho One te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de volgende stappen uit te voeren voor het configureren van Azure AD AD single sign-on met Zoho One:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Zoho** One-toepassingsintegratie de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de **idp-modus** wilt configureren:

    ![Zoho One Domain en URL's single sign-on informatie](common/idp-relay.png)

    a. Typ een URL in het tekstvak **Id:**`one.zoho.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > De voorgaande **URL-waarde voor antwoord** is niet echt. U krijgt `<saml-identifier>` de waarde van #step4 van **Configureren Zoho One Single Sign-On** sectie, die later wordt uitgelegd in de tutorial.

    c. Klik op **Extra URL's instellen**.

    d. In het tekstvak **Relaystatus** typt u een URL: `https://one.zoho.com`

5. Als u de toepassing in de door **SP** gestarte modus wilt configureren, voert u de volgende stap uit:


    ![Zoho One Domain en URL's single sign-on informatie](common/both-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > De voorgaande **URL-waarde van aanmelding** is niet echt. U werkt de waarde bij met de werkelijke aanmeldings-URL vanuit de sectie **Zoho One Sign-On configureren,** die later in de zelfstudie wordt uitgelegd. 

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer in de sectie **Zoho One instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-zoho-one-single-sign-on"></a>Zoho One Sign-On configureren

1. Meld u in een ander browservenster aan bij uw Zoho One-bedrijfssite als beheerder.

2. Klik op het tabblad **Organisatie** op **Instellen** onder **SAML-verificatie**.

    ![Zoho One organisatie](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Voer op de pop-uppagina de volgende stappen uit:

    ![Zoho Een sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Plak in het tekstvak **AanmeldingsURL** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd vanuit azure-portal.

    b. Plak in het tekstvak **Voor uitlogURL** de waarde van de URL van **Afmelden**, die u hebt gekopieerd van azure-portal.

    c. Klik **op Bladeren** om het certificaat **(Base64)** te uploaden dat u hebt gedownload van azure-portal.

    d. Klik op **Opslaan**.

4. Nadat u de instelling SAML-verificatie hebt opgeslagen, kopieert u de **SAML-id-waarde** en plaatst u deze met de **URL van antwoord** in plaats van `<saml-identifier>`, zoals `https://accounts.zoho.com/samlresponse/one.zoho.com` en plakt u de gegenereerde waarde in het tekstvak Voor het beantwoorden VAN **URL** onder de sectie **BasisSAML-configuratie.**

    ![Zoho Een saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Ga naar het tabblad **Domeinen** en klik op **Domein toevoegen**.

    ![Zoho One-domein](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Voer **op** de pagina Domein toevoegen de volgende stappen uit:

    ![Zoho One voegt domein toe](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Typ domein zoals contoso.com in het tekstvak **Domeinnaam.**

    b. Klik op**toevoegen**.

    >[!Note]
    >Nadat u het domein hebt toegevoegd, voert [u deze](https://www.zoho.com/one/help/admin-guide/domain-verification.html) stappen uit om uw domein te verifiëren. Zodra het domein is geverifieerd, gebruikt u uw domeinnaam in **de aanmeldings-URL** in de sectie **BasisSAML-configuratie** in azure-portal.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In **User name** het veld `brittasimon@yourcompanydomain.extension`type gebruikersnaam . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Zoho One.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Zoho One**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zoho One**in de lijst met toepassingen .

    ![De Zoho One-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-zoho-one-test-user"></a>Zoho One-testgebruiker maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij Zoho One, moeten ze zijn ingericht in Zoho One. In Zoho One is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij Zoho One als beveiligingsbeheerder.

2. Klik op het tabblad **Gebruikers** op **het gebruikerslogo**.

    ![Zoho Een gebruiker](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Voer **op** de pagina Gebruiker toevoegen de volgende stappen uit:

    ![Zoho One voegt gebruiker toe](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Voer in **het tekstvak Naam** de naam van de gebruiker in, zoals **Britta Simon.**
    
    b. Voer in het tekstvak **Email Address** het e-mailadres van de gebruiker in, zoals brittasimon@contoso.com.

    >[!Note]
    >Selecteer uw geverifieerde domein in de domeinlijst.

    c. Klik op**toevoegen**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Zoho One-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Zoho One waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

