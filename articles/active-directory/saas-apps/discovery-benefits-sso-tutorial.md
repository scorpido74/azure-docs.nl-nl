---
title: 'Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met SSO van detectie voordelen | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen de SSO van Azure Active Directory en detectie voordelen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a788cd07-0eed-4067-b79d-19b840e8836d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c4a6811ef5d7ed4f29c7dae89561616895a42a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72266141"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met SSO voor detectie voordelen

In deze zelf studie leert u hoe u SSO van detectie voordelen integreert met Azure Active Directory (Azure AD). Wanneer u de SSO van detectie voordelen integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot SSO van detectie voordelen.
* Stel uw gebruikers in staat om automatisch te worden aangemeld voor detectie van voor delen met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Detectie voordelen SSO-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* SSO van detectie voordelen ondersteunt **IDP** GEÏNITIEERDe SSO

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>SSO van detectie voordelen toevoegen vanuit de galerie

Als u de integratie van detectie voordelen SSO wilt configureren in azure AD, moet u SSO van detectie voordelen vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **detectie voordelen SSO** in het zoekvak.
1. Selecteer **SSO bene fits** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen voor SSO van detectie voordelen

Configureer en test Azure AD SSO met detectie voordelen SSO met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de SSO-voor delen van detectie.

Als u Azure AD SSO wilt configureren en testen met SSO van detectie voordelen, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Detectie voordelen SSO SSO configureren](#configure-discovery-benefits-sso-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een SSO-test gebruiker voor de detectie van voor delen](#create-discovery-benefits-sso-test-user)** : als u een tegen hanger van B. Simon wilt hebben in de SSO voor detectie voordelen die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de integratie pagina **detectie voordelen SSO** -toepassing en selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **basis configuratie** van SAML is de toepassing vooraf geconfigureerd in de gestarte modus **IDP** en de benodigde url's zijn al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door te klikken op de knop **Opslaan** .

1. De SSO-toepassing voor voor delen van detectie verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![installatiekopie](common/edit-attribute.png)

    a. Klik op het pictogram **bewerken** om het dialoog venster **unieke gebruikers-ID (naam-id)** te openen.

    ![SSO-configuratie detectie voordelen](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![SSO-configuratie detectie voordelen](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. Klik op het pictogram **bewerken** om het dialoog venster **trans formatie beheren** te openen.

    c. In het tekstvak **Transformeren** typt u de **ToUppercase ()** die voor die rij wordt weer gegeven.

    d. Typ in het tekstvak **para meter 1** de para meter `<Name Identifier value>`zoals.

    e. Klik op **Add**.

    > [!NOTE]
    > Voor SSO van detectie voordelen moet een vaste teken reeks waarde worden door gegeven aan een **unieke gebruikers-id (naam-id)** om deze integratie te kunnen gebruiken. Azure AD biedt momenteel geen ondersteuning voor deze functie. u kunt **toupper** of **ToLower** -trans formaties van NameID gebruiken om een vaste teken reeks waarde in te stellen zoals hierboven in de scherm afbeelding wordt weer gegeven.

    f. We hebben de extra claims die vereist zijn voor de SSO-configuratie (`SSOInstance` en `SSOID`) automatisch ingevuld. Gebruik het **bewerkings** pictogram om de waarden te koppelen volgens uw organisatie.

    ![SSO-configuratie detectie voordelen](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer de gewenste URL ('s) op basis van uw vereiste op de SSO-sectie **detectie voordelen instellen** .

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SSO voor detectie voordelen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **detectie van voor delen SSO**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-discovery-benefits-sso-sso"></a>Detectie voordelen SSO SSO configureren

Voor het configureren van eenmalige aanmelding op **detectie voordelen SSO** -zijde moet u het gedownloade **certificaat (base64)** en de juiste gekopieerde url's verzenden van Azure Portal naar het [SSO-ondersteunings team voor detectie voordelen](mailto:Jsimpson@DiscoveryBenefits.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-discovery-benefits-sso-test-user"></a>SSO-test gebruiker voor voor delen van detectie maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in detectie voordelen SSO. Werk met [detectie van voor delen SSO-ondersteunings team](mailto:Jsimpson@DiscoveryBenefits.com) om de gebruikers toe te voegen in het SSO-platform detectie voordelen. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SSO van detectie voordelen in het toegangs venster klikt, moet u automatisch worden aangemeld bij de SSO-voor delen van detectie waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [SSO voor delen van detectie proberen met Azure AD](https://aad.portal.azure.com/)

