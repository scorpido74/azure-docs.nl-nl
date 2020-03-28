---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Clever | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Clever.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6884445b49b518654ccb1484b024728c4feb2b51
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75689649"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-clever"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Clever

In deze zelfstudie leert u hoe u Clever integreren met Azure Active Directory (Azure AD). Wanneer u Clever integreert met Azure AD, u het:

* Beheer in Azure AD die toegang heeft tot Clever.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Clever met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Slim single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Clever ondersteunt door **SP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-clever-from-the-gallery"></a>Clever toevoegen vanuit de galerie

Voor het configureren van de integratie van Clever in Azure AD, moet u Clever vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ Slim in het zoekvak in de sectie Toevoegen in de sectie **Toevoegen** in de **galerie.**
1. Selecteer **Slim** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-clever"></a>Azure AD-aanmelding configureren en testen voor Clever

Azure AD SSO configureren en testen met Clever met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Clever.

Als u Azure AD SSO met Clever wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Clever SSO configureren](#configure-clever-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak slimme testgebruiker](#create-clever-test-user)** - om een tegenhanger van B.Simon in Clever te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Slimme** toepassingsintegratie de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://clever.com/in/<companyname>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://clever.com/oauth/saml/metadata.xml`

    > [!NOTE]
    > De waarde van de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [Clever-ondersteuningsteam](https://clever.com/about/contact/) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** op de knop Kopiëren om **de url van de appfederatie-metagegevens** te kopiëren en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Clever.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Clever** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-clever-sso"></a>Slimme SSO configureren

1. Teken in een ander browservenster bij uw Clever-bedrijfssite als beheerder.

1. Klik in de werkbalk op **Directe aanmelding**.

    ![Direct inloggen](./media/clever-tutorial/ic798984.png "Direct inloggen")

    > [!NOTE]
    > Voordat u eenmalige aanmelding kunt testen, moet u contact opnemen met het [Clever-ondersteuningsteam](https://clever.com/about/contact/) om eenmalige aanmelding voor Office 365 in de back-end in te schakelen.

1. Voer op de pagina **Directe aanmelding** de volgende stappen uit:
 
    ![Direct inloggen](./media/clever-tutorial/ic798985.png "Direct inloggen")

    a. Typ de **aanmeldings-URL**.

    >[!NOTE]
    >De **aanmeldings-URL** is een aangepaste waarde. Neem contact op met het [Clever-ondersteuningsteam](https://clever.com/about/contact/) om deze waarde te verkrijgen.

    b. Selecteer **ADFS** als **Identiteitssysteem**.

    c. Plak in het tekstvak **Metagegevens-URL** de waarde van **App-URL voor federatieve metagegevens** die u uit de Azure-portal hebt gekopieerd.

    d. Klik op **Opslaan**.

### <a name="create-clever-test-user"></a>Testgebruiker voor Clever maken

Om Azure AD-gebruikers in staat te stellen zich bij Clever te ondertekenen, moeten ze worden ingericht in Clever.

Werk in het geval van Clever samen met het [Clever-ondersteuningsteam](https://clever.com/about/contact/) om de gebruikers toe te voegen aan het Clever-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het creëren van Clever-gebruikersaccounts of API's van Clever gebruiken om Azure AD-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Clever klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Clever waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Clever met Azure AD](https://aad.portal.azure.com/)