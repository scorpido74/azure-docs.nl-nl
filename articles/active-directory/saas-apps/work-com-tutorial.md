---
title: 'Zelf studie: integratie Azure Active Directory met Work.com | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7a6dc16eef1bb36a5bd6cbf0502a83481230bc0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67087084"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Zelf studie: integratie Azure Active Directory met Work.com

In deze zelf studie leert u hoe u Work.com integreert met Azure Active Directory (Azure AD).
Het integreren van Work.com met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Work.com.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Work.com (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Work.com wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor eenmalige aanmelding Work.com ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Work.com ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-workcom-from-the-gallery"></a>Work.com toevoegen uit de galerie

Als u de integratie van Work.com in azure AD wilt configureren, moet u Work.com uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Work.com toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **work.com**in het zoekvak, selecteer **work.com** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Work.com in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Work.com op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Work.com tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Work.com, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Work.com eenmalige aanmelding configureren](#configure-workcom-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een work.com-test gebruiker](#create-workcom-test-user)** -om een equivalent van Julia Simon in work.com te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

>[!NOTE]
>Als u eenmalige aanmelding wilt configureren, moet u nog een aangepaste Work.com-domein naam instellen. U moet ten minste een domein naam definiëren, uw domein naam testen en deze implementeren voor uw hele organisatie.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Work.com:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **work.com** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor Work.com domein en Url's](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van work.com](https://help.salesforce.com/articleView?id=000159855&type=3) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer op de sectie **work.com instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-workcom-single-sign-on"></a>Eenmalige aanmelding voor Work.com configureren

1. Meld u als beheerder aan bij uw Work.com-Tenant.

2. Ga naar **Setup**.
   
    ![Instellen](./media/work-com-tutorial/ic794108.png "Instellen")

3. Klik in het navigatie deel venster links in de sectie **beheren** op **domein beheer** om de gerelateerde sectie uit te vouwen en klik vervolgens op **mijn domein** om de pagina **mijn domein** te openen. 
   
    ![Mijn domein](./media/work-com-tutorial/ic767825.png "Mijn domein")

4. Als u wilt controleren of uw domein correct is ingesteld, controleert u of de**stap 4 is geïmplementeerd op gebruikers**en controleert u de**instellingen van mijn domein**.
   
    ![Domein geïmplementeerd voor gebruiker](./media/work-com-tutorial/ic784377.png "Domein geïmplementeerd voor gebruiker")

5. Meld u aan bij uw Work.com-Tenant.

6. Ga naar **Setup**.
    
    ![Instellen](./media/work-com-tutorial/ic794108.png "Instellen")

7. Vouw het menu **beveiligings controles** uit en klik vervolgens op **instellingen voor eenmalige aanmelding**.
    
    ![Instellingen voor eenmalige aanmelding](./media/work-com-tutorial/ic794113.png "Instellingen voor eenmalige aanmelding")

8. Voer de volgende stappen uit op de pagina **instellingen voor eenmalige aanmelding** :
    
    ![SAML ingeschakeld](./media/work-com-tutorial/ic781026.png "SAML ingeschakeld")
    
    a. Selecteer **SAML Enabled**.
    
    b. Klik op **Nieuw**.

9. Voer de volgende stappen uit in de sectie **instellingen voor eenmalige SAML-aanmelding** :
    
    ![Instelling voor eenmalige aanmelding voor SAML](./media/work-com-tutorial/ic794114.png "Instelling voor eenmalige aanmelding voor SAML")
    
    a. Typ in het tekstvak **Name** een naam voor de configuratie.  
       
    > [!NOTE]
    > Het opgeven van een waarde voor **naam** vult automatisch het tekstvak van de **API-naam** in.
    
    b. Plak in het tekstvak van de **Uitgever** de waarde van de **Azure ad-id** die u hebt gekopieerd uit Azure Portal.
    
    c. Als u het gedownloade certificaat van Azure Portal wilt uploaden, klikt u op **Bladeren**.
    
    d. Typ `https://salesforce-work.com`in het tekstvak **Entiteits-ID** .
    
    e. Als **SAML-identiteits type**, selecteert u **beweringen bevat de Federatie-id van het gebruikers object**.
    
    f. Als **SAML-identiteits locatie**selecteert u **identiteit in het NameIdentfier-element van de instructie subject**.
    
    g. Plak de waarde van de **aanmeldings-URL** die u van Azure Portal hebt gekopieerd in het tekstvak **ID-provider aanmeldings-URL** .

    h. Plak in het tekstvak voor de **Afmeldings-URL van de identiteits provider** de waarde van de **afmeldings-URL** die u van Azure Portal hebt gekopieerd.
    
    i. Selecteer **http post**als **service provider geïnitieerde aanvraag binding**heeft.
    
    j. Klik op **Opslaan**.

10. Klik in de klassieke Work.com-Portal in het navigatie deel venster links op **domein beheer** om de gerelateerde sectie uit te vouwen en klik vervolgens op **mijn domein** om de pagina **mijn domein** te openen. 
    
    ![Mijn domein](./media/work-com-tutorial/ic794115.png "Mijn domein")

11. Klik op de pagina **mijn domein** in de sectie **huis stijl aanmeldings pagina** op **bewerken**.
    
    ![Huis stijl aanmeldings pagina](./media/work-com-tutorial/ic767826.png "Huis stijl aanmeldings pagina")

12. Op de pagina **huis stijl aanmeldings pagina** , in de sectie **verificatie service** , wordt de naam van uw **SAML SSO-instellingen** weer gegeven. Selecteer deze en klik vervolgens op **Opslaan**.
    
    ![Huis stijl aanmeldings pagina](./media/work-com-tutorial/ic784366.png "Huis stijl aanmeldings pagina")

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

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Work.com.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **work.com**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **work.com**.

    ![De koppeling Work.com in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-workcom-test-user"></a>Work.com-test gebruiker maken

Als Azure Active Directory gebruikers zich kunnen aanmelden, moeten ze worden ingericht op Work.com. In het geval van Work.com is inrichting een hand matige taak.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Voer de volgende stappen uit om de inrichting van gebruikers te configureren:

1. Meld u aan bij de Work.com-bedrijfs site als beheerder.

2. Ga naar **Setup**.
   
    ![Instellen](./media/work-com-tutorial/IC794108.png "Instellen")

3. Ga naar gebruikers van gebruikers **beheren \> **.
   
    ![Gebruikers beheren](./media/work-com-tutorial/IC784369.png "Gebruikers beheren")

4. Klik op **New User**.
   
    ![Alle gebruikers](./media/work-com-tutorial/IC794117.png "Alle gebruikers")

5. Voer in de sectie gebruiker bewerken de volgende stappen uit in kenmerken van een geldige Azure AD-account die u wilt inrichten in de Verwante tekst vakken:
   
    ![Gebruiker bewerken](./media/work-com-tutorial/ic794118.png "Gebruiker bewerken")
   
    a. Typ in het tekstvak **voor de voor naam** de **voor naam** van de gebruiker **Julia**.
    
    b. Typ in het tekstvak **Achternaam** de **Achternaam** van de gebruiker **Simon**.
    
    c. In het tekstvak **alias** typt u de **naam** van de gebruiker **BrittaS**.
    
    d. Typ in het tekstvak **e-mail** het **e-mail adres** van Brittasimon@contoso.comde gebruiker.
    
    e. Typ in het tekstvak **gebruikers naam** de gebruikers naam van de gebruiker zoals Brittasimon@contoso.com.
    
    f. Typ in het tekstvak **bijnaam** de waarde **Nick** van gebruiker **Simon**.
    
    g. Selecteer de **rol**, de **gebruikers licentie**en het **profiel**.
    
    h. Klik op **Opslaan**.  
      
    > [!NOTE]
    > De houder van het Azure AD-account krijgt een e-mail bericht met een koppeling om het account te bevestigen voordat het actief wordt.
    > 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Work.com in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Work.com waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

