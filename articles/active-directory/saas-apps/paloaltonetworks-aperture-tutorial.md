---
title: 'Zelfstudie: Azure Active Directory-integratie met Palo Alto Networks - Aperture | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Palo Alto Networks - Aperture.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: fd498dc1c37ed6e9518fcefbdb237153504b5e98
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095063"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Zelfstudie: Azure Active Directory-integratie met Palo Alto Networks - Aperture

In deze zelfstudie leert u hoe u Palo Alto Networks - Aperture integreert met Azure Active Directory (Azure AD).
Palo Alto Networks integreren - Aperture met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Palo Alto Networks - Aperture.
* U uw gebruikers automatisch laten aanmelden bij Palo Alto Networks - Aperture (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie wilt configureren met Palo Alto Networks - Aperture, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Palo Alto Networks - Aperture single sign-on enabled subscription Palo Alto Networks - Aperture single sign-on enabled subscription Palo Alto Networks - Aperture single sign-on enabled subscription Palo Al

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Palo Alto Networks - Aperture ondersteunt **SP** en **IDP** geïnitieerd SSO

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Palo Alto Networks toevoegen - Diafragma uit de galerij

Als u de integratie van Palo Alto Networks - Aperture in Azure AD wilt configureren, moet u Palo Alto Networks - Aperture vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Palo Alto Networks - Aperture vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Palo Alto Networks - Aperture**, selecteer Palo **Alto Networks - Aperture** uit het resultaatpaneel en klik op Knop **Toevoegen** om de toepassing toe te voegen.

     ![Palo Alto Networks - Aperture in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Palo Alto Networks - Aperture op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding om te werken, moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Palo Alto Networks - Aperture.

Als u Azure AD single sign-on wilt configureren en testen met Palo Alto Networks - Aperture, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Palo Alto Networks - Aperture Single Sign-On](#configure-palo-alto-networks---aperture-single-sign-on)** configureren om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Palo Alto Networks - Aperture test gebruiker](#create-palo-alto-networks---aperture-test-user)** - om een tegenhanger van Britta Simon in Palo Alto Networks hebben - Aperture die is gekoppeld aan de Azure AD vertegenwoordiging van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD te configureren met Palo Alto Networks - Aperture:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Palo Alto Networks -** Aperture-toepassingsintegratie de optie **Enkele aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de **idp-modus** wilt configureren:

    ![Palo Alto Networks - Aperture Domain en URL's single sign-on informatie](common/idp-intiated.png)

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Palo Alto Networks - Aperture Domain en URL's single sign-on informatie](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [Palo Alto Networks - Aperture Client support team](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer in de sectie **Palo Alto Networks - Aperture** de juiste URL(s) naar uw behoefte.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-palo-alto-networks---aperture-single-sign-on"></a>Palo Alto-netwerken configureren - Aperture Single Sign-On

1. Log in een ander browservenster in bij Palo Alto Networks - Aperture als administrator.

2. Klik op de bovenste menubalk op **INSTELLINGEN**.

    ![Het tabblad Instellingen](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. Navigeer naar de sectie **TOEPASSING** klik **op Verificatie** aan de linkerkant van het menu.

    ![Het tabblad Auth](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. Voer **op** de pagina Verificatie de volgende stappen uit:
    
    ![Het tabblad Verificatie](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Controleer het veld **Single Sign-On inschakelen (Ondersteunde SSP-providers zijn Okta, One-login)** vanuit het veld **Eenmalig aanmelden.**

    b. Plak in het tekstvak **Identiteitsprovider-id** de waarde van **Azure AD-id**, die u hebt gekopieerd van Azure-portal.

    c. Klik **op Bestand kiezen** om het gedownloade certificaat van Azure AD te uploaden in het veld Certificaat van de **identiteitsprovider.**

    d. Plak in het tekstvak **Van de URL van de identiteitsprovider** de waarde van de **aanmeldings-URL**, die u hebt gekopieerd van azure-portal.

    e. Bekijk de IdP-informatie uit de sectie **Aperture Info** en download het certificaat van het veld **Aperture Key.**

    f. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension**.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Palo Alto Networks - Aperture.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, Selecteer **Alle toepassingen**en selecteer Vervolgens **Palo Alto Networks - Aperture**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Palo Alto Networks - Aperture**in de lijst met toepassingen .

    ![De Palo Alto Networks - Aperture link in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-palo-alto-networks---aperture-test-user"></a>Palo Alto Networks maken - Aperture test gebruiker

In deze sectie maakt u een gebruiker genaamd Britta Simon in Palo Alto Networks - Aperture. Werk samen met [Palo Alto Networks - Aperture Client support team](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) om de gebruikers toe te voegen in het Palo Alto Networks - Aperture platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de palo alto-netwerken - diafragmategel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Palo Alto Networks - Aperture waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

