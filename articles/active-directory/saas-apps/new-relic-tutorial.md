---
title: 'Zelf studie: integratie Azure Active Directory met New Relic by account | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en New Relic by account.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: jeedes
ms.openlocfilehash: 9fdcec5b55f52b7b6b824bf2ba25c14541b2a3c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186563"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met New Relic by account

In deze zelf studie leert u hoe u nieuwe Relic kunt integreren op basis van een account met Azure Active Directory (Azure AD). Wanneer u nieuwe Relic op basis van een account met Azure AD integreert, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot nieuwe Relic per account.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij nieuwe Relic door account met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Nieuw Relic door het account voor eenmalige aanmelding (SSO) waarvoor het abonnement is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Nieuw Relic by-account ondersteunt door **SP** GEÏNITIEERDe SSO

* Zodra u de nieuwe Relic per account hebt geconfigureerd, kunt u sessie besturings elementen afdwingen, waardoor de gevoelige gegevens van uw organisatie in realtime worden beveiligd tegen exfiltration en onfiltratie. Sessie besturings elementen worden uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-new-relic-by-account-from-the-gallery"></a>Nieuwe Relic door account toevoegen uit de galerie

Als u de integratie van nieuwe Relic door een account wilt configureren in azure AD, moet u in de galerie nieuwe Relic toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **nieuwe Relic per account** in het zoekvak.
1. Selecteer **Nieuw Relic per account** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-new-relic-by-account"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor nieuwe Relic per account

Configureer en test Azure AD SSO met New Relic by account met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in New Relic by account.

Als u Azure AD SSO wilt configureren en testen met New Relic by account, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Nieuwe Relic configureren door SSO](#configure-new-relic-by-account-sso)** van het account: Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een nieuw Relic per account test gebruiker](#create-new-relic-by-account-test-user)** : als u een tegen hanger van B. Simon in nieuwe Relic wilt maken op basis van een account dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **nieuwe Relic op account** toepassing de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)
1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: 

    `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login`-Zorg ervoor dat u `acc_id` vervangt door uw eigen account-id van New Relic by account.

    b. Typ een URL in het vak **Id (Entiteits-id)**: `rpm.newrelic.com`

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer op de sectie **Nieuw Relic per account instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

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

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot nieuwe Relic op account.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Nieuw Relic per account**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-new-relic-by-account-sso"></a>Nieuwe Relic configureren door SSO van het account

1. Meld u in een ander browser venster aan bij uw **nieuwe Relic door** de bedrijfs site als beheerder.

2. Klik in het menu aan de bovenkant op **account instellingen**.
   
    ![Account instellingen](./media/new-relic-tutorial/ic797036.png "Account instellingen")

3. Klik op het tabblad **beveiliging en verificatie** en klik vervolgens op het tabblad **eenmalige aanmelding** .
   
    ![Eenmalige aanmelding](./media/new-relic-tutorial/ic797037.png "Eenmalige aanmelding")

4. Voer de volgende stappen uit op de pagina SAML-dialoog vensters:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Klik op **bestand kiezen** om uw gedownloade Azure Active Directory certificaat te uploaden.

    b. Plak in het tekstvak **URL voor externe aanmelding** de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd.
   
    c. Plak in het tekstvak **Afmeldings-URL afmelden** de waarde van de **afmeldings-URL**, die u van Azure Portal hebt gekopieerd.

    d. Klik op **Mijn wijzigingen opslaan**.

### <a name="create-new-relic-by-account-test-user"></a>Nieuwe Relic maken op basis van account test gebruiker

1. Meld u aan bij uw **nieuwe Relic door** de bedrijfs site als beheerder.

2. Klik in het menu aan de bovenkant op **account instellingen**.
   
    ![Account instellingen](./media/new-relic-tutorial/ic797040.png "Account instellingen")

3. Klik in het deel venster **account** aan de linkerkant op **samen vatting**en klik vervolgens op **gebruiker toevoegen**.
   
    ![Account instellingen](./media/new-relic-tutorial/ic797041.png "Account instellingen")

4. Voer de volgende stappen uit in het dialoog venster **actieve gebruikers** :
   
    ![Actieve gebruikers](./media/new-relic-tutorial/ic797042.png "Actieve gebruikers")
   
    a. Typ in het tekstvak **e-mail** het e-mail adres van een geldige Azure Active Directory gebruiker die u wilt inrichten.

    b. Als **rol** **gebruiker**selecteren.

    c. Klik op **deze gebruiker toevoegen**.

> [!NOTE]
> U kunt elk ander nieuw Relic gebruiken door accounts voor het maken van gebruikers accounts of Api's die worden geleverd door nieuwe Relic door account om Azure AD-gebruikers accounts in te richten.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel nieuw Relic per account in het toegangs venster klikt, moet u automatisch worden aangemeld bij het nieuwe Relic door account waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Nieuwe Relic op basis van het account proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)