---
title: 'Zelfstudie: SSO-integratie (Single Sign-On) van Azure Active Directory met Profit.co SAML-app | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Profit.co SAML-app.
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
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770928"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco-saml-app"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Profit.co SAML App

In deze zelfstudie leert u hoe u Profit.co SAML-app integreren met Azure Active Directory (Azure AD). Wanneer u Profit.co SAML-app integreert met Azure AD, u het als:

* Beheer in Azure AD wie toegang heeft tot Profit.co SAML-app.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Profit.co SAML-app met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie, de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Profit.co SAML App single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Profit.co SAML App ondersteunt IDP geïnitieerde SSO.

* Nadat u Profit.co SAML-app hebt geconfigureerd, u sessiebeheer afdwingen. Dit beschermt exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime. Sessiebeheer strekt zich uit van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-profitco-saml-app-from-the-gallery"></a>Profit.co SAML-app toevoegen vanuit de galerie

Als u de integratie van Profit.co SAML-app in Azure AD wilt configureren, moet u Profit.co SAML-app uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Profit.co SAML-app** in de sectie **Toevoegen in de galeriesectie** in het zoekvak.
1. Selecteer **Profit.co SAML-app** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco-saml-app"></a>Azure AD-aanmelding configureren en testen voor Profit.co SAML-app

Azure AD SSO configureren en testen met Profit.co SAML-app met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een gekoppelde relatie aanmaken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Profit.co SAML-app.

Hier volgen de algemene stappen om Azure AD SSO te configureren en te testen met Profit.co SAML-app:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met B.Simon te testen.
    1. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Profit.co SAML App SSO](#configure-profitco-saml-app-sso)** om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak Profit.co SAML App-testgebruiker](#create-a-profitco-saml-app-test-user)** om een tegenhanger van B.Simon te hebben in Profit.co SAML-app. Deze tegenhanger is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Profit.co SAML** App-toepassingsintegratie de sectie **Beheren.** Selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Selecteer op de pagina **Eén aanmelding instellen met SAML** het potloodpictogram voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Schermafbeelding van Eenmalige aanmelding instellen met SAML-pagina, met potloodpictogram gemarkeerd](common/edit-urls.png)

1. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld in Azure. Gebruikers moeten de configuratie opslaan door de knop **Opslaan te** selecteren.

1. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de knop **Kopiëren.** Hiermee wordt de url van de **appfederatie-metagegevens kopieën** en opgeslagen op uw computer.

    ![Schermafbeelding van het SAML-ondertekeningscertificaat, waarbij de knop kopiëren is gemarkeerd](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory** > **Users** > **All users .**
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het veld **Wachtwoord.**
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Profit.co SAML App.

1. Selecteer in de Azure-portal Alle**bedrijfstoepassingen** **.** > 
1. Selecteer **Profit.co SAML-app**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Schermafbeelding van de sectie Beheren, met gemarkeerde gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoogvenster **Toewijzing toevoegen** de optie Gebruikers **en groepen**.

    ![Schermafbeelding van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de gebruikerslijst. Kies vervolgens de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens de knop **Selecteren** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-profitco-saml-app-sso"></a>Profit.co SAML App SSO configureren

Als u eenmalige aanmelding wilt configureren aan de Profit.co SAML-app-kant, moet u de URL van de app-federatie-metagegevens verzenden naar het [Profit.co SAML App-ondersteuningsteam.](mailto:support@profit.co) Het team configureert deze instelling dusdanig dat de SAML SSO-verbinding goed is ingesteld aan beide zijden.

### <a name="create-a-profitco-saml-app-test-user"></a>Een Profit.co SAML App-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd B.Simon in Profit.co SAML-app. Werk samen met het [Profit.co SAML App-ondersteuningsteam](mailto:support@profit.co) om de gebruikers toe te voegen aan het Profit.co SAML App-platform. U geen enkele aanmelding gebruiken totdat u gebruikers maakt en activeert.

## <a name="test-sso"></a>Test SSO

In deze sectie test u uw azure AD-configuratie voor eenmalige aanmelding met access panel.

Wanneer u de Profit.co SAML-app-tegel selecteert in het Access Panel, moet u automatisch worden aangemeld bij de Profit.co SAML-app waarvoor u SSO hebt ingesteld. Zie [Inleiding tot het toegangspaneel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie .

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Profit.co SAML-app met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hoe Profit.co SAML-app te beschermen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
