---
title: 'Zelf studie: integratie Azure Active Directory met Wdesk | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: a4cfcf20fc7a6a3532f65c3e797da6c876844d2c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985506"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Wdesk

In deze zelf studie leert u hoe u Wdesk integreert met Azure Active Directory (Azure AD). Wanneer u Wdesk integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Wdesk.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Wdesk met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Wdesk-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Wdesk ondersteunt SSO die door **SP** en **IDP** is geïnitieerd
* Nadat u Wdesk hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Wdesk toevoegen uit de galerie

Als u de integratie van Wdesk in azure AD wilt configureren, moet u Wdesk uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Wdesk** in het zoekvak.
1. Selecteer **Wdesk** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Wdesk op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Wdesk tot stand worden gebracht.

Als u Azure AD SSO wilt configureren en testen met Wdesk, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[WDESK SSO configureren](#configure-wdesk-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Wdesk-test gebruiker](#create-wdesk-test-user)** -om een equivalent van B. Simon in Wdesk te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Wdesk:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Wdesk** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Informatie over eenmalige aanmelding voor Wdesk domein en Url's](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Informatie over eenmalige aanmelding voor Wdesk domein en Url's](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en aanmeldings-URL. U krijgt deze waarden van WDesk Portal wanneer u de SSO configureert.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer op de sectie **Wdesk instellen** de gewenste URL ('s) volgens uw vereiste.

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

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. Typ brittasimon@yourcompanydomain.extensionin het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Wdesk.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Wdesk**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Wdesk**.

    ![De koppeling Wdesk in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-wdesk-sso"></a>Wdesk SSO configureren

1. Meld u in een ander browser venster aan bij Wdesk als een beveiligings beheerder.

2. Klik linksonder op **Administrator** en kies **account beheerder**:
 
     ![Eenmalige aanmelding configureren](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Navigeer in Wdesk-beheerder naar **beveiliging**en vervolgens op **SAML** - > **SAML-instellingen**:

    ![Eenmalige aanmelding configureren](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. Schakel onder **algemene instellingen**de **optie eenmalige SAML-aanmelding inschakelen in**:

    ![Eenmalige aanmelding configureren](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. Voer de volgende stappen uit onder **Details van service provider**:

    ![Eenmalige aanmelding configureren](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Kopieer de **aanmeldings-URL** en plak deze in het tekstvak **URL voor aanmelden** op Azure Portal.
   
      b. Kopieer de **meta gegevens-URL** en plak deze in het tekstvak **id** in azure Portal.
       
      c. Kopieer de **URL** van de consument en plak deze in het tekstvak **antwoord-URL** op Azure Portal.
   
      d. Klik op **Opslaan** op Azure Portal om de wijzigingen op te slaan.      

6. Klik op **IDP-instellingen configureren** om het dialoog venster **IDP-instellingen bewerken** te openen. Klik op **bestand kiezen** om het bestand **meta data. XML** te vinden dat u hebt opgeslagen vanaf Azure Portal en upload het.
    
    ![Eenmalige aanmelding configureren](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Klik op **Save changes** (Wijzigingen opslaan).

    ![Eenmalige aanmelding configureren](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Wdesk-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij Wdesk, moeten ze worden ingericht in Wdesk. In Wdesk is inrichten een hand matige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij Wdesk als een beveiligings beheerder.

2. Navigeer naar **admin** > **account Administrator**.

     ![Eenmalige aanmelding configureren](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Klik op **leden** onder **personen**.

4. Klik nu op **lid toevoegen** om het dialoog venster **lid toevoegen** te openen. 
   
    ![Een Azure AD-testgebruiker maken](./media/wdesk-tutorial/createuser1.png)  

5. Voer in het tekstvak **gebruiker** de gebruikers naam van de gebruiker in, zoals brittasimon@contoso.com en klik op de knop **door gaan** .

    ![Een Azure AD-testgebruiker maken](./media/wdesk-tutorial/createuser3.png)

6.  Voer de details in zoals hieronder wordt weer gegeven:
  
    ![Een Azure AD-testgebruiker maken](./media/wdesk-tutorial/createuser4.png)
 
    a. Voer in het tekstvak **e-mail** het e-mail adres van de gebruiker in, zoals brittasimon@contoso.com.

    b. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **Britta**.

    c. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

7. Klik op **de knop lid opslaan** .  

    ![Een Azure AD-testgebruiker maken](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Wdesk in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Wdesk waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)