---
title: 'Zelf studie: integratie Azure Active Directory met KnowBe4 Security bewustzijn training | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en KnowBe4 Security Awareness Training.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f774357dc2fd1e37466c2c338e8e8616ae599d12
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159668"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Zelf studie: integratie Azure Active Directory met KnowBe4 Security bewustzijns training

In deze zelfstudie leert u hoe u KnowBe4 Security Awareness Training kunt integreren met Azure Active Directory (Azure AD).
Integratie van KnowBe4 Security Awareness Training met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot KnowBe4 Security Awareness Training.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij KnowBe4 Security Awareness Training (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor de configuratie van Azure AD-integratie met KnowBe4 Security Awareness Training, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op KnowBe4 Security Awareness Training waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* KnowBe4 Security Awareness Training ondersteunt door **IDP** geïnitieerde SSO

* KnowBe4 Security Awareness Training ondersteunt **JIT**-inrichting (Just-in-Time) van gebruikers

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>KnowBe4 Security Awareness Training toevoegen vanuit de galerie

Voor het configureren van de integratie van KnowBe4 Security Awareness Training in Azure AD, moet u KnowBe4 Security Awareness Training vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u KnowBe4 Security Awareness Training wilt toevoegen vanuit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **KnowBe4 Security Awareness Training** in het zoekvak, selecteer **KnowBe4 Security Awareness Training** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![KnowBe4 Security Awareness Training in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met KnowBe4 Security Awareness Training met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in KnowBe4 Security Awareness Training tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met KnowBe4 Security Awareness Training wilt configureren en testen, moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor KnowBe4 Security Awareness Training configureren](#configure-knowbe4-security-awareness-training-single-sign-on)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker maken voor KnowBe4-Awareness Training](#create-knowbe4-security-awareness-training-test-user)** : als u een tegenhanger van Britta Simon in KnowBe4 Security Awareness Training wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met KnowBe4 Security Awareness Training, moet u de volgende stappen uitvoeren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de overzichtspagina van de integratie voor **KnowBe4 Security Awareness Training** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij het KnowBe4 Security Awareness Training-domein en URL's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE]
    > De waarde van de aanmeldings-URL is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [het ondersteuningsteam van KnowBe4 Security Awareness Training Client](mailto:support@KnowBe4.com) om deze waarde te verkrijgen. U kunt ook het patroon raadplegen in de sectie **Standaard SAML-configuratie** van de Azure-portal.

    b. Typ de waarde van de tekenreeks in het tekstvak **Id (Entiteits-id)** : `KnowBe4`

    > [!NOTE]
    > Deze is hoofdlettergevoelig.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Raw)** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

6. In de sectie **KnowBe4 Security Awareness Training instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-knowbe4-security-awareness-training-single-sign-on"></a>Eenmalige aanmelding configureren voor KnowBe4 Security Awareness Training

Om eenmalige aanmelding te configureren aan de kant van **KnowBe4 Security Awareness Training**, moet u het gedownloade **certificaat (Raw)** en de juiste uit de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot KnowBe4 Security Awareness Training.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **KnowBe4 Security Awareness Training**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **KnowBe4-Awareness beveiligingstraining** in de lijst met toepassingen.

    ![De koppeling KnowBe4 Security Awareness Training in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-knowbe4-security-awareness-training-test-user"></a>Een KnowBe4 Security Awareness Training-testgebruiker maken

Het doel van deze sectie is om een gebruiker met de naam Britta Simon te maken in KnowBe4 Security Awareness Training. KnowBe4 Security Awareness Training biedt ondersteuning voor JIT-inrichting (Just-in-Time). Dit is standaard ingeschakeld.

Er is geen actie-item voor u in deze sectie. Er wordt eventueel een nieuwe gebruiker gemaakt bij een poging KnowBe4 Security Awareness Training te openen.

> [!NOTE]
> Als u handmatig een gebruiker wilt maken, moet u contact opnemen met het [ondersteuningsteam van KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel KnowBe4 Security Awareness Training klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van KnowBe4 Security Awareness Training waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
