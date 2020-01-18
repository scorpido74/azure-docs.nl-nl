---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Dovetale | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Dovetale configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81da50c3-df94-458a-8b6a-a30827ee6358
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2c96c6ddd015285b7780c70741ddb704866aa60
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260647"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-dovetale"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Dovetale

In deze zelf studie leert u hoe u Dovetale integreert met Azure Active Directory (Azure AD). Wanneer u Dovetale integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Dovetale.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Dovetale met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Dovetale-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.



* Dovetale ondersteunt SSO die door **SP en IDP** is geïnitieerd
* Dovetale biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.


## <a name="adding-dovetale-from-the-gallery"></a>Dovetale toevoegen vanuit de galerie

Om de integratie van Dovetale te configureren in Azure AD moet u Dovetale vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Dovetale** in het zoekvak.
1. Selecteer **Dovetale** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-dovetale"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Dovetale

Azure AD SSO met Dovetale configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Dovetale.

Als u Azure AD SSO wilt configureren en testen met Dovetale, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[DOVETALE SSO configureren](#configure-dovetale-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Dovetale-test gebruiker](#create-dovetale-test-user)** -om een equivalent van B. Simon in Dovetale te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Dovetale** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte van de **basis configuratie van SAML** is de toepassing vooraf geconfigureerd in de modus **IDP** gestart en de benodigde url's zijn al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door te klikken op de knop  **Opslaan** .

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `<COMPANYNAME>.dovetale.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Dovetale](mailto:support@dovetale.com) om de waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De Dovetale-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

1. Daarnaast verwacht Dovetale toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereiste.

    | Name | Bronkenmerk|
    | ---------------| --------------- |
    | e-mail | user.mail |
    | first_name | user.givenname |
    | name | user.userprincipalname |
    | last_name | user.surname |

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op de knop kopiëren om de URL van de **app Federation-meta gegevens** te kopiëren en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Dovetale.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Dovetale** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-dovetale-sso"></a>Dovetale SSO configureren

Als u eenmalige aanmelding wilt configureren in **Dovetale**, moet u de **App-URL voor federatieve metagegevens** verzenden naar het [ondersteuningsteam van Dovetale](mailto:support@dovetale.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-dovetale-test-user"></a>Dovetale-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Dovetale. Dovetale biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Dovetale bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

> [!Note]
> Neem contact op met het [ondersteuningsteam van Dovetale](mailto:support@dovetale.com) als u handmatig een gebruiker moet maken.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Dovetale klikt, wordt u automatisch aangemeld bij de instantie van Dovetale waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Dovetale met Azure AD](https://aad.portal.azure.com/)

