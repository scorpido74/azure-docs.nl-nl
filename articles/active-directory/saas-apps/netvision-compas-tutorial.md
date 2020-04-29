---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met netvision-kompas | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en netvision-kompas.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a56f44c5-dc08-4c7c-ad20-b6e7127deb2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3015ea26d81505c4f058846dbcb3b7858f79267
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80520106"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met netvision-kompas

In deze zelf studie leert u hoe u het Vision-kompas kunt integreren met Azure Active Directory (Azure AD). Wanneer u het Vision-kompas met Azure AD integreert, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot het Visio-kompas.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij netvision-kompas met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Netvision kompas, eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Netvision-kompas ondersteunt SSO **met SP en IDP**
* Zodra u netvision-kompas hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessie beheer wordt uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>Het netvision-kompas vanuit de galerie toevoegen

Als u de integratie van netvision-kompas wilt configureren in azure AD, moet u het netvision-kompas vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ **netvision-kompas** in het zoekvak in het gedeelte **toevoegen van de galerie** .
1. Selecteer **netvision kompas** vanuit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Eenmalige aanmelding voor Azure AD voor netvisios-kompas configureren en testen

Azure AD SSO configureren en testen met netvision-kompas met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in het Visio-kompas.

Als u Azure AD SSO wilt configureren en testen met netvision-kompas, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CONFIGUREER SSO van het Vision-kompas](#configure-netvision-compas-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. **[Configureer gebruiker van het Vision-kompas-test-gebruikers](#configure-netvision-compas-test-user)** -naar een soort B. Simon in het netvision-kompas dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **netvision** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://<TENANT>.compas.cloud/Identity/Saml20`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team netvision kompas client](mailto:contact@net.vision) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het meta gegevensbestand te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)



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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan het Visio-kompas.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **netvision-kompas**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-netvision-compas-sso"></a>Netvision-kompas-ondersso configureren

In deze sectie schakelt u SAML SSO in op het | **Vision-kompas**.
1. Meld u aan bij **netvision-kompas** met een beheerders account en toegang tot het gebied beheer.

    ![Beheerders gebied](media/netvision-compas-tutorial/admin.png)

1. Zoek het **systeem** gebied en selecteer **id-providers**.

    ![Beheerder id](media/netvision-compas-tutorial/admin-idps.png)

1. Selecteer de actie **toevoegen** om Azure ad te registreren als een nieuwe IDP.

    ![IDP toevoegen](media/netvision-compas-tutorial/idps-add.png)

1. Selecteer **SAML** voor het **provider type**.
1. Voer betekenis volle waarden in voor de velden **weergave naam** en **Beschrijving** .
1. Wijs **netvision** -gebruikers op het IDP toe door te selecteren in de lijst **beschik bare gebruikers** en selecteer vervolgens de knop **selectie toevoegen** . Gebruikers kunnen ook worden toegewezen aan de IDP tijdens het volgen van de inrichtings procedure.
1. Voor de SAML-optie voor **meta gegevens** klikt u op de knop **bestand kiezen** en selecteert u het meta gegevensbestand dat eerder op uw computer is opgeslagen.
1. Klik op **Opslaan**.

    ![IDP bewerken](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>De gebruiker netvision-kompas test configureren

In deze sectie gaat u een bestaande gebruiker in **netvision-kompas** configureren voor het gebruik van Azure AD voor SSO.
1. Volg de procedure voor het inrichten van een net **Vision** -gebruiker, zoals gedefinieerd door uw bedrijf of een bestaand gebruikers account bewerken.
1. Zorg er bij het definiëren van het profiel van de gebruiker voor dat het **e-mail adres (persoonlijk)** van de gebruiker username@companydomain.extensionovereenkomt met de Azure AD-gebruikers naam:. Bijvoorbeeld `B.Simon@contoso.com`.

    ![Gebruiker bewerken](media/netvision-compas-tutorial/user-config.png)

Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>SSO testen 

In deze sectie test u de configuratie van eenmalige aanmelding voor Azure AD.

### <a name="using-the-access-panel-idp-initiated"></a>Het toegangs venster (IDP gestart) gebruiken.

Wanneer u op de tegel netvision-kompas in het toegangs venster klikt, moet u automatisch worden aangemeld bij het netvisios-kompas waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Rechtstreeks toegang tot netvision-kompas (SP initiated).

1. De **netvision-kompas** -URL openen. Bijvoorbeeld `https://tenant.compas.cloud`.
1. Voer de gebruikers naam van het **netvision-kompas** in en selecteer **volgende**.

    ![Aangemelde gebruiker](media/netvision-compas-tutorial/login-user.png)

1. **(optioneel)** Als aan de gebruiker meerdere id binnen **netvision-kompas**zijn toegewezen, wordt een lijst met beschik bare id weer gegeven. Selecteer de Azure AD-IDP die u eerder hebt geconfigureerd in het **Visio-kompas**.

    ![Aanmelden kiezen](media/netvision-compas-tutorial/login-choose.png)

1. U wordt omgeleid naar Azure AD om de verificatie uit te voeren. Wanneer u bent geverifieerd, moet u automatisch worden aangemeld bij **netvision-kompas** waarvoor u SSO hebt ingesteld.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer netvision kompas uit met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
