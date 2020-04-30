---
title: 'Zelf studie: integratie Azure Active Directory met ArcGIS Enter prise | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en ArcGIS Enterprise configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/28/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e21b1c72f191f3644975afd511a900667a04ce9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73157895"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Zelf studie: integratie Azure Active Directory met ArcGIS Enter prise

In deze zelfstudie leert u hoe u ArcGIS Enterprise integreert met Azure Active Directory (Azure AD).
De integratie van ArcGIS Enterprise met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot ArcGIS Enterprise.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij ArcGIS Enterprise (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met ArcGIS Enterprise te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op ArcGIS Enterprise waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.



* ArcGIS Enterprise biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding
* ArcGIS Enterprise biedt ondersteuning voor het **Just In Time** inrichten van gebruikers


## <a name="adding-arcgis-enterprise-from-the-gallery"></a>ArcGIS Enterprise toevoegen vanuit de galerie

Als u de integratie van ArcGIS Enterprise in Azure AD wilt configureren, moet u ArcGIS Enterprise vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om ArcGIS Enterprise toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **ArcGIS Enterprise** in het zoekvak, selecteer **ArcGIS Enterprise** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![ArcGIS Enterprise toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met [toepassingsnaam] configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerd gebruiker in [toepassingsnaam] tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met [toepassingsnaam] wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor ArcGIS Enterprise configureren](#configure-arcgis-enterprise-single-sign-on)**: de instellingen voor eenmalige aanmelding aan de toepassingszijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor ArcGIS Enterprise maken](#create-arcgis-enterprise-test-user)**: als u een equivalent van Britta Simon in ArcGIS Enterprise wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met [toepassingsnaam], moet u de volgende stappen uitvoeren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina van de integratie van **ArcGIS Enterprise** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **basis configuratie van SAML** de volgende stappen uit als u de toepassing wilt configureren in de gestarte modus **IDP** :

    ![Gegevens van domein en URL's voor eenmalige aanmelding van ArcGIS Enterprise](common/idp-intiated.png)

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`<EXTERNAL_DNS_NAME>.portal`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Gegevens van domein en URL's voor eenmalige aanmelding van ArcGIS Enterprise](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van ArcGIS Enterprise](mailto:support@esri.com) om deze waarden op te vragen. De waarde voor de id is beschikbaar in de sectie **Set Identity Provider**. Dit wordt verderop in deze zelfstudie uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-arcgis-enterprise-single-sign-on"></a>Eenmalige aanmelding configureren voor ArcGIS Enterprise

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van ArcGIS Enterprise.

2. Selecteer **Organization >EDIT SETTINGS**.

    ![Configuratie van ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure1.png)

3. Selecteer het tabblad **Security**.

    ![Configuratie van ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure2.png)

4. Blader omlaag naar de sectie **Enterprise Logins via SAML** en selecteer **SET ENTERPRISE LOGIN**.

    ![Configuratie van ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure3.png)

5. Voer in de sectie **Set Identity Provider** de volgende stappen uit:

    ![Configuratie van ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure4.png)

    a. Geef in het tekstvak **Name** een naam op, zoals **Azure Active Directory Test**.

    b. Plak in het tekstvak **URL** de waarde van **App-URL voor federatieve metagegevens** die u uit de Azure-portal hebt gekopieerd.

    c. Klik op **Show advanced settings**, kopieer de waarde van **Entity ID** en plak deze in het tekstvak **Id** in de sectie **ArcGIS Enterprise-domein en -URL's** in de Azure-portal.
    
    ![Configuratie van ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure5.png)

    d. Klik op **UPDATE IDENTITY PROVIDER**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ in het veld **gebruikers naam** **brittasimon\@yourcompanydomain. extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte stelt u Britta Simon in staat om gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot ArcGIS Enterprise.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **ArcGIS Enterprise**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **ArcGIS Enterprise** in de lijst met toepassingen.

    ![De koppeling naar ArcGIS Enterprise in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-arcgis-enterprise-test-user"></a>Een testgebruiker maken voor ArcGIS Enterprise

In dit gedeelte wordt een gebruiker met de naam Britta Simon gemaakt in ArcGIS Enterprise. ArcGIS Enterprise biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in ArcGIS Enterprise, wordt er een nieuwe gemaakt na verificatie.

> [!Note]
> Als u handmatig een gebruiker moet maken, neemt u contact op met het  [ondersteuningsteam van ArcGIS Enterprise](mailto:support@esri.com).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel ArcGIS Enterprise klikt, wordt u automatisch aangemeld bij de instantie van ArcGIS Enterprise waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

