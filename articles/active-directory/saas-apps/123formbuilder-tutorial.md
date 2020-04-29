---
title: 'Zelf studie: integratie Azure Active Directory met 123FormBuilder SSO | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en 123FormBuilder SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 947a9d632089b18f6b950c5eecbcb74d061f32eb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81274212"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met 123FormBuilder SSO

In deze zelf studie leert u hoe u 123FormBuilder SSO integreert met Azure Active Directory (Azure AD). Wanneer u 123FormBuilder SSO integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot 123FormBuilder SSO.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij 123FormBuilder SSO met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* 123FormBuilder SSO-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* 123FormBuilder SSO ondersteunt SSO **met SP en IDP** .
* 123FormBuilder SSO ondersteunt **just-in-time** -gebruikers inrichting.
* Zodra u 123FormBuilder SSO hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessie beheer wordt uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-123formbuilder-sso-from-the-gallery"></a>123FormBuilder SSO toevoegen vanuit de galerie

Als u de integratie van 123FormBuilder SSO wilt configureren in azure AD, moet u 123FormBuilder SSO vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **123FormBuilder SSO** in het zoekvak.
1. Selecteer **123FORMBUILDER SSO** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-123formbuilder-sso"></a>Eenmalige aanmelding van Azure AD voor 123FormBuilder SSO configureren en testen

Azure AD SSO met 123FormBuilder SSO configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in 123FormBuilder SSO.

Als u Azure AD SSO wilt configureren en testen met 123FormBuilder SSO, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[123FORMBUILDER SSO configureren](#configure-123formbuilder-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een 123FORMBUILDER SSO-test gebruiker](#create-123formbuilder-sso-test-user)** -om een soort tegen te brengen van B. Simon in 123FormBuilder SSO dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **123FormBuilder SSO** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/metadata`


    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/acs`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL's en id, zoals later in de zelfstudie wordt uitgelegd.

1. Zoek op de pagina **eenmalige aanmelding instellen met SAML** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de **SSO-sectie 123FormBuilder instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan 123FormBuilder SSO.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **123FORMBUILDER SSO**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-123formbuilder-sso"></a>123FormBuilder SSO configureren

1. Als u eenmalige aanmelding wilt configureren op **123FORMBUILDER SSO** -zijde, [https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) gaat u naar en voert u de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/123formbuilder-tutorial/submit.png) 

    a. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld `B.Simon@Contoso.com`.

    b. Klik op **Upload** en blader naar het gedownloade XML-bestand met metagegevens dat u hebt gedownload vanuit de Azure-portal.

    c. Klik op **SUBMIT FORM**.

2. Voer in **Microsoft Azure AD - Eenmalige aanmelding - App-instellingen configureren** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/123formbuilder-tutorial/url3.png)

    a. Als u de toepassing wilt configureren in de **door IDP geïnitieerde modus**, kopieert u de waarde van **IDENTIFIER** voor uw exemplaar en plakt u deze in het tekstvak **Id** in de sectie **SAML-basisconfiguratie** van de Azure-portal.

    b. Als u de toepassing wilt configureren in de **door IDP geïnitieerde modus**, kopieert u de waarde van **REPLY URL** voor uw exemplaar en plakt u deze in het tekstvak **Antwoord-URL** in de sectie **SAML-basisconfiguratie** van de Azure-portal.

    c. Als u de toepassing wilt configureren in de **door SP geïnitieerde modus**, kopieert u de waarde van **SIGN ON URL** voor uw exemplaar en plakt u deze in het tekstvak **Aanmeldings-URL** in de sectie **SAML-basisconfiguratie** van de Azure-portal.

### <a name="create-123formbuilder-sso-test-user"></a>123FormBuilder SSO-test gebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in 123FormBuilder SSO. 123FormBuilder SSO ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in 123FormBuilder SSO, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel 123FormBuilder SSO in het toegangs venster klikt, moet u automatisch worden aangemeld bij de 123FormBuilder SSO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer 123FormBuilder SSO met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
