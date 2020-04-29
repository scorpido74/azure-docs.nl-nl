---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met de Zscaler B2B-gebruikers Portal | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zscaler B2B-gebruikers Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 75b90449-095d-4e47-a1fd-8b5cbbfe7e3d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c28af94c92fa56d5217aee91053521d2c12b0aef
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80668928"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-b2b-user-portal"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met de Zscaler B2B-gebruikers Portal

In deze zelf studie leert u hoe u Zscaler B2B-gebruikers Portal integreert met Azure Active Directory (Azure AD). Wanneer u Zscaler B2B-gebruikers Portal integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de Zscaler B2B-gebruikers Portal.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Zscaler B2B-gebruikers Portal met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Zscaler B2B-gebruikers Portal-abonnement voor eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Zscaler B2B-gebruikers Portal ondersteunt door **IDP** GEÏNITIEERDe SSO

* Zscaler B2B-gebruikers Portal ondersteunt **just-in-time** -gebruikers inrichting

* Zodra u de Zscaler B2B-gebruikers Portal hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-zscaler-b2b-user-portal-from-the-gallery"></a>Zscaler B2B-gebruikers portal toevoegen vanuit de galerie

Als u de integratie van de Zscaler B2B-gebruikers Portal wilt configureren in azure AD, moet u Zscaler B2B-gebruikers portal toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Zscaler B2B-gebruikers Portal** in het zoekvak.
1. Selecteer **ZSCALER B2B-gebruikers Portal** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-b2b-user-portal"></a>Eenmalige aanmelding van Azure AD voor Zscaler B2B-gebruikers Portal configureren en testen

Azure AD SSO met Zscaler B2B-gebruikers Portal configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de Zscaler B2B-gebruikers Portal.

Als u Azure AD SSO wilt configureren en testen met Zscaler B2B-gebruikers Portal, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CONFIGUREER SSO van de ZSCALER B2B-gebruikers Portal](#configure-zscaler-b2b-user-portal-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. **[Maak een ZSCALER B2B gebruikers Portal test gebruiker](#create-zscaler-b2b-user-portal-test-user)** -om een soort tegen te brengen van B. Simon in Zscaler B2B-gebruikers Portal dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/) **naar de pagina** **Zscaler B2B User Portal** Application Integration en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **eenmalige aanmelding met SAML instellen** de waarden in voor de volgende velden:

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://samlsp.private.zscaler.com/auth/metadata/<UniqueID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://samlsp.private.zscaler.com/auth/login?domain=EXAMPLE`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteunings team van de ZSCALER B2B-gebruikers Portal](https://help.zscaler.com/) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer de gewenste URL ('s) op basis van uw vereiste op de sectie **ZSCALER B2B-gebruikers Portal instellen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de Zscaler B2B-gebruikers Portal.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de **ZSCALER B2B-gebruikers Portal**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-zscaler-b2b-user-portal-sso"></a>SSO van Zscaler B2B-gebruikers Portal configureren

1. Open een nieuw webbrowser venster en meld u aan bij de bedrijfs site van uw Zscaler B2B-gebruikers portal als beheerder en voer de volgende stappen uit:

1. Klik aan de linkerkant van het menu op **beheer** en ga naar de sectie **verificatie** en klik op **IDP-configuratie**.

    ![Beheer van Zscaler private Access-beheerder](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-administration.png)

1. Klik in de rechter bovenhoek op **IDP-configuratie toevoegen**. 

    ![Zscaler private Access Administrator IDP](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-idp.png)

1. Voer de volgende stappen uit op de pagina **IDP-configuratie toevoegen** :
 
    ![Zscaler-beheerder voor persoonlijke toegang selecteren](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-select.png)

    a. Klik op **bestand selecteren** om het gedownloade meta gegevensbestand te uploaden vanuit Azure AD in het IDP-bestand voor het **uploaden van meta gegevens** .

    b. De **meta gegevens van IDP** worden gelezen uit Azure AD en alle veld gegevens worden ingevuld zoals hieronder wordt weer gegeven.

    ![Zscaler persoonlijke toegang beheerder config](./media/zscaler-b2b-user-tutorial/config.png)

    c. Selecteer uw domein in het veld **domeinen** .
    
    d. Klik op **Opslaan**.

### <a name="create-zscaler-b2b-user-portal-test-user"></a>Test gebruiker voor Zscaler B2B-gebruikers portal maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in Zscaler B2B-gebruikers Portal. De Zscaler B2B-gebruikers Portal ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker niet al aanwezig is in de Zscaler B2B-gebruikers Portal, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Zscaler B2B-gebruikers Portal in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Zscaler B2B-gebruikers Portal waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Zscaler B2B-gebruikers Portal uit met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)