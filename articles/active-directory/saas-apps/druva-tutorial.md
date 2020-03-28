---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Druva | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f019d818fb5a017d184bda8d773eb0aaf0f3645a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78944403"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-druva"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Druva

In deze zelfstudie leert u hoe u Druva integreert met Azure Active Directory (Azure AD). Wanneer u Druva integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Druva.
* Stel uw gebruikers in om automatisch te worden aangemeld bij Druva met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Druva single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Druva ondersteunt **IDP** geïnitieerde SSO
* Zodra u Druva SSO hebt geconfigureerd, u sessiecontrole afdwingen, wat exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermt. Sessiebesturingselement strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-druva-from-the-gallery"></a>Druva uit de galerie toevoegen

Om de integratie van Druva in Azure Active Directory te configureren, moet u Druva uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Druva** in het zoekvak in de sectie **Toevoegen van de galerie.**
1. Selecteer **Druva** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-druva"></a>Azure AD-aanmelding voor Druva configureren en testen

Azure AD SSO configureren en testen met Druva met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Druva.

Als u Azure AD SSO met Druva wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Druva SSO](#configure-druva-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak Druva-testgebruiker](#create-druva-test-user)** - om een tegenhanger van B.Simon in Druva te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Druva-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. Typ in het tekstvak **Id (Entity ID)** de tekenreekswaarde: `DCP-login`.
    
    b. Typ de URL van het tekstvak **Antwoord-URL (Bevestigingsconsumentenservice)** in het tekstvak Van de Consumentenbond: `https://cloud.druva.com/wrsaml/consume`.

1. Klik op **Opslaan**.

1. Druva-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, Druva applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name | Bronkenmerk|
    | ------------------- | -------------------- |
    | Emailaddress | user.email |
    | druva_auth_token | SSO-token gegenereerd vanaf DCP Admin Console, zonder aanhalingstekens.  Bijvoorbeeld: X-XXXXX-XXXX-S-A-M-P-L-E+TXOXKXEXNX=. Azure voegt automatisch aanhalingstekens toe rond het auth-token. |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Druva instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Druva.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Druva**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-druva-sso"></a>Druva SSO configureren

1. Meld u in een ander browservenster aan op uw Druva-bedrijfssite als beheerder.

1. Klik op het Druva-logo in de linkerbovenhoek en klik vervolgens op **Druva Cloud Settings**.

    ![Instellingen](./media/druva-tutorial/ic795091.png "Instellingen")

1. Klik op het tabblad **Eén aanmelding** op **Bewerken**.

    ![Instellingen voor eenmalig aanmelden](./media/druva-tutorial/ic795092.png "Instellingen voor eenmalig aanmelden")

1. Voer op de pagina **Instellingen voor eenmalig aanmelden** de volgende stappen uit:

    ![Instellingen voor eenmalig aanmelden](./media/druva-tutorial/ic795095.png "Instellingen voor eenmalig aanmelden")

    1. Plak in het tekstvak **Aanmeldings-URL van ID-provier** de waarde van de **Aanmeldings-URL** in die u hebt gekopieerd uit de Azure Portal.

    1. Open uw base-64 gecodeerde certificaat in Kladblok, kopieer de inhoud ervan naar het klembord en plak deze naar het tekstvak **Certificaat ID-provider**

       > [!NOTE]
       > Als u Eenmalige aanmelding voor beheerders wilt inschakelen, selecteert u **Beheerders die zich aanmelden bij Druva Cloud via de SSO-provider** en geeft u **failsafe-toegang tot selectievakjes (aanbevolen) van Druva Cloud-beheerders.** Druva raadt aan om Failsafe in te schakelen **voor beheerders,** zodat ze toegang hebben tot de DCP-console in geval van storingen in IdP. Het stelt de beheerders ook in staat om zowel het SSO- als het DCP-wachtwoord te gebruiken om toegang te krijgen tot de DCP-console.

    1. Klik op **Opslaan**. Dit maakt de toegang tot Druva Cloud Platform mogelijk met Behulp van SSO.

### <a name="create-druva-test-user"></a>Druva-testgebruiker maken

In deze sectie wordt een gebruiker genaamd B.Simon gemaakt in Druva. Druva ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Druva, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Druva in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Druva waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Druva met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)