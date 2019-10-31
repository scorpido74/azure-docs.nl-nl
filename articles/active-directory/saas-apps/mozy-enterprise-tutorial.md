---
title: 'Zelf studie: integratie Azure Active Directory met Mozy Enter prise | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Mozy Enter prise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 0f1aca75045f674fa45f5ad00c14a37c1abe7aea
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160428"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Zelf studie: integratie Azure Active Directory met Mozy Enter prise

In deze zelf studie leert u hoe u Mozy Enter prise integreert met Azure Active Directory (Azure AD).
Het integreren van Mozy Enter prise met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Mozy Enter prise.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Mozy Enter prise (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Mozy Enter prise wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding voor Mozy Enter prise

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Mozy Enter prise ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Mozy Enter prise toevoegen vanuit de galerie

Als u de integratie van Mozy Enter prise wilt configureren in azure AD, moet u Mozy Enter prise toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Mozy Enter prise toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Mozy Enter prise**in het zoekvak, selecteer **Mozy Enter prise** uit het resultaten paneel en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![Mozy Enter prise in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Mozy Enter prise op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Mozy Enter prise tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Mozy Enter prise, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Mozy Enter prise configureren](#configure-mozy-enterprise-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Mozy Enter prise test User](#create-mozy-enterprise-test-user)** -om een tegen hanger te hebben van Julia Simon in Mozy Enter prise dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD met Mozy Enter prise te configureren:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Mozy Enter prise** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor Mozy Enter prise Domain en URLs](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van Mozy Enter prise client](https://support.mozy.com/) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in het gedeelte **Mozy Enter prise instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-mozy-enterprise-single-sign-on"></a>Eenmalige aanmelding voor Mozy Enter prise configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw Mozy Enter prise-bedrijfs site.

2. Klik in de sectie **configuratie** op **verificatie beleid**.
   
    ![Verificatie beleid](./media/mozy-enterprise-tutorial/ic777314.png "Verificatie beleid")

3. Voer de volgende stappen uit in de sectie **verificatie beleid** :
   
    ![Verificatie beleid](./media/mozy-enterprise-tutorial/ic777315.png "Verificatie beleid")
   
    a. Selecteer **Directory service** als **provider**.
   
    b. Selecteer **LDAP-push gebruiken**.
   
    c. Klik op het tabblad **SAML-verificatie**.
   
    d. Plak de **aanmeldings-URL**die u hebt gekopieerd van de Azure Portal naar het TEKSTVAK **Authentication URL** .
   
    e. Plak de **Azure ad-id**, die u hebt gekopieerd van de Azure Portal naar het tekstvak met het **SAML-eind punt** .
   
    f. Open het gedownloade base-64-versleutelde certificaat in Klad blok, kopieer de inhoud ervan naar het klem bord en plak het hele certificaat in het tekstvak **SAML-certificaat** .
   
    g. Selecteer **SSO inschakelen voor beheerders om u aan te melden met hun netwerk referenties**.
   
    h. Klik op **Wijzigingen opslaan**.

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

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Mozy Enter prise.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer **Mozy Enter prise**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Mozy Enter prise**.

    ![De Mozy Enter prise-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-mozy-enterprise-test-user"></a>Mozy Enter prise-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij Mozy Enter prise, moeten ze worden ingericht in Mozy Enter prise. In het geval van Mozy Enter prise is inrichting een hand matige taak.

>[!NOTE]
>U kunt alle andere hulpprogram ma's voor het maken van Mozy voor ondernemings accounts of Api's van Mozy Enter prise gebruiken om AAD-gebruikers accounts in te richten.

**Voer de volgende stappen uit om een gebruikersaccount in te richten:**

1. Meld u aan bij uw **Mozy Enter prise** -Tenant.

2. Klik op **gebruikers**en klik vervolgens op **nieuwe gebruiker toevoegen**.
   
    ![Gebruikers](./media/mozy-enterprise-tutorial/ic777317.png "Gebruikers")
   
    >[!NOTE]
    >De optie **nieuwe gebruiker toevoegen** wordt alleen weer gegeven als **Mozy** als provider is geselecteerd onder **verificatie beleid**. Als SAML-verificatie is geconfigureerd, worden de gebruikers automatisch toegevoegd bij hun eerste aanmelding via eenmalige aanmeldingen.
    
3. Voer de volgende stappen uit in het dialoog venster nieuwe gebruiker:
   
    ![Gebruikers toevoegen](./media/mozy-enterprise-tutorial/ic777318.png "Gebruikers toevoegen")
   
    a. Selecteer een groep in de lijst **een groep kiezen** .
   
    b. Selecteer een type in de lijst **type gebruiker** .
   
    c. Typ in het tekstvak **username** de naam van de Azure AD-gebruiker.
   
    d. Typ in het tekstvak **e-mail** het e-mail adres van de Azure AD-gebruiker.
   
    e. Selecteer **e-mail met gebruikers instructies verzenden**.
   
    f. Klik op **gebruiker (s) toevoegen**.

     >[!NOTE]
     > Nadat de gebruiker is gemaakt, wordt er een e-mail bericht verzonden naar de Azure AD-gebruiker die een koppeling bevat om het account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Mozy Enter prise in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Mozy-onderneming waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

