---
title: 'Zelfstudie: Azure Active Directory-integratie met Egnyte | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Egnyte configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f8eb0f0d566d656436da11141ea7f6c521b7b82
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983668"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Egnyte

In deze zelfstudie leert u hoe u Egnyte integreert met Azure Active Directory (Azure AD). Wanneer u Egnyte integreert met Azure AD, u het als:

* Controle in Azure AD die toegang heeft tot Egnyte.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Egnyte met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Egnyte single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Egnyte biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding
* Zodra u Egnyte hebt geconfigureerd, u sessiecontrole afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-egnyte-from-the-gallery"></a>Egnyte toevoegen vanuit de galerie

Om de integratie van Egnyte te configureren in Azure AD moet u Egnyte vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Egnyte** in het zoekvak in de sectie **Toevoegen van de galerie.**
1. Selecteer **Egnyte** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Egnyte op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Egnyte tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Egnyte, moet u de volgende procedures uitvoeren:

Als u Azure AD SSO met Egnyte wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Egnyte SSO](#configure-egnyte-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak Egnyte-testgebruiker](#create-egnyte-test-user)** - om een tegenhanger van B.Simon in Egnyte te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met Egnyte:

1. Selecteer in de [Azure-portal](https://portal.azure.com/), op de integratiepagina voor de toepassing **Egnyte**, de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens voor domein en URL's voor eenmalige aanmelding van Egnyte](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<companyname>.egnyte.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

5. Kopieer in het gedeelte **Egnyte instellen** de juiste URL('s) op basis van wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

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

In dit gedeelte geeft u Britta Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Egnyte.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Egnyte**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Egnyte** in de lijst met toepassingen.

    ![De koppeling naar Egnyte in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-egnyte-sso"></a>Egnyte SSO configureren

1. Meld u in een ander browservenster aan bij uw egnyte-bedrijfssite als beheerder.

2. Klik op **Instellingen**.
   
    ![Instellingen](./media/egnyte-tutorial/ic787819.png "Instellingen")

3. Klik in het menu op **Instellingen**.

    ![Instellingen](./media/egnyte-tutorial/ic787820.png "Instellingen")

4. Klik op het tabblad **Configuratie** en klik vervolgens op **Beveiliging**.

    ![Beveiliging](./media/egnyte-tutorial/ic787821.png "Beveiliging")

5. Voer in de sectie **Verificatie voor eenmalige aanmelding** de volgende stappen uit:

    ![Verificatie met één aanmelding](./media/egnyte-tutorial/ic787822.png "Verificatie met één aanmelding")   
    
    a. Selecteer **SAML 2.0** als **Verificatie voor eenmalige aanmelding**.
   
    b. Selecteer **AzureAD** als **Id-provider**.
   
    c. Plak de **Aanmeldings-URL** die u in de Azure-portal hebt gekopieerd, in het tekstvak **Aanmeldings-URL voor id-provider**.
   
    d. Plak de **Azure AD-id** die u hebt gekopieerd in de Azure-portal, in het tekstvak **Entiteits-id van id-provider**.
      
    e. Open in Kladblok het met Base64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **Id-providercertificaat**.
   
    f. Selecteer **E-mailadres** als **Standaardgebruikerstoewijzing**.
   
    g. Selecteer **Uitgeschakeld** bij **Waarde voor domeinspecifieke verlener gebruiken**.
   
    h. Klik op **Opslaan**.

### <a name="create-egnyte-test-user"></a>Egnyte-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Egnyte, moeten ze in Egnyte worden ingericht. In het geval van Egnyte is het inrichten een handmatige taak.

**Ga als volgt te werk om een gebruikersaccount in te richten:**

1. Meld u aan bij uw **egnyte-bedrijfssite** als beheerder.

2. Ga naar **Instellingen \> Gebruikers en groepen**.

3. Klik op **Nieuwe gebruiker toevoegen** en selecteer vervolgens het type gebruiker dat u wilt toevoegen.
   
    ![Gebruikers](./media/egnyte-tutorial/ic787824.png "Gebruikers")

4. Voer in de sectie **Nieuwe hoofdgebruiker** de volgende stappen uit:
    
    ![Nieuwe standaardgebruiker](./media/egnyte-tutorial/ic787825.png "Nieuwe standaardgebruiker")   

    a. Voer in het tekstvak **E-mail** de e-mail van gebruiker in, zoals **\@Brittasimon contoso.com**.

    b. Voer in het tekstvak **Gebruikersnaam** de gebruikersnaam van een gebruiker zoals **Brittasimon**.

    c. Selecteer als **Verificatietype** de optie **Eenmalige aanmelding**.
   
    d. Klik op **Opslaan**.
    
    >[!NOTE]
    >De gebruiker van het Azure Active Directory-account ontvangt een e-mailmelding.
    >

>[!NOTE]
>U alle andere egnyte-hulpprogramma's voor het maken van gebruikersaccounts of API's van Egnyte gebruiken om Azure AD-gebruikersaccounts in te richten.
>

### <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Egnyte klikt, wordt u automatisch aangemeld bij de instantie van Egnyte waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
