---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met ADP | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b031ded2022078c31bd8570c6a6317c398715480
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "70162663"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met ADP

In deze zelfstudie leert u hoe u ADP integreert met Azure Active Directory (Azure AD). Wanneer u ADP integreert met Azure AD, u het als:

* Beheer in Azure AD wie toegang heeft tot ADP.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij ADP met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* ADP single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* ADP biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-adp-from-the-gallery"></a>ADP toevoegen vanuit de galerie

Om de integratie van ADP te configureren in Azure AD moet u ADP vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **ADP** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **ADP** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Azure AD-aanmelding voor ADP configureren en testen

Azure AD SSO configureren en testen met ADP met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in ADP.

Als u Azure AD SSO met ADP wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
2. **[ADP SSO configureren](#configure-adp-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak ADP-testgebruiker](#create-adp-test-user)** - om een tegenhanger van B.Simon in ADP te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Ga in de Azure-portal naar de overzichtspagina van de integratie **ADP**, klik op het tabblad **Eigenschappen** en voer de volgende stappen uit: 

    ![Eigenschappen voor eenmalige aanmelding](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Stel het veld **Ingeschakeld zodat gebruikers zich kunnen aanmelden** in op **Ja**.

    b. Kopieer de waarde van **URL van gebruikerstoegang** om deze verderop in de zelfstudie te plakken in de sectie **Aanmeldings-URL configureren**.

    c. Stel de waarde van het veld **Gebruikerstoewijzing vereist** in op **Ja**.

    d. Stel de waarde van het veld **Zichtbaar voor gebruikers** in op **Nee**.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **ADP-toepassingsintegratie** de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    Typ een URL in het tekstvak **Id (Entity ID):**`https://fed.adp.com`

4. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **ADP instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot ADP.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **ADP**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-adp-sso"></a>ADP SSO configureren

Om eenmalige aanmelding te configureren in **ADP**, moet u het gedownloade **XML-bestand met metagegevens** uploaden naar de [ADP-website](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Dit proces kan enkele dagen duren.

### <a name="configure-your-adp-services-for-federated-access"></a>De ADP service(s) configureren voor federatieve toegang

>[!Important]
> Werknemers die federatieve toegang tot ADP-services nodig hebben, moeten worden toegewezen aan de ADP-service-app en daarna aan de specifieke ADP-service.
Nadat uw ADP-vertegenwoordiger de toewijzing heeft bevestigd, configureert u de ADP-service(s) en wijst u gebruikers toe of beheert u deze om de toegang van gebruikers tot de desbetreffende ADP-service te bepalen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **ADP** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **ADP** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.
1. Ga in de Azure-portal naar de overzichtspagina van de integratie **ADP**, klik op het tabblad **Eigenschappen** en voer de volgende stappen uit:  

    ![Gekoppelde eigenschappen met één aanmelding](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Stel het veld **Ingeschakeld zodat gebruikers zich kunnen aanmelden** in op **Ja**.

    b.  Stel de waarde van het veld **Gebruikerstoewijzing vereist** in op **Ja**.

    c.  Stel de waarde van het veld **Zichtbaar voor gebruikers** in op **Ja**.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **ADP-toepassingsintegratie** de sectie **Beheren** en selecteer **Enkele aanmelding**.

1. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de **modus****Gekoppeld** om uw toepassing aan **ADP** te koppelen.

    ![Eenmalige aanmelding in de modus Gekoppeld](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Ga naar de sectie **Aanmeldings-URL configureren** en voer de volgende stappen uit:

    ![Aanmeldings-URL voor eenmalige aanmelding configureren](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Plak de **User access URL**, die u eerder hebt gekopieerd van het **tabblad Properties** van de ADP-app.
                                                             
    b. Hieronder ziet u de vijf apps die ondersteuning bieden voor verschillende **relaystate-URL's**. U moet de juiste **relaystate-URL** voor een bepaalde toepassing handmatig toevoegen aan de **User access URL**.
    
    * **ADP Workforce Now**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. Kies **Opslaan** om de wijzigingen op te slaan.

10. Nadat uw bevestiging heeft ontvangen van de ADP-vertegenwoordiger, begint u te testen met een of twee gebruikers.

    a. Wijs enkele gebruikers toe aan de ADP-service-app om federatieve toegang te testen.

    b. De test is geslaagd wanneer gebruikers via de ADP service-app in de galerie toegang krijgen tot hun ADP-service.
 
11. Als u bericht krijgt dat de test is geslaagd, wijst u de federatieve ADP-service toe aan individuele gebruikers of gebruikersgroepen en implementeert u de service voor uw werknemers. Deze toewijzing wordt later in de zelfstudie uitgelegd.

### <a name="create-adp-test-user"></a>ADP-testgebruiker maken

Het doel van deze sectie is het creëren van een gebruiker genaamd B.Simon in ADP. Neem contact op met het [ADP-ondersteuningsteam](https://www.adp.com/contact-us/overview.aspx) om gebruikers toe te voegen aan het ADP-account. 

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel ADP klikt, wordt u automatisch aangemeld bij de instantie van ADP waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [ADP uitproberen met Azure AD](https://aad.portal.azure.com)
