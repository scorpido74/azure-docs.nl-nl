---
title: 'Zelfstudie: Azure Active Directory-integratie met Wdesk | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76985506"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Wdesk

In deze zelfstudie leert u hoe u Wdesk integreert met Azure Active Directory (Azure AD). Wanneer u Wdesk integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Wdesk.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Wdesk met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Wdesk single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Wdesk ondersteunt **SP** en **IDP** geïnitieerdse SSO
* Zodra u Wdesk hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Wdesk toevoegen vanuit de galerie

Als u de integratie van Wdesk in Azure AD wilt configureren, moet u Wdesk uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Wdesk** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Wdesk** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Wdesk op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Wdesk.

Als u Azure AD SSO met Wdesk wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Wdesk SSO configureren](#configure-wdesk-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Wdesk-testgebruiker maken](#create-wdesk-test-user)** - om een tegenhanger van B.Simon in Wdesk te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Wdesk te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Wdesk-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de **idp-modus** wilt configureren:

    ![Wdesk-domein- en URL's met eenmalige aanmelding](common/idp-intiated.png)

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Wdesk-domein- en URL's met eenmalige aanmelding](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en aanmeldings-URL. U krijgt deze waarden van WDesk portal wanneer u de SSO configureert.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Wdesk instellen** de juiste URL(s) volgens uw vereiste.

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
  
    b. In **User name** het veld brittasimon@yourcompanydomain.extensiontype gebruikersnaam . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Wdesk.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Wdesk**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Wdesk**in de lijst met toepassingen .

    ![De koppeling Wdesk in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-wdesk-sso"></a>Wdesk SSO configureren

1. Meld u in een ander browservenster aan bij Wdesk als beveiligingsbeheerder.

2. Klik linksonder op **Beheerder** en kies **Accountbeheerder:**
 
     ![Eenmalige aanmelding configureren](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Navigeer in Wdesk-beheerder naar **Beveiliging**en **saml** > **SAML-instellingen:**

    ![Eenmalige aanmelding configureren](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. Controleer **onder Algemene instellingen**de **inschakeling van SAML Single Sign On:**

    ![Eenmalige aanmelding configureren](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. Voer **onder Gegevens van de serviceprovider**de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Kopieer de **aanmeldings-URL** en plak deze in het tekstvak Url van **Aanmelding** op azure-portal.
   
      b. Kopieer de **url met ametaal** en plak deze in het tekstvak **Identifier** op azure-portal.
       
      c. Kopieer de **url van de consument** en plak deze in het tekstvak Url van **antwoord** op de Azure-portal.
   
      d. Klik **op Opslaan** op Azure-portal om de wijzigingen op te slaan.      

6. Klik **op IdP-instellingen configureren** om het dialoogvenster **IdP-instellingen bewerken** te openen. Klik **op Bestand kiezen** om het bestand **Metagegevens.xml** te zoeken dat u hebt opgeslagen vanuit de Azure-portal en upload het vervolgens.
    
    ![Eenmalige aanmelding configureren](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Klik **op Wijzigingen opslaan**.

    ![Eenmalige aanmelding configureren](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Wdesktestgebruiker maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij Wdesk, moeten ze zijn ingericht in Wdesk. In Wdesk is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij Wdesk als beveiligingsbeheerder.

2. Navigeer naar **beheerder** > **accountbeheerder**.

     ![Eenmalige aanmelding configureren](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Klik op **Leden** onder **Personen**.

4. Klik nu op **Lid toevoegen** om het dialoogvenster **Lid toevoegen** te openen. 
   
    ![Een Azure AD-testgebruiker maken](./media/wdesk-tutorial/createuser1.png)  

5. Voer **in het** tekstvak Gebruiker brittasimon@contoso.com de gebruikersnaam van gebruiker like in en klik op **Doorgaan.**

    ![Een Azure AD-testgebruiker maken](./media/wdesk-tutorial/createuser3.png)

6.  Voer de onderstaande gegevens in:
  
    ![Een Azure AD-testgebruiker maken](./media/wdesk-tutorial/createuser4.png)
 
    a. Voer in **het e-mailtekstvak** de brittasimon@contoso.come-mail van de gebruiker in, zoals .

    b. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **Britta**.

    c. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals **Simon**.

7. Klik **op Lid opslaan.**  

    ![Een Azure AD-testgebruiker maken](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de wdesk-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de wdesk waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)