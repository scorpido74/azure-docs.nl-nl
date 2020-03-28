---
title: 'Zelfstudie: Azure Active Directory-integratie met MobileIron | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en MobileIron configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3e4bbd5b-290e-4951-971b-ec0c1c11aaa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea4e997c6f2d0826c8914c671d625cc1c49bb018
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160457"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Zelfstudie: Azure Active Directory-integratie met MobileIron

In deze zelfstudie leert u hoe u MobileIron kunt integreren met Azure Active Directory (Azure AD).
De integratie van MobileIron met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot MobileIron.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij MobileIron (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met MobileIron hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op MobileIron waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* MobileIron ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-mobileiron-from-the-gallery"></a>MobileIron toevoegen vanuit de galerie

Om de integratie van MobileIron te configureren in Azure AD, moet u MobileIron vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om MobileIron vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **MobileIron**, selecteer **MobileIron** in het deelvenster met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![MobileIron toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met MobileIron op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in MobileIron tot stand is gebracht.

Om eenmalige aanmelding van Azure AD met MobileIron te configureren en testen, moet u de volgende procedures voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor MobileIron configureren](#configure-mobileiron-single-sign-on)**: de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor MobileIron maken](#create-mobileiron-test-user)**: als u een tegenhanger van Britta Simon in MobileIron wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met MobileIron:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina van de integratie van **MobileIron** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in het gedeelte **Standaard SAML-configuratie** de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Gegevens van domein en URL's voor eenmalige aanmelding van MobileIron](common/idp-intiated.png)

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://www.mobileiron.com/<key>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<host>.mobileiron.com/saml/SSO/alias/<key>`

    c. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Gegevens van domein en URL's voor eenmalige aanmelding van MobileIron](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<host>.mobileiron.com/user/login.html`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en aanmeldings-URL. U kunt de waarden voor de sleutel en de host opzoeken in de beheerportal van MobileIron. Dit wordt verderop in de zelfstudie uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-mobileiron-single-sign-on"></a>Eenmalige aanmelding configureren voor MobileIron

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van MobileIron.

2. Ga naar > **Beheerdersidentiteit** en selecteer de optie **AAD** in het veld **Info op Cloud IDP Setup.** **Admin**

    ![Eenmalige aanmelding configureren](./media/mobileiron-tutorial/tutorial_mobileiron_admin.png)

3. Kopieer de waarden van **Key** en **Host** en plak deze in de sectie **Standaard SAML-configuratie** in de Azure-portal om de URL's te voltooien.

    ![Eenmalige aanmelding configureren](./media/mobileiron-tutorial/key.png)

4. Klik in het veld **Export Metadata file from AAD and Import to MobileIron Cloud** op **Choose File** om de metagegevens die u hebt gedownload uit de Azure-portal te uploaden. Klik op **Done** als het uploaden is voltooid.

    ![Eenmalige aanmelding configureren](./media/mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het **veld Type Gebruikersnaam** **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot MobileIron.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **MobileIron**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **MobileIron** in de lijst met toepassingen.

    ![De koppeling naar MobileIron in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-mobileiron-test-user"></a>Een testgebruiker maken voor MobileIron

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij MobileIron, moeten ze worden ingericht voor MobileIron.  
In het geval van MobileIron is dat een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u als beheerder aan bij de bedrijfssite van MobileIron.

1. Ga naar **Users** en klik op **Add** > **Single User**.

    ![Eenmalige aanmelding configureren](./media/mobileiron-tutorial/tutorial_mobileiron_user.png)

1. Voer in het dialoogvenster **Add Single User** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. Voer in het tekstvak **E-mailadres** de brittasimon@contoso.come-mail van de gebruiker in, zoals .

    b. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals Britta.

    c. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals Simon.

    d. Klik op **Gereed**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel MobileIron klikt, wordt u automatisch aangemeld bij de instantie van MobileIron waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

