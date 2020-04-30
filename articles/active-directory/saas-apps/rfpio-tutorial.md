---
title: 'Zelf studie: integratie Azure Active Directory met RFPIO | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d7b6af8ff76c890b98c29ded0e8bdc637b45dd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67092848"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Zelf studie: integratie Azure Active Directory met RFPIO

In deze zelf studie leert u hoe u RFPIO integreert met Azure Active Directory (Azure AD).
Het integreren van RFPIO met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot RFPIO.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij RFPIO (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met RFPIO wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor eenmalige aanmelding RFPIO ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* RFPIO ondersteunt SSO die door **SP en IDP** is geïnitieerd

## <a name="adding-rfpio-from-the-gallery"></a>RFPIO toevoegen uit de galerie

Als u de integratie van RFPIO in azure AD wilt configureren, moet u RFPIO uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om RFPIO toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **RFPIO**in het zoekvak, selecteer **RFPIO** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![RFPIO in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met RFPIO op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in RFPIO tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met RFPIO, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[RFPIO eenmalige aanmelding configureren](#configure-rfpio-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een RFPIO-test gebruiker](#create-rfpio-test-user)** -om een equivalent van Julia Simon in RFPIO te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met RFPIO:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **RFPIO** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u de volgende stap uit in de sectie **basis configuratie van SAML** :

    ![Informatie over eenmalige aanmelding voor RFPIO domein en Url's](common/idp-identifier.png)

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://www.rfpio.com`

    b. Klik op **Extra URL's instellen**.

    c. Voer in het tekstvak **Relay-status** een teken reeks waarde in. Neem contact op met het [ondersteunings team van RFPIO](https://www.rfpio.com/contact/) om deze waarde te verkrijgen.

    ![Informatie over eenmalige aanmelding voor RFPIO domein en Url's](common/idp-preintegrated-relay.png)

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![installatiekopie](common/both-preintegrated-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://www.app.rfpio.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daad werkelijke id en aanmeldings-URL. Neem contact op met het [ondersteunings team van RFPIO-clients](https://www.rfpio.com/contact/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer op de sectie **RFPIO instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-rfpio-single-sign-on"></a>Eenmalige aanmelding voor RFPIO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij de **RFPIO** -website.

1. Klik op de linkerbenedenhoek van de linkerbenedenhoek.

    ![Eenmalige aanmelding configureren](./media/rfpio-tutorial/app1.png)

1. Klik op de **instellingen**van de organisatie. 

    ![Eenmalige aanmelding configureren](./media/rfpio-tutorial/app2.png)

1. Klik op de **functies &-integratie**.

    ![Eenmalige aanmelding configureren](./media/rfpio-tutorial/app4.png)

1. Klik in de **configuratie van SAML SSO** op **bewerken**.

    ![Eenmalige aanmelding configureren](./media/rfpio-tutorial/app3.png)

1. In deze sectie voert u de volgende acties uit:

    ![Eenmalige aanmelding configureren](./media/rfpio-tutorial/app5.png)
    
    a. Kopieer de inhoud van de **gedownloade meta gegevens-XML** en plak deze in het veld **identiteits configuratie** .

    > [!NOTE]
    >Als u de inhoud van de gedownloade **federatieve meta gegevens-XML** wilt kopiëren, gebruikt u **Klad blok** en de juiste **XML-editor**.

    b. Klik op **Valideren**.

    c. Nadat u op **valideren**hebt geklikt, **schakelt u SAML (ingeschakeld)** in op aan.

    d. Klik op **verzenden**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ `brittasimon@yourcompanydomain.extension`in het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan RFPIO.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **RFPIO**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **RFPIO**.

    ![De koppeling RFPIO in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-rfpio-test-user"></a>RFPIO-test gebruiker maken

1. Meld u als beheerder aan bij de RFPIO-bedrijfs site.

1. Klik op de linkerbenedenhoek van de linkerbenedenhoek.

    ![Eenmalige aanmelding configureren](./media/rfpio-tutorial/app1.png)

1. Klik op de **instellingen**van de organisatie. 

    ![Eenmalige aanmelding configureren](./media/rfpio-tutorial/app2.png)

1. Klik op **team leden**.

    ![Eenmalige aanmelding configureren](./media/rfpio-tutorial/app6.png)

1. Klik op **leden toevoegen**.

    ![Eenmalige aanmelding configureren](./media/rfpio-tutorial/app7.png)

1. In de sectie **nieuwe leden toevoegen** . Voer de volgende acties uit:

    ![Eenmalige aanmelding configureren](./media/rfpio-tutorial/app8.png)

    a. Voer een **e-mail adres** in het veld **Eén E-mail per regel invoeren** in.

    b. Selecteer op basis van uw vereisten een **rol** .

    c. Klik op **leden toevoegen**.

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel RFPIO in het toegangs venster klikt, moet u automatisch worden aangemeld bij de RFPIO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

