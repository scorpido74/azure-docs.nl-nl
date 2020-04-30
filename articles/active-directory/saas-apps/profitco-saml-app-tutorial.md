---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Profit.co SAML-app | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en de SAML-app Profit.co.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 27f42934-c636-43dd-9c20-a3c6316f2763
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 564ca97210d85c5118901f30261abe3de9df1053
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81770928"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco-saml-app"></a>Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met Profit.co SAML-app

In deze zelf studie leert u hoe u Profit.co SAML app integreert met Azure Active Directory (Azure AD). Wanneer u Profit.co SAML-app integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de Profit.co SAML-app.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Profit.co SAML-app met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie, het Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Profit.co-abonnement van de SAML-app voor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Profit.co SAML-app ondersteunt door IDP geïnitieerde SSO.

* Nadat u de Profit.co SAML-app hebt geconfigureerd, kunt u sessie beheer afdwingen. Dit beveiligt exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time. Sessie beheer wordt uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-profitco-saml-app-from-the-gallery"></a>Profit.co SAML-app toevoegen vanuit de galerie

Als u de integratie van Profit.co SAML-app wilt configureren in azure AD, moet u Profit.co SAML-app uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **profit.co SAML-app** in het zoekvak.
1. Selecteer **profit.co SAML-app** in het deel venster resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco-saml-app"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Profit.co SAML-app

Configureer en test Azure AD SSO met Profit.co SAML-app met behulp van een test gebruiker met de naam **B. Simon**. Als u SSO wilt gebruiken, moet u een gekoppelde relatie instellen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de Profit.co SAML-app.

Hier volgen de algemene stappen voor het configureren en testen van Azure AD SSO met Profit.co SAML-app:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. **[Configureer profit.co SAML app SSO](#configure-profitco-saml-app-sso)** om de instellingen voor eenmalige aanmelding te configureren aan de kant van de toepassing.
    1. **[Maak een profit.co-test gebruiker](#create-a-profitco-saml-app-test-user)** voor de SAML-app om een tegen hanger te hebben van B. Simon in profit.co SAML-app. Deze tegen hanger is gekoppeld aan de Azure AD-weer gave van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **profit.co SAML app** Application Integration de sectie **Manage** . Selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het potlood pictogram voor de basis- **SAML-configuratie** om de instellingen te bewerken.

   ![Scherm afbeelding van het instellen van eenmalige aanmelding met de SAML-pagina, waarbij het potlood pictogram is gemarkeerd](common/edit-urls.png)

1. In de sectie **basis configuratie van SAML** is de toepassing vooraf geconfigureerd en zijn de benodigde url's al vooraf ingevuld in Azure. Gebruikers moeten de configuratie opslaan door de knop **Opslaan** te selecteren.

1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de knop **kopiëren** . Hiermee wordt de **URL voor de federatieve meta gegevens** van de app gekopieerd en op uw computer opgeslagen.

    ![Scherm opname van het SAML-handtekening certificaat, met de knop kopiëren gemarkeerd](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer **Azure Active Directory** > **gebruikers** > van**alle gebruikers**in het linkerdeel venster van de Azure Portal.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het veld **wacht woord** .
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de Profit.co SAML-app.

1. Selecteer in het Azure Portal**alle toepassingen**in **bedrijfs toepassingen** > .
1. Selecteer in de lijst toepassingen de optie **profit.co SAML-app**.
1. Zoek op de pagina overzicht van de app de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![Scherm afbeelding van de sectie beheren met gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![Scherm opname van de pagina gebruikers en groepen, met gemarkeerde gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers. Kies vervolgens de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens de knop **selecteren** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-profitco-saml-app-sso"></a>Profit.co SAML app SSO configureren

Als u eenmalige aanmelding wilt configureren voor de Profit.co SAML-app, moet u de URL voor de federatieve meta gegevens van de app verzenden naar het [ondersteunings team van de profit.co SAML-app](mailto:support@profit.co). Het team configureert deze instelling dusdanig dat de SAML SSO-verbinding goed is ingesteld aan beide zijden.

### <a name="create-a-profitco-saml-app-test-user"></a>Een test gebruiker voor een Profit.co SAML-app maken

In deze sectie maakt u een gebruiker met de naam B. Simon in Profit.co SAML-app. Werk met het [ondersteunings team voor profit.co SAML-apps](mailto:support@profit.co) om de gebruikers toe te voegen in het profit.co SAML-app-platform. U kunt eenmalige aanmelding pas gebruiken als u gebruikers maakt en activeert.

## <a name="test-sso"></a>SSO testen

In deze sectie kunt u de configuratie van eenmalige aanmelding voor Azure AD testen met behulp van het toegangs venster.

Wanneer u de tegel Profit.co SAML-app in het toegangs venster selecteert, moet u automatisch worden aangemeld bij de Profit.co SAML-app waarvoor u SSO hebt ingesteld. Zie [Inleiding tot het toegangs venster](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Profit.co SAML-app uit met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Profit.co SAML-app beveiligen met geavanceerde zicht baarheid en besturings elementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
