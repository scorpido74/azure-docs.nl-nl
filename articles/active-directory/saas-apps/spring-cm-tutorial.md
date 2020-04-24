---
title: 'Zelf studie: integratie Azure Active Directory met SpringCM | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: be4edfe3262ab74d903384eb00c0282acc7a3299
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867434"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Zelf studie: integratie Azure Active Directory met SpringCM

In deze zelf studie leert u hoe u SpringCM integreert met Azure Active Directory (Azure AD).
Het integreren van SpringCM met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot SpringCM.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij SpringCM (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met SpringCM wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor eenmalige aanmelding SpringCM ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SpringCM ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-springcm-from-the-gallery"></a>SpringCM toevoegen uit de galerie

Als u de integratie van SpringCM in azure AD wilt configureren, moet u SpringCM uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SpringCM toe te voegen uit de galerie:**

1. In de **[Azure-portal](https://portal.azure.com)** klikt u in het navigatievenster aan de linkerkant op het pictogram **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, klikt u op de knop **nieuwe toepassing** boven aan het dialoog venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SpringCM**in het zoekvak, selecteer **SpringCM** in het deel venster resultaat en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![SpringCM in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met SpringCM op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SpringCM tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met SpringCM, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[SpringCM eenmalige aanmelding configureren](#configure-springcm-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een SpringCM-test gebruiker](#create-springcm-test-user)** -om een equivalent van Julia Simon in SpringCM te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met SpringCM:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **SpringCM** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor SpringCM domein en Url's](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van SpringCM](https://knowledge.springcm.com/support) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Raw)** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

6. Kopieer op de sectie **SpringCM instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-springcm-single-sign-on"></a>Eenmalige aanmelding voor SpringCM configureren

1. Meld u in een ander browser venster aan bij de **SpringCM** -bedrijfs site als beheerder.

1. Klik in het menu aan de bovenkant op **Ga naar**, klik op **voor keuren**en klik vervolgens in de sectie **account voorkeuren** op **SAML SSO**.
   
    ![Eenmalige aanmelding op basis van SAML](./media/spring-cm-tutorial/ic797051.png "Eenmalige aanmelding op basis van SAML")

1. Voer de volgende stappen uit in de sectie configuratie van de identiteits provider:
   
    ![Configuratie van de ID-provider](./media/spring-cm-tutorial/ic797052.png "Configuratie van de ID-provider")
    
    a. Als u het gedownloade Azure Active Directory certificaat wilt uploaden, klikt u op certificaat van **verlener selecteren** of **certificaat van verlener wijzigen**.
    
    b. Plak in het tekstvak **Uitgever** de **Azure ad-id** -waarde die u van Azure Portal hebt gekopieerd.
    
    c. Plak in het tekstvak door de **service provider geïnitieerde eindpunt** de waarde voor de **aanmeldings-URL** die u hebt gekopieerd van de Azure Portal.
            
    d. Selecteer **SAML ingeschakeld** als **inschakelen**.

    e. Klik op **Opslaan**.

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

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SpringCM.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **SpringCM**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **SpringCM**.

    ![De koppeling SpringCM in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-springcm-test-user"></a>SpringCM-test gebruiker maken

Om Azure Active Directory gebruikers in staat te stellen zich aan te melden bij SpringCM, moeten ze worden ingericht in SpringCM. In het geval van SpringCM is inrichting een hand matige taak.

> [!NOTE]
> Zie [een SpringCM-gebruiker maken en bewerken](http://community.springcm.com/s/article/Create-and-Edit-a-SpringCM-User-1619481053)voor meer informatie. 

**Voer de volgende stappen uit om een gebruikers account in te richten op SpringCM:**

1. Meld u als beheerder aan bij de **SpringCM** -bedrijfs site.

1. Klik op **Ga**naar en klik vervolgens op **adres boek**.
   
    ![Gebruiker maken](./media/spring-cm-tutorial/ic797054.png "Gebruiker maken")

1. Klik op **Gebruiker maken**.

1. Selecteer een **gebruikersrol**.

1. Selecteer **activerings-e-mail verzenden**.

1. Typ de voor naam, achternaam en het e-mail adres van een geldig Azure Active Directory gebruikers account dat u wilt inrichten in de bijbehorende tekst vakken.

1. Voeg de gebruiker toe aan een **beveiligings groep**.

1. Klik op **Opslaan**.

   > [!NOTE]
   > U kunt alle andere hulpprogram ma's voor het maken van SpringCM-gebruikers accounts of Api's die worden geleverd door SpringCM, gebruiken om Azure AD-gebruikers accounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SpringCM in het toegangs venster klikt, moet u automatisch worden aangemeld bij de SpringCM waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Extra resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

