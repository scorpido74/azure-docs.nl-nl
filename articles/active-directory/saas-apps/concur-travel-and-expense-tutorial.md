---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met concur reizen en onkosten | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en concur reizen en onkosten.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6401ace4-71c0-4778-8b15-900f2f5f0c4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dddd9f6904aa5ef7840850792aeabf04666dddc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373419"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met concur reizen en onkosten

In deze zelf studie leert u hoe u concur Traveling and lasten integreert met Azure Active Directory (Azure AD). Wanneer u concur Traveling and lasten integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot concur reizen en onkosten.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld voor concur reizen en onkosten met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Concur reis-en onkosten abonnement.
* De rol ' bedrijfs beheerder ' onder uw concur-gebruikers account. U kunt testen of u de juiste toegang hebt door naar het [CONCUR SSO self-service tool](https://www.concursolutions.com/nui/authadmin/ssoadmin)te gaan. Als u geen toegang hebt, neemt u contact op met concur support of implementation project manager. 

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie kunt u Azure AD SSO configureren en testen.

* Concur Traveling en lasten bieden ondersteuning voor eenmalige aanmelding met **IDP** en **SP**
* Concur Traveling en lasten bieden ondersteuning voor het testen van SSO in zowel de productie-als implementatie omgeving 

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde voor elk van de drie regio's: US, EMEA en China. Er kan slechts één exemplaar worden geconfigureerd voor elke regio in één Tenant. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Concur Traveling and lasten toevoegen uit de galerie

Als u de integratie van concur-reizen en-uitgaven wilt configureren in azure AD, moet u concur Traveling en lasten toevoegen van de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **concur reizen en onkosten** in het zoekvak.
1. Selecteer **concur reizen en onkosten** in het paneel resultaten en voeg de app vervolgens toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor concur reizen en onkosten

Azure AD SSO configureren en testen met concur reizen en onkosten met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in concur reizen en onkosten.

Als u Azure AD SSO wilt configureren en testen met concur reizen en onkosten, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Concur reizen en onkosten-SSO configureren](#configure-concur-travel-and-expense-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak concur Traveling en onkosten test gebruiker](#create-concur-travel-and-expense-test-user)** -om een soort tegen te brengen van B. Simon in concur reizen en onkosten die zijn gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina **concur reis-en onkosten** toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **basis configuratie van SAML** is de toepassing vooraf geconfigureerd in de gestarte modus **IDP** en de benodigde url's zijn al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door te klikken op de knop **Opslaan** .

    > [!NOTE]
    > De id (Entiteits-ID) en de antwoord-URL (assertion Consumer Service-URL) zijn regio-specifiek. Selecteer op basis van het Data Center van uw concur-entiteit. Als u het Data Center van uw concur-entiteit niet weet, neemt u contact op met de ondersteuning van concur. 

5. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **gebruikers kenmerk** om de instellingen te bewerken. De unieke gebruikers-id moet overeenkomen met concur-gebruikers login_id. Normaal gesp roken moet u **User. userPrincipalName** wijzigen in **User. mail**.

    ![Gebruikers kenmerk bewerken](common/edit-attribute.png)

6. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om de meta gegevens te downloaden en op uw computer op te slaan.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan concur reizen en onkosten.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen **concur reizen en onkosten**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-concur-travel-and-expense-sso"></a>Concur reizen en onkosten-SSO configureren

1. Als u eenmalige aanmelding wilt configureren voor **concur reizen en onkosten** zijde, moet u de gedownloade **federatieve meta gegevens-XML** uploaden naar [concur SSO self-service tool](https://www.concursolutions.com/nui/authadmin/ssoadmin) en u aanmelden met een account met de rol ' bedrijfs beheerder '. 

1. Klik op **Add**.
1. Voer een aangepaste naam in voor uw IdP, bijvoorbeeld ' Azure AD (US) '. 
1. Klik op **XML-bestand uploaden** en voeg de **XML voor federatieve meta gegevens** toe die u eerder hebt gedownload.
1. Klik op **meta gegevens toevoegen** om de wijziging op te slaan.

    ![Scherm afbeelding van concur SSO self-service tool](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Concur reis-en onkosten test gebruiker maken

In deze sectie maakt u een gebruiker met de naam B. Simon in concur reizen en onkosten. Werk samen met concur-ondersteunings team om de gebruikers toe te voegen in het concur Traveling and onkosten-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

> [!NOTE]
> B. de aanmeldings-id van Simon concur moet overeenkomen met B. de unieke id van Simon bij Azure AD. Bijvoorbeeld als B. Simon is `B.Simon@contoso.com`de unieke id voor Azure AD. B. de aanmeldings-id van Simon concur moet `B.Simon@contoso.com` ook zijn. 

## <a name="configure-concur-mobile-sso"></a>Concur Mobile SSO configureren
Als u concur Mobile SSO wilt inschakelen, moet u de URL van de **gebruikers toegang**van concur ondersteunings team opgeven. Volg de onderstaande stappen om de **URL voor gebruikers toegang** op te halen uit Azure AD:
1. Ga naar **bedrijfs toepassingen**
1. Klik op **concur reizen en onkosten**
1. Klik op **Eigenschappen**
1. Kopieer de **gebruikers toegangs-URL** en geef deze URL naar concur-ondersteuning

> [!NOTE]
> De selfservice optie voor het configureren van SSO is niet beschikbaar, dus werk met concur-ondersteunings team om mobiele SSO in te scha kelen. 

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel concur reis-en onkosten in het toegangs venster klikt, moet u automatisch worden aangemeld bij de concur reizen en onkosten waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Extra resources

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer concur reizen en onkosten met Azure AD](https://aad.portal.azure.com/)

