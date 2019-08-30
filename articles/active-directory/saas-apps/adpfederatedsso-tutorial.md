---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met ADP | Microsoft Docs'
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
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162663"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met ADP

In deze zelf studie leert u hoe u ADP kunt integreren met Azure Active Directory (Azure AD). Wanneer u ADP integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot ADP.
* Stel in dat uw gebruikers automatisch worden aangemeld bij ADP met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Het abonnement voor eenmalige aanmelding voor de ADP (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* ADP biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-adp-from-the-gallery"></a>ADP toevoegen vanuit de galerie

Om de integratie van ADP te configureren in Azure AD moet u ADP vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **ADP** in het zoekvak.
1. Selecteer **ADP** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor ADP

Azure AD SSO met ADP configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ADP.

Als u Azure AD SSO wilt configureren en testen met ADP, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
2. **[ADP SSO configureren](#configure-adp-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. Een gebruiker van de **[ADP-test maken](#create-adp-test-user)** : als u een equivalent van B. Simon in ADP wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
3. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in de Azure-portal naar de overzichtspagina van de integratie **ADP**, klik op het tabblad **Eigenschappen** en voer de volgende stappen uit: 

    ![Eigenschappen voor eenmalige aanmelding](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Stel het veld **Ingeschakeld zodat gebruikers zich kunnen aanmelden** in op **Ja**.

    b. Kopieer de waarde van **URL van gebruikerstoegang** om deze verderop in de zelfstudie te plakken in de sectie **Aanmeldings-URL configureren**.

    c. Stel de waarde van het veld **Gebruikerstoewijzing vereist** in op **Ja**.

    d. Stel de waarde van het veld **Zichtbaar voor gebruikers** in op **Nee**.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **ADP** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    Typ een URL in het vak **Id (Entiteits-id)** : `https://fed.adp.com`

4. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/metadataxml.png)

6. Op de sectie **set up ADP** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** aan de bovenkant van het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan ADP.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **ADP**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-adp-sso"></a>ADP SSO configureren

Om eenmalige aanmelding te configureren in **ADP**, moet u het gedownloade **XML-bestand met metagegevens** uploaden naar de [ADP-website](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Dit proces kan enkele dagen duren.

### <a name="configure-your-adp-services-for-federated-access"></a>De ADP service(s) configureren voor federatieve toegang

>[!Important]
> Werknemers die federatieve toegang tot ADP-services nodig hebben, moeten worden toegewezen aan de ADP-service-app en daarna aan de specifieke ADP-service.
Nadat uw ADP-vertegenwoordiger de toewijzing heeft bevestigd, configureert u de ADP-service(s) en wijst u gebruikers toe of beheert u deze om de toegang van gebruikers tot de desbetreffende ADP-service te bepalen.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **ADP** in het zoekvak.
1. Selecteer **ADP** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.
1. Ga in de Azure-portal naar de overzichtspagina van de integratie **ADP**, klik op het tabblad **Eigenschappen** en voer de volgende stappen uit:  

    ![Gekoppelde eigenschappen van eenmalige aanmelding](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Stel het veld **Ingeschakeld zodat gebruikers zich kunnen aanmelden** in op **Ja**.

    b.  Stel de waarde van het veld **Gebruikerstoewijzing vereist** in op **Ja**.

    c.  Stel de waarde van het veld **Zichtbaar voor gebruikers** in op **Ja**.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **ADP** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.

1. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de **modus** **Gekoppeld** om uw toepassing aan **ADP** te koppelen.

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

Het doel van deze sectie is het maken van een gebruiker met de naam B. Simon in ADP. Neem contact op met het [ADP-ondersteuningsteam](https://www.adp.com/contact-us/overview.aspx) om gebruikers toe te voegen aan het ADP-account. 

## <a name="test-sso"></a>SSO testen 

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel ADP klikt, wordt u automatisch aangemeld bij de instantie van ADP waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [ADP met Azure AD proberen](https://aad.portal.azure.com)
