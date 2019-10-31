---
title: 'Zelf studie: integratie met Halo-software Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Halogen Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 220fa6bf16bf92f1907002100dc46895a9807251
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159139"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Zelf studie: integratie met Halo-software Azure Active Directory

In deze zelfstudie leert u hoe u Halogen Software kunt integreren met Azure Active Directory (Azure AD).
De integratie van Halogen Software met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang tot Halogen Software heeft.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Halogen Software (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Halogen Software hebt u de volgende zaken nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Halogen Software waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Halogen Software ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-halogen-software-from-the-gallery"></a>Halogeen Software toevoegen vanuit de galerie

Voor het configureren van de integratie van Halogen Software met Azure AD moet u Halogen Software uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Halogen Software vanuit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Halogen Software** in het zoekvak, selecteer **Halogen Software** in het deelvenster met resultaten en klik op **Toevoegen** om de toepassing toe te voegen.

     ![Halogen Software in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij Halogen Software configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Halogen Software tot stand is gebracht.

Voor het configureren en testen van eenmalige aanmelding via Azure AD bij Halogen Software moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Halogen Software configureren](#configure-halogen-software-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Halogen Software-testgebruiker maken](#create-halogen-software-test-user)** : als u een equivalent van Britta Simon in Halogen Software wilt hebben dat gekoppeld is aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met Halogen Software moet u de volgende stappen uitvoeren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **Halogen Software** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij het Halogen Software-domein en Halogen Software-URL's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://global.hgncloud.com/<companyname>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon:

    | |
    |--|
    | `https://global.halogensoftware.com/<companyname>`|
    | `https://global.hgncloud.com/<companyname>`|
    | |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteuningsteam van Halogen Software](https://support.halogensoftware.com/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Ga op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** en klik op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **Halogen Software instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-halogen-software-single-sign-on"></a>Eenmalige aanmelding voor Halogen Software configureren

1. Meld u in een ander browservenster als beheerder aan bij de **Halogen Software**-toepassing.

2. Klik op het tabblad **Options**.
  
    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_12.png)

3. Klik in het linkernavigatiedeelvenster op **SAML Configuration**.
  
    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_13.png)

4. Voer de volgende stappen uit op de pagina **SAML Configuration**:

    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_14.png)

    a. Selecteer bij **Unique Identifier** de waarde **NameID**.

    b. Selecteer bij **Unique Identifier Maps To** de waarde **Username**.
  
    c. Klik op **Browse**, selecteer het gedownloade metagegevensbestand dat u wilt uploaden, en selecteer vervolgens **Upload File**.

    d. Klik op **Run Test** om de configuratie te testen.

    > [!NOTE]
    > U moet wachten op het bericht '*de SAML-test is voltooid. Sluit dit venster*. Sluit vervolgens het geopende browservenster. Het selectievakje **SAML inschakelen** is pas beschikbaar als de test is voltooid.

    e. Selecteer **SAML inschakelen**.

    f. Klik op **Wijzigingen opslaan**.

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

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Halogen Software.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Halogen Software**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Halogen Software** in de lijst met toepassingen.

    ![De Halogen Software-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-halogen-software-test-user"></a>Halogen Software-testgebruiker maken

Het doel van dit gedeelte is om een gebruiker met de naam Britta Simon te maken in Halogen Software.

**Voer de volgende stappen uit om een gebruiker met de naam Britta Simon te maken in Halogen Software:**

1. Meld u aan bij uw **Halogen Software**-toepassing als beheerder.

2. Klik op het tabblad **User Center** tabblad en klik vervolgens op **Create User**.

    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_300.png)  

3. Voer de volgende stappen uit in het dialoogvenster **New User**:

    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_301.png)

    a. Typ in het tekstvak **First Name** de voornaam van de gebruiker, bijvoorbeeld **Britta**.

    b. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, bijvoorbeeld **Simon**.

    c. Typ in het tekstvak **Username** de gebruikersnaam **Britta Simon**, zoals in de Azure-portal.

    d. Typ in het tekstvak **Password** een wachtwoord voor Britta.

    e. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Halogen Software klikt, wordt u als het goed is automatisch aangemeld bij de instantie van Halogen Software waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)