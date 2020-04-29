---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Freshworks | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Freshworks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 697c3509-0e3a-4b05-9d5b-9ded4269eb60
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b73b8769b5f7360ba4ad778600b1000b8b3f7bc7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72376411"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Freshworks

In deze zelf studie leert u hoe u Freshworks integreert met Azure Active Directory (Azure AD). Wanneer u Freshworks integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Freshworks.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Freshworks met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Freshworks-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Freshworks ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-freshworks-from-the-gallery"></a>Freshworks toevoegen uit de galerie

Als u de integratie van Freshworks in azure AD wilt configureren, moet u Freshworks uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Freshworks** in het zoekvak.
1. Selecteer **Freshworks** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshworks"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Freshworks

Azure AD SSO met Freshworks configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Freshworks.

Als u Azure AD SSO wilt configureren en testen met Freshworks, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[FRESHWORKS SSO configureren](#configure-freshworks-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Freshworks-test gebruiker](#create-freshworks-test-user)** -om een equivalent van B. Simon in Freshworks te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Freshworks** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.freshworks.com/login`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteunings team van Freshworks-clients](mailto:support@freshworks.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Als u de **handtekening** opties wilt wijzigen volgens uw vereiste, klikt u op de knop **bewerken** om het dialoog venster **SAML-handtekening certificaat** te openen.

     ![installatiekopie](common/edit-certificate.png)

     ![Freshworks-configuratie](./media/freshworks-tutorial/response.png)

    a. Selecteer **SAML-respons ondertekenen** als **handtekening optie**.

    b. Klik op **Opslaan**.

1. Op de sectie **Freshworks instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Freshworks.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Freshworks**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-freshworks-sso"></a>Freshworks SSO configureren

1. Open een nieuw webbrowser venster en meld u aan bij uw Freshworks-bedrijfs site als beheerder en voer de volgende stappen uit:

2. Klik aan de linkerkant van het menu op **beveiligings** pictogram en controleer vervolgens de **optie eenmalige aanmelding** en selecteer **SAML SSO** onder **verificatie methoden**.

    ![Freshworks-configuratie](./media/freshworks-tutorial/configure01.png)

3. Voer de volgende stappen uit in de sectie **eenmalige aanmelding** :

    ![Freshworks-configuratie](./media/freshworks-tutorial/configure02.png)

    a. Klik op **kopiëren** om de **entiteits-id van de service provider (SP)** te kopiëren voor uw exemplaar en plak deze in het tekstvak **ID (Entiteits-ID)** in het gedeelte **basis-SAML-configuratie** op Azure Portal.

    b. Plak in de **entiteits-id die is geleverd door het tekstvak IDP** de id-waarde van **Azure AD** die u hebt gekopieerd van de Azure Portal.

    c. Plak in het tekstvak **SAML SSO URL** de waarde van de **aanmeldings-URL** , die u hebt gekopieerd uit de Azure Portal.

    d. Open het met base64 gecodeerde certificaat in Klad blok, kopieer de inhoud en plak het in het tekstvak voor het **beveiligings certificaat** .

    e. Klik op **Opslaan**.

### <a name="create-freshworks-test-user"></a>Freshworks-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam B. Simon in Freshworks. Werk samen met het [Freshworks-client ondersteunings team](mailto:support@freshworks.com) om de gebruikers toe te voegen in het Freshworks-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Freshworks in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Freshworks waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Freshworks met Azure AD](https://aad.portal.azure.com/)

