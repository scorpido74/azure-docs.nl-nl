---
title: 'Zelfstudie: Azure Active Directory-integratie met Sage Intacct | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Sage Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b28390dba009226d493f5bfc6a5270b067f5bba0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73570468"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Zelfstudie: Sage Intacct integreren met Azure Active Directory

In deze zelfstudie leert u hoe U Sage Intacct integreren met Azure Active Directory (Azure AD). Wanneer u Sage Intacct integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Sage Intacct.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Sage Intacct met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Sage Intacct single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Sage Intacct ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-sage-intacct-from-the-gallery"></a>Sage Intacct toevoegen vanuit de galerie

Als u de integratie van Sage Intacct in Azure AD wilt configureren, moet u Sage Intacct vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Sage Intacct** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Sage Intacct** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sage-intacct"></a>Azure AD-aanmelding voor Sage Intacct configureren en testen

Azure AD SSO configureren en testen met Sage Intacct met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Sage Intacct.

Als u Azure AD SSO wilt configureren en testen met Sage Intacct, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
2. **[Sage Intacct SSO configureren](#configure-sage-intacct-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak Sage Intacct-testgebruiker](#create-sage-intacct-test-user)** - om een tegenhanger van B.Simon in Sage Intacct te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Sage Intacct-toepassingsintegratie** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    Typ een URL in het tekstvak **URL beantwoorden:**`https://www.intacct.com/ia/acct/sso_response.phtml`

1. Sage Intacct-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik **op Pictogram Bewerken** om het dialoogvenster Gebruikerskenmerken te openen..

    ![installatiekopie](common/edit-attribute.png)

1. Naast bovenstaande, Sage Intacct applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:

    | Name  |  Bronkenmerk|
    | ---------------| --------------- |
    | Bedrijfsnaam | **Sage Intacct Company ID** |
    | name | De waarde moet hetzelfde zijn als de **Gebruikers-id**van Sage Intacct , die u invoert in de **sectie Sage Intacct-testgebruikers maken,** die later in de zelfstudie wordt uitgelegd |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | De waarde moet hetzelfde zijn als de Sage Intacct **Federated SSO-gebruikersnaam**, die u invoert in de **sectie Sage Intacct-testgebruikers**maken, die later in de zelfstudie wordt uitgelegd |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Sage Intacct instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Sage Intacct.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Sage Intacct in**de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-sage-intacct-sso"></a>Sage Intacct SSO configureren

1. Meld u in een ander browservenster aan bij uw bedrijfssite van Sage Intacct als beheerder.

1. Klik op het tabblad **Bedrijf** en klik vervolgens op **Bedrijfsgegevens**.

    ![Bedrijf](./media/intacct-tutorial/ic790037.png "Bedrijf")

1. Klik op het tabblad **Beveiliging** en klik vervolgens op **Bewerken**.

    ![Beveiliging](./media/intacct-tutorial/ic790038.png "Beveiliging")

1. Voer in de sectie **Enkelteken op (SSO)** de volgende stappen uit:

    ![Eén enkel teken op](./media/intacct-tutorial/ic790039.png "eenmalig teken op")

    a. Selecteer **Eén teken inschakelen op**.

    b. Selecteer **SAML 2.0**als **type identiteitsprovider**.

    c. Plak in het tekstvak **van de url** van uitgever de waarde van azure **AD-id**, die u hebt gekopieerd vanuit azure-portal.

    d. Plak in het tekstvak **Voor aanmeldings-URL** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd vanuit azure-portal.

    e. Open uw **basis-64** gecodeerd certificaat in kladblok, kopieer de inhoud ervan in uw klembord en plak het vervolgens in het vak **Certificaat.**

    f. Klik op **Opslaan**.

### <a name="create-sage-intacct-test-user"></a>Sage Intacct-testgebruiker maken

Als u Azure AD-gebruikers wilt instellen zodat ze zich kunnen aanmelden bij Sage Intacct, moeten ze zijn ingericht in Sage Intacct. Voor Sage Intacct is inrichten een handmatige taak.

**Voer de volgende stappen uit om gebruikersaccounts in te richten:**

1. Meld u aan bij uw **Sage Intacct-huurder.**

1. Klik op het tabblad **Bedrijf** en klik vervolgens op **Gebruikers**.

    ![Gebruikers](./media/intacct-tutorial/ic790041.png "Gebruikers")

1. Klik op het tabblad **Toevoegen.**

    ![Toevoegen](./media/intacct-tutorial/ic790042.png "Toevoegen")

1. Voer in de sectie **Gebruikersgegevens** de volgende stappen uit:

    ![Gebruikersgegevens](./media/intacct-tutorial/ic790043.png "Gebruikersgegevens")

    a. Voer de **gebruikersnaam**, de **achternaam**, **voornaam,** het **e-mailadres,** de **titel**en de **telefoon** van een Azure AD-account in die u wilt inrichten in de sectie **Gebruikersgegevens.**

    > [!NOTE]
    > Zorg ervoor dat de **gebruikersnaam** in bovenstaande schermafbeelding en de waarde van het **bronkenmerk** die is toegewezen met het **naamkenmerk** in de sectie **Gebruikerskenmerken** in de Azure-portal, hetzelfde moeten zijn.

    b. Selecteer de **beheerdersbevoegdheden** van een Azure AD-account die u wilt inrichten.

    c. Klik op **Opslaan**. 
    
    d. De houder van het Azure AD-account ontvangt een e-mail en volgt een koppeling om zijn/haar account te bevestigen voordat het actief wordt.

1. Klik op het tabblad **Eén aanmelding** en controleer of de **Federatieve SSO-gebruikers-id** hieronder `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` en de waarde van het **bronkenmerk** die is toegewezen aan de sectie **Gebruikerskenmerken** in de Azure-portal, hetzelfde moeten zijn.

    ![Gebruikersgegevens](./media/intacct-tutorial/ic790044.png "Gebruikersgegevens")

> [!NOTE]
> Als u Azure AD-gebruikersaccounts wilt inrichten, u andere hulpprogramma's voor het maken van gebruikersaccounts of API's voor Sage Intacct gebruiken die door Sage Intacct worden geleverd.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Sage Intacct in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Sage Intacct waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

