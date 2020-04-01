---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met TINFOIL SECURITY | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en TINFOIL-beveiliging.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d3078b553843922cd51e4e0f43ea84b6dcde16
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74170769"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met TINFOIL SECURITY

In deze zelfstudie leert u hoe u TINFOIL-beveiliging integreert met Azure Active Directory (Azure AD). Wanneer u TINFOIL-beveiliging integreert met Azure AD, u het als:

* Beheer in Azure AD wie toegang heeft tot TINFOIL-beveiliging.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij TINFOIL-beveiliging met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* TINFOIL SECURITY single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* TINFOIL SECURITY ondersteunt **IDP** geïnitieerde SSO

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-tinfoil-security-from-the-gallery"></a>TINFOIL-beveiliging toevoegen vanuit de galerie

Als u de integratie van TINFOIL-beveiliging in Azure AD wilt configureren, moet u TINFOIL-beveiliging vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **TINFOIL-beveiliging** in de sectie **Toevoegen in de galeriesectie** in het zoekvak.
1. Selecteer **TINFOIL-beveiliging** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Azure AD-aanmelding configureren en testen voor TINFOIL-beveiliging

Azure AD SSO configureren en testen met TINFOIL-beveiliging met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in TINFOIL-beveiliging.

Als u Azure AD SSO wilt configureren en testen met TINFOIL-beveiliging, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[TINFOIL SECURITY SSO configureren](#configure-tinfoil-security-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[TINFOIL SECURITY test user](#create-tinfoil-security-test-user)** - to have a counterpart of B.Simon in TINFOIL SECURITY that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **TINFOIL SECURITY-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Visitly-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, Visitly applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name | Bronkenmerk |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    > [!NOTE]
    > U krijgt de accountid waarde uitgelegd later in de tutorial.

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer in de sectie **SAML-ondertekeningscertificaat** de **waarde voor duimafdruk** en sla deze op uw computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. Kopieer in de sectie **TINFOIL-beveiliging** instellen de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot TINFOIL SECURITY.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **TINFOIL-BEVEILIGING**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-tinfoil-security-sso"></a>TINFOIL SECURITY SSO configureren

1. Meld u in een ander browservenster aan op de site van uw TINFOIL SECURITY-bedrijf als beheerder.

1. Klik op de werkbalk bovenaan op **Mijn account**.

    ![Dashboard](./media/tinfoil-security-tutorial/ic798971.png "Dashboard")

1. Klik op **Beveiliging**.

    ![Beveiliging](./media/tinfoil-security-tutorial/ic798972.png "Beveiliging")

1. Voer op de configuratiepagina **Single Sign-On** de volgende stappen uit:

    ![Eén aanmelding](./media/tinfoil-security-tutorial/ic798973.png "Eenmalige aanmelding")

    a. Selecteer **SAML inschakelen**.

    b. Klik **op Handmatige configuratie**.

    c. Plak in **het tekstvak SAML Post URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd van Azure-portal

    d. Plak in het tekstvak **SAML-certificaat vingerafdruk** de waarde van **Thumbprint** die u hebt gekopieerd uit de sectie **SAML-ondertekeningscertificaat.**
  
    e. Kopieer de waarde **van uw account-id** en plak de waarde in het tekstvak **Bronkenmerk** onder de sectie **Gebruikerskenmerken & claims** in azure-portal.

    f. Klik op **Opslaan**.

### <a name="create-tinfoil-security-test-user"></a>TINFOIL-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij TINFOIL SECURITY, moeten ze worden ingericht in TINFOIL-beveiliging. In het geval van TINFOIL SECURITY is inrichten een handmatige taak.

**Voer de volgende stappen uit om een gebruiker in te richten:**

1. Als de gebruiker deel uitmaakt van een Enterprise-account, moet u contact opnemen [met het beveiligingsondersteuningsteam van TINFOIL](https://www.tinfoilsecurity.com/contact) om het gebruikersaccount te laten maken.

1. Als de gebruiker een gewone SaaS-gebruiker van TINFOIL SECURITY is, kan de gebruiker een medewerker toevoegen aan een van de sites van de gebruiker. Dit activeert een proces om een uitnodiging naar de opgegeven e-mail te verzenden om een nieuw TINFOIL SECURITY-gebruikersaccount te maken.

> [!NOTE]
> U alle andere tools voor het maken van beveiligingsaccounts voor TINFOIL-gebruikers of API's die door TINFOIL SECURITY worden geleverd, gebruiken om Azure AD-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de beveiligingstegel TINFOIL klikt in het access panel, moet u automatisch worden aangemeld bij de Beveiliging van TINFOIL waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [TINFOIL-beveiliging uitproberen met Azure AD](https://aad.portal.azure.com/)