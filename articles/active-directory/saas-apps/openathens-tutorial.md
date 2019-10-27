---
title: 'Zelf studie: integratie Azure Active Directory met openathene | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en OpenAthens configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81e37e78246a8c021cfdea015a9c4fe7695f5e44
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933990"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Zelf studie: integratie met openathene Azure Active Directory

In deze zelfstudie leert u hoe u OpenAthens kunt integreren met Azure Active Directory (Azure AD).
De integratie van OpenAthens met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot OpenAthens.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij OpenAthens (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met OpenAthens hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op OpenAthens waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* OpenAthens ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

* OpenAthens biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers

## <a name="adding-openathens-from-the-gallery"></a>OpenAthens toevoegen vanuit de galerie

Om de integratie van OpenAthens te configureren in Azure AD, moet u OpenAthens vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om OpenAthens vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **OpenAthens** in het zoekvak, selecteer **OpenAthens** in het deelvenster met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![OpenAthens toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met OpenAthens op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in OpenAthens tot stand is gebracht.

Om eenmalige aanmelding van Azure AD met OpenAthens te configureren en testen, moet u de volgende procedures voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor OpenAthens configureren](#configure-openathens-single-sign-on)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor OpenAthens maken](#create-openathens-test-user)** : als u een tegenhanger van Britta Simon in OpenAthens wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met OpenAthens:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina van de integratie van **OpenAthens** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

5. Upload in de sectie **Standaard SAML-configuratie** het **bestand met metagegevens van de serviceprovider**. De instructies hiervoor vindt u verderop in deze zelfstudie.

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevens uploaden voor OpenAthens](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Bestand met metagegevens selecteren en uploaden voor OpenAthens](common/browse-upload-metadata.png)

    c. Zodra het bestand met metagegevens is geüpload, wordt de waarde voor **Id** automatisch ingevuld in de sectie **Standaard SAML-configuratie**:

    ![Gegevens van domein en URL's voor eenmalige aanmelding van OpenAthens](common/idp-identifier.png)

6. Ga op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** en klik op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-openathens-single-sign-on"></a>Eenmalige aanmelding configureren voor OpenAthens

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van OpenAthens.

2. Selecteer **Connections** in de lijst onder het tabblad **Management**. 

    ![Eenmalige aanmelding configureren](./media/openathens-tutorial/tutorial_openathens_application1.png)

3. Selecteer **SAML 1.1/2.0** en selecteer vervolgens de knop **Configure**.

    ![Eenmalige aanmelding configureren](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
4. Als u de configuratie wilt toevoegen, selecteert u de knop **Browse** om het XML-bestand met metagegevens te uploaden dat u hebt gedownload uit de Azure-portal en selecteert u vervolgens **Add**.

    ![Eenmalige aanmelding configureren](./media/openathens-tutorial/tutorial_openathens_application3.png)

5. Voer de volgende stappen uit op het tabblad **Details**.

    ![Eenmalige aanmelding configureren](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. Selecteer **Use attribute** bij **Display name mapping**.

    b. Typ de waarde `http://schemas.microsoft.com/identity/claims/displayname` in het tekstvak **Display name attribute**.
    
    c. Selecteer **Use attribute** bij **Unique user mapping**.

    d. Typ de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` in het tekstvak **Unique user attribute**.

    e. SchakeI bij **Status** alle drie de selectievakjes in.

    f. Selecteer **automatically** bij **Create local accounts**.

    g. Selecteer **Save changes**.

    h. Ga naar het tabblad **</> Relying Party**, kopieer de waarde van **Metadata URL** en open deze URL in de browser om het **XML-bestand met metagegevens van de SP** te downloaden. Upload dit bestand met metagegevensbestand van de serviceprovider naar de sectie **Standaard SAML-configuratie** in Azure AD.

    ![Eenmalige aanmelding configureren](./media/openathens-tutorial/tutorial_openathens_application5.png)

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot OpenAthens.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **OpenAthens**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **OpenAthens** in de lijst met toepassingen.

    ![De koppeling naar OpenAthens in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-openathens-test-user"></a>Een testgebruiker maken voor OpenAthens

In dit gedeelte wordt een gebruiker met de naam Britta Simon gemaakt in OpenAthens. OpenAthens biedt ondersteuning voor **Just-In-Time-inrichting van gebruikers**. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in OpenAthens bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel OpenAthens klikt, wordt u automatisch aangemeld bij de instantie van OpenAthens waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

