---
title: 'Zelf studie: integratie Azure Active Directory met verstek verbinding | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en verstek verbinding.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 30a4dcbd15d8a47d99b3a61879a7e61938d3b133
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160531"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Zelf studie: integratie Azure Active Directory met verstek MiCloud Connect

In deze zelf studie leert u hoe u Afknot MiCloud kunt integreren met Azure Active Directory (Azure AD). Het integreren van MiCloud-verbinding met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot MiCloud Connect apps met behulp van hun bedrijfs referenties.
* U kunt instellen dat gebruikers in uw account automatisch worden aangemeld voor MiCloud Connect (eenmalige aanmelding) met hun Azure AD-accounts.


Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met MiCloud Connect wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement

  Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Een verstek MiCloud Connect-account

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u eenmalige aanmelding voor Azure AD (SSO).

* Afknot-verbinding ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-mitel-connect-from-the-gallery"></a>Verstek verbinding toevoegen vanuit de galerie

Als u de integratie van verstek verbinding in azure AD wilt configureren, moet u een verstek verbinding vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps in de Azure Portal.

**Voer de volgende stappen uit om een verstekige verbinding toe te voegen vanuit de galerie:**

1. Klik in de **[Azure-portal](https://portal.azure.com)** in het navigatiepaneel aan de linkerkant op **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Klik op **bedrijfs toepassingen** en klik vervolgens op **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Klik op **nieuwe toepassing**.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **verstek verbinding** in het zoek veld, klik op **verstek verbinding maken** in het deel venster resultaten en klik vervolgens op **toevoegen**.

     ![Verstek verbinding in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met MiCloud Connect op basis van een test gebruiker met de naam **Julia Simon**. Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in MiCloud verbinding tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met MiCloud Connect, moet u de volgende stappen uitvoeren:

1. **[Configureer MiCloud Connect for SSO met Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** om uw gebruikers in staat te stellen deze functie te gebruiken en de SSO-instellingen aan de kant van de toepassing te configureren.
2. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
3. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
4. **[Maak een Afknot MiCloud Connect test gebruiker](#create-a-mitel-micloud-connect-test-user)** -zodat deze een equivalent van Julia Simon heeft op uw MiCloud Connect-account dat is gekoppeld aan de Azure AD-weer gave van de gebruiker.
5. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>MiCloud Connect for SSO configureren met Azure AD

In deze sectie schakelt u eenmalige aanmelding voor Azure AD in voor MiCloud Connect in de Azure Portal en configureert u uw MiCloud Connect-account om eenmalige aanmelding met Azure AD mogelijk te maken.

Als u MiCloud Connect with SSO voor Azure AD wilt configureren, is het het gemakkelijkst om de Azure Portal en de verstek account Portal naast elkaar te openen. U moet bepaalde gegevens van de Azure Portal kopiëren naar de verstek-account Portal en enkele van de verstek-account Portal naar de Azure Portal.


1. Ga als volgt te werk om de configuratie pagina te openen in de [Azure Portal](https://portal.azure.com/):

    a. Klik op de pagina **Afknot-verbinding met** toepassing op **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

    b. Klik in het dialoog venster **eenmalige aanmelding selecteren** op **SAML**.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)
    
    De op SAML gebaseerde aanmeldings pagina wordt weer gegeven.

2. Ga als volgt te werk om het dialoog venster configuratie te openen in de portal van de verwerkings account:

    a. Klik in het menu **telefoon systeem** op **invoeg**toepassingen.

    b. Klik rechts van **eenmalige aanmelding**op **activeren** of **instellingen**.
    
    Het dialoog venster instellingen voor eenmalige aanmelding verbinden wordt weer gegeven.
    
3. Schakel het selectie vakje **eenmalige aanmelding inschakelen** in.
    ![installatie kopie](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. Klik in de Azure Portal op het pictogram **bewerken** in de sectie **basis configuratie van SAML** .
    ![installatie kopie](common/edit-urls.png)

    Het dialoog venster basis configuratie van SAML wordt weer gegeven.

5.  Kopieer de URL uit het veld **Afknot-id (Entiteits-ID)** in de verstek-account Portal en plak deze in het veld **id (Entiteits-ID)** in de Azure Portal.

6. Kopieer de URL van het veld **antwoord-URL (assertion Consumer Service-URL)** in de verstek-account Portal en plak dit in het veld **antwoord-URL (assertion Consumer Service-URL)** in de Azure Portal.  
   ![installatie kopie](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. Typ in het tekstvak **URL voor aanmelden** een van de volgende url's:

    * **https://portal.shoretelsky.com** -de verwerkings account Portal gebruiken als de standaard verstek toepassing
    * **https://teamwork.shoretel.com** -om teamwork als uw standaard verstek toepassing te gebruiken

    **Opmerking**: de standaard verstek toepassing is de toepassing die wordt geopend wanneer een gebruiker op de Afknot Connect-tegel in het toegangs venster klikt. Dit is ook de toepassing die wordt geopend bij het uitvoeren van een test installatie vanuit Azure AD.

8. Klik op **Opslaan** in het dialoog venster **basis-SAML-configuratie** in de Azure Portal.

9. Klik in de sectie **SAML-handtekening certificaat** op de op **SAML gebaseerde aanmeldings** pagina in het Azure Portal op **downloaden** naast **certificaat (base64)** om het **handtekening certificaat** te downloaden en op uw computer op te slaan.
    ![installatie kopie](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Open het handtekening certificaat bestand in een tekst editor, kopieer alle gegevens in het bestand en plak de gegevens in het veld **handtekening certificaat** in de portal van de verlaagde account. 
    ![installatie kopie](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. Ga als volgt te werk in de sectie **Afknot verbinding instellen** op de op **SAML gebaseerde aanmeldings** pagina van de Azure portal:

    a. Kopieer de URL uit het veld **aanmeldings-URL** en plak deze in het veld **aanmeld-URL** in de verstek-account Portal.

    b. Kopieer de URL uit het veld **id van Azure AD** en plak deze in het veld **Entiteits-ID** in de verstek-account Portal.
    ![installatie kopie](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Klik op **Opslaan** in het dialoog venster **instellingen voor eenmalige aanmelding verbinden** in de verstek-account Portal.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

In deze sectie maakt u een test gebruiker met de naam Julia Simon in de Azure Portal.

1. Klik in het linkerdeel venster van de Azure Portal op **Azure Active Directory**, klik op **gebruikers**en klik vervolgens op **alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Klik boven aan het scherm op **nieuwe gebruiker** .

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. Voer in het dialoog venster gebruikers eigenschappen de volgende stappen uit:

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Typ **BrittaSimon**in het veld **naam** .
  
    b. Typ in het veld **gebruikers naam** brittasimon @\<yourcompanydomain\>.\<extensie\>.  
Bijvoorbeeld BrittaSimon@contoso.com.

    c. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .

    d. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de verstek verbinding.

1. Klik in de Azure Portal op **bedrijfs toepassingen**en klik vervolgens op **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Klik in de lijst toepassingen op **verstek verbinding maken**.

    ![De koppeling verstek in de lijst met toepassingen](common/all-applications.png)

3. Klik in het menu aan de linkerkant op **gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op **gebruiker toevoegen**en klik vervolgens op **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** de optie **Julia Simon** in de lijst **gebruikers** en klik vervolgens op **selecteren** onder aan het scherm.

6. Als u een wille keurige rol in de SAML-bevestiging verwacht, selecteert u de juiste rol voor de gebruiker in de lijst in het dialoog venster **rol selecteren** en klikt u onder aan het scherm op **selecteren** .

7. Klik in het dialoog venster **toewijzing toevoegen** op **toewijzen**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Een Afknot test gebruiker voor MiCloud Connect maken

In deze sectie maakt u een gebruiker met de naam Julia Simon op uw MiCloud Connect-account. Gebruikers moeten worden gemaakt en geactiveerd voordat ze eenmalige aanmelding kunnen gebruiken.

Voor meer informatie over het toevoegen van gebruikers in de verlaagde account Portal raadpleegt u het artikel [een gebruiker toevoegen](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) in de verstek kennis basis.

Maak een gebruiker op uw MiCloud Connect-account met de volgende details:

  * **Naam:** Julia Simon

* **Zakelijk e-mail adres:** `brittasimon@<yourcompanydomain>.<extension>`   
(Voor beeld: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Gebruikers naam:** `brittasimon@<yourcompanydomain>.<extension>`  
(Voor beeld: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); de gebruikers naam van de gebruiker is doorgaans hetzelfde als het zakelijke e-mail adres van de gebruiker.)

**Opmerking:** De gebruikers naam van de gebruiker MiCloud Connect moet identiek zijn aan het e-mail adres van de gebruiker in Azure.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u de configuratie van eenmalige aanmelding voor Azure AD testen met behulp van het toegangs venster.

Wanneer u op de tegel verbinding maken in het toegangs venster klikt, moet u automatisch worden omgeleid om u aan te melden bij de MiCloud Connect-toepassing die u als standaard hebt geconfigureerd in het veld **Sign on URL** . Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
