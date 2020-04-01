---
title: 'Zelfstudie: Azure Active Directory-integratie met Vidyard | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: a55ec7afc94440ea8b6a48ed1507476d362df6c0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087426"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Zelfstudie: Azure Active Directory-integratie met Vidyard

In deze zelfstudie leert u hoe U Vidyard integreren met Azure Active Directory (Azure AD).
De integratie van Vidyard met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Vidyard.
* U uw gebruikers automatisch laten aanmelden bij Vidyard (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met Vidyard wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Abonnement met één aanmelding voor Vidyard

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Vidyard ondersteunt **SP** en **IDP** geïnitieerd sso

* Vidyard ondersteunt **Just In Time** gebruikersinrichting

## <a name="adding-vidyard-from-the-gallery"></a>Vidyard toevoegen vanuit de galerie

Als u de integratie van Vidyard in Azure AD wilt configureren, moet u Vidyard uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Vidyard vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Vidyard**in het zoekvak , selecteer **Vidyard** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Vidyard in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Vidyard op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Vidyard.

Als u Azure AD-singlesign-on met Vidyard wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Vidyard Single Sign-On configureren](#configure-vidyard-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Vidyard-testgebruiker maken](#create-vidyard-test-user)** - om een tegenhanger van Britta Simon in Vidyard te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Vidyard te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Vidyard-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de **idp-modus** wilt configureren:

    ![Vidyard Domain en URL's single sign-on informatie](common/idp-intiated.png)

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Vidyard Domain en URL's single sign-on informatie](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. U werkt deze waarden bij met de werkelijke id, url voor antwoorden en de URL van aanmelding, die later in de zelfstudie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer in de sectie **Vidyard instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-vidyard-single-sign-on"></a>Vidyard-aanmelding configureren

1. Meld u in een ander browservenster aan bij uw vidyard softwarebedrijfssite als beheerder.

2. Selecteer > **groepsbeveiliging** in **Group**het dashboard Vidyard

    ![Vidyard-configuratie](./media/vidyard-tutorial/configure1.png)

3. Klik op het tabblad **Nieuw profiel.**

    ![Vidyard-configuratie](./media/vidyard-tutorial/configure2.png)

4. Voer in de sectie **SAML-configuratie** de volgende stappen uit:

    ![Vidyard-configuratie](./media/vidyard-tutorial/configure3.png)

    a. Voer de algemene profielnaam in het tekstvak **Profielnaam** in.

    b. Kopieer de waarde **van de sso-gebruikersaanmeldingspagina** en plak deze in **het tekstvak Aanmelden op URL** in de sectie **BasisSAML-configuratie** op azure-portal.

    c. Kopieer **de URL-waarde van ACS** en plak deze in het tekstvak van de URL van **antwoord** in de sectie **BasisSAML-configuratie** op azure-portal.

    d. Kopieer **de URL-waarde van de uitgever/metagegevens** en plak deze in **het tekstvak Identifier** in de sectie **BasisSAML-configuratie** op azure-portal.

    e. Open het gedownloade certificaatbestand van Azure-portal in Kladblok en plak het vervolgens in het tekstvak **X.509 Certificate.**

    f. Plak in het tekstvak **SAML-eindpunt-URL** de waarde van **de inlog-URL** die is gekopieerd uit azure-portal.

    g. Klik op **Bevestigen**.

5. Selecteer toewijzen naast een bestaand profiel op het tabblad Eén aanmelding **smaken**

    ![Vidyard-configuratie](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Zodra u een SSO-profiel hebt gemaakt, wijst u het toe aan elke groep(en) waarvoor gebruikers toegang nodig hebben via Azure. Als de gebruiker niet bestaat binnen de groep waaraan hij is toegewezen, maakt Vidyard automatisch een gebruikersaccount aan en wijst hij zijn rol in realtime toe.

6. Selecteer uw organisatiegroep die zichtbaar is in de groepen die beschikbaar zijn **om toe te wijzen**.

    ![Vidyard-configuratie](./media/vidyard-tutorial/configure5.png)

7. U de toegewezen groepen onder de **groepen die momenteel zijn toegewezen,** bekijken. Selecteer een rol voor de groep per organisatie en klik op **Bevestigen**.

    ![Vidyard-configuratie](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Zie voor meer informatie [dit document](https://knowledge.vidyard.com/hc/articles/360009990033-SAML-based-Single-Sign-On-SSO-in-Vidyard).

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Vidyard.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Vidyard**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Vidyard**in de lijst met toepassingen .

    ![De koppeling Vidyard in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-vidyard-test-user"></a>Vidyard-testgebruiker maken

In deze sectie wordt een gebruiker genaamd Britta Simon gemaakt in Vidyard. Vidyard ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Vidyard, wordt er een nieuwe gemaakt na verificatie.

>[!Note]
>Als u handmatig een gebruiker wilt maken, neemt u contact op met [vidyard support team](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de vidyard-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Vidyard waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

