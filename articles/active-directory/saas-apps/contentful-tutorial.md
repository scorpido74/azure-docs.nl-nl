---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met Contentful | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Contentful.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f29e1015-d508-4698-a381-5d871c646161
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd218c61114c1e15009ace5a9a9bd7a536996e86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72968652"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-contentful"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Contentful

In deze zelfstudie leert u hoe u Contentful integreren met Azure Active Directory (Azure AD). Wanneer u Contentful integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Contentful.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Contentful met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Contentful single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Contentful ondersteunt **SP en IDP** geïnitieerd sso
* Contentful ondersteunt **Just In Time-gebruikersinrichting**

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde. Er kan slechts één exemplaar in één tenant worden geconfigureerd.

## <a name="adding-contentful-from-the-gallery"></a>Contentful toevoegen vanuit de galerie

Als u de integratie van Contentful in Azure AD wilt configureren, moet u Contentful vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ Inhoud in het zoekvak in de sectie **Toevoegen in de sectie Toevoegen in de galerie.** **Contentful**
1. Selecteer **Inhoud in** de resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-contentful"></a>Azure AD-aanmelding voor Contentful configureren en testen

Azure AD SSO configureren en testen met Contentful met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Contentful.

Als u Azure AD SSO met Contentful wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Contentful SSO](#configure-contentful-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Contentful-testgebruiker maken](#create-contentful-test-user)** - om een tegenhanger van B.Simon in Contentful te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Contentful-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    - Kopieer in het tekstvak **Url van antwoord** de URL ACS (Assertion Consumer Service) vanaf de installatiepagina van SSO in Contentful. Het zal er als volgt uitzien:`https://be.contentful.com/sso/<organization_id>/consume`

1. Klik **op Extra URL's instellen** en voer de volgende stap uit als u de toepassing wilt configureren in de gestarte **SP-modus:**

    - Kopieer in het tekstvak **URL aanmelding** dezelfde URL van ACS (Assertion Consumer Service). Het zal er als volgt uitzien:`https://be.contentful.com/sso/<organization_id>/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL van antwoord en aanmeldings-URL door de URL van de ACS-URL (Assertion Consumer Service) te kopiëren vanaf de installatiepagina van SSO in Contentful.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Inhoud instellen** de inlog-URL om Contentful SSO te configureren.

    ![Configuratie-URL's kopiëren](media/contentful-tutorial/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory**, selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Contentful.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Contentful**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan de pagina.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan de pagina.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-contentful-sso"></a>Contentful SSO configureren

Volg deze stappen om eenmalige aanmelding aan de **kant contentful** te configureren.

1. Navigeer in [Contentful](https://app.contentful.com)naar de installatiepagina van SSO in **Organisatie-instellingen**.
1. Klik op **SSO instellen**.
1. Kopieer en plak de aanmeldings-URL uit de sectie **Inhoud instellen in** Azure AD.
1. Kopieer en plak het certificaat uit het Base64-certificaatbestand dat u hebt gedownload van Azure AD.
1. Stel een SSO-naam in voor inloginde IN- en Inlogen met SP.
1. Klik op **SSO inschakelen**.

Als dat niet werkt, neem dan contact op met het [ondersteuningsteam van Contentful.](mailto:support@contentful.com)

### <a name="create-contentful-test-user"></a>Contentful testgebruiker maken

In deze sectie wordt een gebruiker genaamd B.Simon gemaakt in Contentful. Contentful ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Contentful, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Contentful in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Inhoud waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Contentful uitproberen met Azure AD](https://aad.portal.azure.com/)
