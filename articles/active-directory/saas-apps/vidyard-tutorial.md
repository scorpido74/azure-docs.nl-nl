---
title: 'Zelf studie: integratie Azure Active Directory met Vidyard | Microsoft Docs'
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67087426"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Zelf studie: integratie Azure Active Directory met Vidyard

In deze zelf studie leert u hoe u Vidyard integreert met Azure Active Directory (Azure AD).
Het integreren van Vidyard met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Vidyard.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Vidyard (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Vidyard wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor eenmalige aanmelding Vidyard ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Vidyard ondersteunt SSO die door **SP** en **IDP** is geïnitieerd

* Vidyard ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-vidyard-from-the-gallery"></a>Vidyard toevoegen uit de galerie

Als u de integratie van Vidyard in azure AD wilt configureren, moet u Vidyard uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Vidyard toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Vidyard**in het zoekvak, selecteer **Vidyard** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![Vidyard in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Vidyard op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Vidyard tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Vidyard, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Vidyard eenmalige aanmelding configureren](#configure-vidyard-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een Vidyard-test gebruiker](#create-vidyard-test-user)** -om een equivalent van Julia Simon in Vidyard te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Vidyard:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Vidyard** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    ![Informatie over eenmalige aanmelding voor Vidyard domein en Url's](common/idp-intiated.png)

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Informatie over eenmalige aanmelding voor Vidyard domein en Url's](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Deze waarden worden bijgewerkt met de daad werkelijke id, de antwoord-URL en de aanmeldings-URL, die verderop in de zelf studie wordt beschreven. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer op de sectie **Vidyard instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-vidyard-single-sign-on"></a>Eenmalige aanmelding voor Vidyard configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw Vidyard-software-bedrijfs site.

2. Selecteer in het Vidyard-dash board **groeps** > **beveiliging**

    ![Vidyard-configuratie](./media/vidyard-tutorial/configure1.png)

3. Klik op het tabblad **Nieuw profiel** .

    ![Vidyard-configuratie](./media/vidyard-tutorial/configure2.png)

4. Voer de volgende stappen uit in de sectie **SAML-configuratie** :

    ![Vidyard-configuratie](./media/vidyard-tutorial/configure3.png)

    a. Voer een algemene profiel naam in het tekstvak **profiel naam** in.

    b. Kopieer de waarde van de **aanmeldings pagina voor SSO-gebruikers** en plak deze in het tekstvak **URL voor aanmelden** in de sectie **basis configuratie van SAML** op Azure Portal.

    c. Kopieer de waarde van de **ACS-URL** en plak deze in het tekstvak **antwoord-URL** in de sectie **basis configuratie van SAML** op Azure Portal.

    d. Kopieer de naam van de **Uitgever/meta gegevens-URL** en plak deze in een tekstvak **id** in het gedeelte **basis-SAML-configuratie** op Azure Portal.

    e. Open het gedownloade certificaat bestand van Azure Portal in Klad blok en plak het in het tekstvak **X. 509-certificaat** .

    f. Plak in het tekstvak URL van het **SAML-eind punt** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    g. Klik op **Bevestigen**.

5. Selecteer op het tabblad eenmalige aanmelding de optie **toewijzen** naast een bestaand profiel

    ![Vidyard-configuratie](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Zodra u een SSO-profiel hebt gemaakt, wijst u het toe aan een of meer groepen waarvoor gebruikers toegang nodig hebben via Azure. Als de gebruiker niet bestaat in de groep waaraan deze zijn toegewezen, wordt door Vidyard automatisch een gebruikers account gemaakt en wordt in realtime een rol toegewezen.

6. Selecteer uw organisatie groep, die zichtbaar is in de **groepen die kunnen worden toegewezen**.

    ![Vidyard-configuratie](./media/vidyard-tutorial/configure5.png)

7. U kunt de toegewezen groepen zien onder de **groepen die momenteel zijn toegewezen**. Selecteer een rol voor de groep conform uw organisatie en klik op **bevestigen**.

    ![Vidyard-configuratie](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Raadpleeg [dit document](https://knowledge.vidyard.com/hc/articles/360009990033-SAML-based-Single-Sign-On-SSO-in-Vidyard)voor meer informatie.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ brittasimon@yourcompanydomain.extensionin het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Vidyard.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Vidyard**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Vidyard**.

    ![De koppeling Vidyard in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-vidyard-test-user"></a>Vidyard-test gebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in Vidyard. Vidyard biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Vidyard, wordt er een nieuwe gemaakt na verificatie.

>[!Note]
>Als u hand matig een gebruiker moet maken, neemt u contact op met het [ondersteunings team van Vidyard](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Vidyard in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Vidyard waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

