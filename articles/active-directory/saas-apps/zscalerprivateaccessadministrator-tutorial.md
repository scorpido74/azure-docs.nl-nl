---
title: 'Zelfstudie: Azure Active Directory-integratie met Zscaler Private Access Administrator | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zscaler Private Access Administrator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7f0bc04bb8e7358f5bdb2e4dfc3db198e24303f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67085645"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Zelfstudie: Azure Active Directory-integratie met Zscaler Private Access Administrator

In deze zelfstudie leert u hoe u Zscaler Private Access Administrator integreert met Azure Active Directory (Azure AD).
De integratie van Zscaler Private Access Administrator met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot De Beheerder voor privétoegang van Zscaler.
* U uw gebruikers automatisch laten aanmelden bij Zscaler Private Access Administrator (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met De Beheerder van de privétoegang van Zscaler, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Abonnement voor één aanmelding sein van Zscaler Private Access Administrator

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zscaler Private Access Administrator ondersteunt **SP** en **IDP** geïnitieerd sso

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Zscaler-beheerder voor privétoegang toevoegen aan de galerie

Als u de integratie van De Beheerder voor privétoegang van Zscaler in Azure AD wilt configureren, moet u De Beheerder voor privétoegang van Zscaler vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Zscaler Private Access Administrator toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer in het zoekvak **Zscaler Private Access Administrator**, selecteer **Zscaler Private Access Administrator** uit het resultaatpaneel en klik op Knop **Toevoegen** om de toepassing toe te voegen.

    ![Zscaler Private Access Administrator in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Zscaler Private Access Administrator op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding voor het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in De Privétoegangsbeheerder van Zscaler.

Als u Azure AD-singlesign-aan wilt configureren en testen met De Beheerder voor privétoegang van Zscaler, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer De voormelding van Zscaler Private Access Administrator Single Sign-On](#configure-zscaler-private-access-administrator-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak de testgebruiker van Zscaler Private Access Administrator](#create-zscaler-private-access-administrator-test-user)** - om een tegenhanger van Britta Simon in Zscaler Private Access Administrator te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met De Privétoegangsbeheerder van Zscaler te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina Integratie van de **Zscaler Private Access** Administrator-toepassingsintegratie de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de **idp-modus** wilt configureren:

    ![Zscaler Private Access Administrator Domain en URL's single sign-on information Zscaler Private Access Administrator Domain and URLLs single sign-on information Zscaler Private Access Administrator Domain and URLLs single sign-on information Z](common/idp-relay.png)

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<subdomain>.private.zscaler.com/auth/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Klik op **Extra URL's instellen**.

    d. In het tekstvak **Relaystatus** typt u een URL: `idpadminsso`

5.  Als u de toepassing in de door **SP** gestarte modus wilt configureren, voert u de volgende stap uit:

    ![Zscaler Private Access Administrator Domain en URL's single sign-on information Zscaler Private Access Administrator Domain and URLLs single sign-on information Zscaler Private Access Administrator Domain and URLLs single sign-on information Z](common/both-signonurl.png)

    Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [het ondersteuningsteam voor clientclient van Zscaler Private Access Administrator](https://help.zscaler.com/zpa-submit-ticket) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer in de sectie **Zscaler Private Access Administrator** de juiste URL(s) naar uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Eenmalig aanmelden voor Zscaler-beheerder voor privétoegang configureren

1. Teken in een ander browservenster bij Zscaler Private Access Administrator als beheerder.

2. Klik bovenaan op **Beheer** en navigeer naar **de** sectie VERIFICATIE op **IdP-configuratie**.

    ![Beheerder van de beheerder van de beheerder van de privétoegang van Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klik in de rechterbovenhoek op **IdP-configuratie toevoegen**. 

    ![Zscaler Private Access Administrator addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Voer op de pagina **IdP-configuratie toevoegen** de volgende stappen uit:
 
    ![Zscaler Private Access Administrator idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Klik **op Bestand selecteren** om het gedownloade bestand met ametagegevens van Azure AD te uploaden in het veld **Metagegevensbestand uploaden van idp.**

    b. Het leest de **IdP metadata** van Azure AD en vult alle velden informatie zoals hieronder weergegeven.

    ![Zscaler Private Access Administrator idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Selecteer **Eén aanmelding** als **beheerder**.

    d. Selecteer uw domein in het veld **Domeinen.**
    
    e. Klik op **Opslaan**.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot De Private Access Administrator van Zscaler.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **vervolgens Zscaler Private Access Administrator**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zscaler Private Access Administrator**in de lijst met toepassingen .

    ![De koppeling Voor de zscaler-beheerder voor privétoegang in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Testgebruiker van Zscaler-beheerder voor privétoegang maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij De Beheerder voor privétoegang van Zscaler, moeten ze zijn ingericht in De Beheerder voor privétoegang van Zscaler. In het geval van Zscaler Private Access Administrator is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw bedrijf van de Zscaler Private Access Administrator-beheerder als beheerder.

2. Klik bovenaan op **Beheer** en navigeer naar **de** sectie VERIFICATIE op **IdP-configuratie**.

    ![Beheerder van de beheerder van de beheerder van de privétoegang van Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klik **op Beheerders** aan de linkerkant van het menu.

    ![Beheerder van de beheerder van de beheerder van de beheerder van de beheerder van de privétoegang van Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. Klik in de rechterbovenhoek op **Beheerder toevoegen:**

    ![Zscaler Private Access Administrator voegt beheerder toe](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Voer **op** de pagina Administrator toevoegen de volgende stappen uit:

    ![Gebruikersbeheerder van Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Voer **in** het tekstvak Gebruikersnaam de BrittaSimon@contoso.come-mail van de gebruiker in, zoals .

    b. Typ het tekstvak **Wachtwoord** in het tekstvak Wachtwoord.

    c. Typ het tekstvak **Wachtwoord bevestigen** in het tekstvak Wachtwoord bevestigen.

    d. Selecteer **Rol** als **Beheerder van privétoegang Zscaler**.

    e. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, bijvoorbeeld BrittaSimon@contoso.com.

    f. Typ het telefoonnummer in het tekstvak **Telefoon.**

    g. Selecteer in het tekstvak **Tijdzone** de tijdzone.

    h. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Voor privétoegang seinen Zscaler klikt in het toegangspaneel, moet u automatisch worden aangemeld bij de Zscaler Private Access Administrator waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

