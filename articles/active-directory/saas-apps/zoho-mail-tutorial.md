---
title: 'Zelfstudie: Integratie van Azure Active Directory met Zoho | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jeedes
ms.openlocfilehash: 00b9df3ad5e4d2104051e5489138785b4ecde8e0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546114"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Zelfstudie: Integratie van Azure Active Directory met Zoho

In deze zelfstudie leert u hoe u Zoho kunt integreren met Azure Active Directory (Azure AD).
De integratie van Zoho met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang krijgt tot Zoho.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Zoho (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u integratie van Azure AD wilt configureren voor Zoho, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Zoho waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zoho ondersteunt eenmalige aanmelding die wordt gestart vanuit **SP**

## <a name="adding-zoho-from-the-gallery"></a>Zoho toevoegen vanuit de galerie

Als u de integratie van Zoho in Azure AD wilt configureren, dient u Zoho vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit als u Zoho vanuit de galerie wilt toevoegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Zoho** in het zoekvak, selecteer **Zoho** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Zoho in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Zoho op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Zoho tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt testen en configureren met Zoho, dient u de volgende bouwstenen te voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Zoho-eenmalige aanmelding configureren](#configure-zoho-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Zoho-testgebruiker maken](#create-zoho-test-user)**: als u een tegenhanger van Britta Simon in Zoho wilt hebben die is gekoppeld aan de weergave in Azure AD van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding van Azure AD met Zoho wilt configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/), op de pagina voor de integratie van de toepassing **Zoho**, de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding van domeinen en URL's van Zoho](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<company name>.zohomail.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Zoho](https://www.zoho.com/mail/contact.html) (Engelstalig) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In dit gedeelte **Zoho instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-zoho-single-sign-on"></a>Eenmalige aanmelding voor Zoho configureren

1. Meld u in een andere webbrowser als administrator aan bij de bedrijfssite voor Zoho Mail.

2. Ga naar het **Configuratiescherm**.
   
    ![Configuratiescherm](./media/zoho-mail-tutorial/ic789607.png "Configuratiescherm")

3. Klik op het tabblad **SAML-verificatie**.
   
    ![SAML-verificatie](./media/zoho-mail-tutorial/ic789608.png "SAML Authentication")

4. Voer in het gedeelte **Details SAML-verificatie** de volgende stappen uit:
   
    ![Details SAML-verificatie](./media/zoho-mail-tutorial/ic789609.png "Details SAML-verificatie")
   
    a. Plak in het tekstvak **Aanmeldings-URL** de **aanmeldings-URL** die u in de Azure-portal hebt gekopieerd.
   
    b. Plak in het tekstvak **Afmeldings-URL** de **afmeldings-URL** die u in de Azure-portal hebt gekopieerd.
   
    c. Plak in het tekstvak **Wachtwoord-URL wijzigen****Wachtwoord-URL wijzigen** dat u in de Azure-portal hebt gekopieerd.
       
    d. Open uw met Base 64 gecodeerde certificaat dat u hebt gedownload in de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze in het tekstvak **PublicKey**.
   
    e. Selecteer **RSA** als **algoritme**.
   
    f. Klik op **OK**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte hebt u Britta Simon in staat gesteld gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot Zoho.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Zoho**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **Zoho** in de lijst met toepassingen.

    ![De koppeling Zoho in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-zoho-test-user"></a>Zoho-testgebruiker maken

Als u gebruikers van Azure AD in staat wilt stellen zich aan te melden bij Zoho Mail, moeten ze worden ingericht in Zoho Mail. Bij Zoho Mail is inrichten een handmatige taak.

> [!NOTE]
> U kunt andere hulpmiddelen voor het maken van Zoho Mail-gebruikersaccounts of door Zoho Mail geleverde API's gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

1. Meld u als administrator aan bij de bedrijfssite voor **Zoho Mail**.

1. Ga naar **Configuratiescherm \> Mail & Docs**.

1. Ga naar **Gebruikersdetails \> Gebruiker toevoegen**.
   
    ![Gebruiker toevoegen](./media/zoho-mail-tutorial/ic789611.png "Gebruiker toevoegen")

1. Voer in het dialoogvenster **Gebruiker toevoegen** de volgende stappen uit:
   
    ![Gebruiker toevoegen](./media/zoho-mail-tutorial/ic789612.png "Gebruiker toevoegen")
   
    a. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker in, bijvoorbeeld **Britta**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker in, bijvoorbeeld **Simon**.

    c. Typ in het tekstvak **E-mail-id** de e-mail-id van de gebruiker, bijvoorbeeld **brittasimon\@contoso.com**.

    d. Voer in het tekstvak **Wachtwoord** het wachtwoord van de gebruiker in.
   
    e. Klik op **OK**.  
      
    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Zoho in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Zoho waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

