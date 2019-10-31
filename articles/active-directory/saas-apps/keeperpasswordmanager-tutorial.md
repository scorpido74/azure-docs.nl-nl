---
title: 'Zelf studie: integratie met wachtwoord beheer van keeper Azure Active Directory & digitale kluis | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Keeper Password Manager & Digital Vault configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e1a98f6a-2dae-4734-bdbf-4fba742a61d2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71fecbe924c1511c247ff846d3b2a39d309ecf0d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159900"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Zelf studie: integratie met wachtwoord beheer van keeper Azure Active Directory & digitale kluis

In deze zelfstudie leert u hoe u Keeper Password Manager & Digital Vault kunt integreren met Azure Active Directory (Azure AD).
De integratie van Keeper Password Manager & Digital Vault met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Keeper Password Manager & Digital Vault.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Keeper Password Manager & Digital Vault (eenmalige aanmelding of SSO).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Keeper Password Manager & Digital Vault hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Keeper Password Manager & Digital Vault waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Keeper Password Manager & Digital Vault ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Keeper Password Manager & Digital Vault ondersteunt **just in time** inrichten van gebruikers

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Keeper Password Manager & Digital Vault toevoegen vanuit de galerie

Om de integratie van Keeper Password Manager & Digital Vault te configureren in Azure AD, moet u Keeper Password Manager & Digital Vault vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Keeper Password Manager & Digital Vault vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Keeper Password Manager & Digital Vault** in het zoekvak, selecteer **Keeper Password Manager & Digital Vault** in het deelvenster met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Keeper Password Manager & Digital Vault toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Keeper Password Manager & Digital Vault op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Keeper Password Manager & Digital Vault tot stand is gebracht.

Om eenmalige aanmelding van Azure AD met Keeper Password Manager & Digital Vault te configureren en testen, moet u de volgende procedures voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **Eenmalige aanmelding configureren voor Keeper Password Manager & Digital Vault configureren**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Keeper Password Manager & Digital Vault maken](#create-keeper-password-manager--digital-vault-test-user)** : als u een tegenhanger van Britta Simon in Keeper Password Manager & Digital Vault wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met Keeper Password Manager & Digital Vault:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina van de integratie van **Keeper Password Manager & Digital Vault** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens van domein en URL's voor eenmalige aanmelding van Keeper Password Manager & Digital Vault](common/sp-identifier-reply.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://{SSO CONNECT SERVER}/sso-connect/saml/login`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://{SSO CONNECT SERVER}/sso-connect`

    c. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon: `https://{SSO CONNECT SERVER}/sso-connect/saml/sso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [ondersteuningsteam van Keeper Password Manager & Digital Vault](https://keepersecurity.com/contact.html) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Ga op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** en klik op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in het gedeelte **Keeper Password Manager & Digital Vault instellen** de juiste URL('s) op basis van wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-keeper-password-manager--digital-vault-single-sign-on"></a>Eenmalige aanmelding configureren voor Keeper Password Manager & Digital Vault

Als u eenmalige aanmelding wilt configureren in **Keeper Password Manager & Digital Vault Configuration**, volgt u de richtlijnen in de [Setup Guide van Keeper SSO Connect](https://keepersecurity.com/assets/pdf/KeeperSSOConnect_v11.pdf).

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. Typ in het veld **gebruikers naam** **brittasimon\@yourcompanydomain. extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte geeft u Britta Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Keeper Password Manager & Digital Vault.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Keeper Password Manager & Digital Vault**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Keeper Password Manager & Digital Vault** in de lijst met toepassingen.

    ![De koppeling naar Keeper Password Manager & Digital Vault in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Testgebruiker maken voor Keeper Password Manager & Digital Vault

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Keeper Password Manager & Digital Vault, moeten ze worden ingericht bij Keeper Password Manager & Digital Vault. De toepassing biedt ondersteuning voor just in time-gebruikersinrichting. Dit betekent dat gebruikers na verificatie automatisch worden aangemaakt in de toepassing. Neem contact op met [Keeper Support](https://keepersecurity.com/contact.html) als u de gebruikers handmatig wilt instellen.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Keeper Password Manager & Digital Vault klikt, wordt u automatisch aangemeld bij de instantie van Keeper Password Manager & Digital Vault waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

