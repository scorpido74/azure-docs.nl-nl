---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met TextMagic | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en TextMagic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca7b900a0e2203fffb5240acd4ab5201fc9d52d7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72533033"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-textmagic"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met TextMagic

In deze zelfstudie leert u hoe u TextMagic integreert met Azure Active Directory (Azure AD). Wanneer u TextMagic integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot TextMagic.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij TextMagic met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* TextMagic single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* TextMagic ondersteunt **IDP** geïnitieerde SSO

* TextMagic ondersteunt **Just In Time** gebruikersinrichting

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-textmagic-from-the-gallery"></a>TextMagic toevoegen vanuit de galerie

Als u de integratie van TextMagic in Azure AD wilt configureren, moet u TextMagic vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **TextMagic** in de sectie **Toevoegen in de galerie** in het zoekvak.
1. Selecteer **TextMagic** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-textmagic"></a>Azure AD-aanmelding voor TextMagic configureren en testen

Azure AD SSO configureren en testen met TextMagic met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in TextMagic.

Als u Azure AD SSO wilt configureren en testen met TextMagic, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[TextMagic SSO configureren](#configure-textmagic-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[TextMagic-testgebruiker maken](#create-textmagic-test-user)** - om een tegenhanger van B.Simon in TextMagic te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **TextMagic-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    In het tekstvak **Id** typt u een URL: `https://my.textmagic.com/saml/metadata`

5. TextMagic-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. TextMagic-toepassing verwacht **dat naam-id** wordt toegewezen met **user.mail,** dus u moet de toewijzing van het kenmerk bewerken door te klikken op **pictogram Bewerken** en de toewijzing van het kenmerk wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Naast bovenstaande, TextMagic applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw eis.

    | Name |   Bronkenmerk| Naamruimte  |
    | --------------- | --------------- | --------------- |
    | bedrijf | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | voornaam               | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | achternaam            | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | telefoon               | User.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **TextMagic instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot TextMagic.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **TextMagic**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="configure-textmagic-sso"></a>TextMagic SSO configureren

1. Als u de configuratie binnen TextMagic wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure sign-in** installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup TextMagic** en leidt u naar de TextMagic-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij TextMagic. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-5.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u TextMagic handmatig wilt instellen, opent u een nieuw webbrowservenster en meldt u zich als beheerder aan bij uw TextMagic-bedrijfssite en voert u de volgende stappen uit:

4. Selecteer **Accountinstellingen** onder de gebruikersnaam.

    ![TextMagic-configuratie](./media/textmagic-tutorial/config1.png)

5. Klik op het tabblad **Aanmelding (SSO)** en vul de volgende velden in:  

    ![TextMagic-configuratie](./media/textmagic-tutorial/config2.png)

    a. In **Identiteitsprovider Entiteit-id:** tekstvak plakt u de waarde van **azure AD-id**, die u hebt gekopieerd van Azure-portal.

    b. In **De URL van de identiteitprovider SSO:** tekstvak, plak de waarde van Login **URL**, die u van azure portal hebt gekopieerd.

    c. In **de URL van identiteitsprovider SLO:** tekstvak plakt u de waarde van de URL van **Afmelden**, die u hebt gekopieerd van azure-portal.

    d. Open uw **basis-64 gecodeerd certificaat** in notitieblok gedownload van Azure portal, kopieer de inhoud ervan in uw klembord en plak het vervolgens op het **Public x509-certificaat:** textbox.

    e. Klik op **Opslaan**.

### <a name="create-textmagic-test-user"></a>TekstMagic-testgebruiker maken

Applicatie ondersteunt **Just in time user provisioning** en after authentication users will be created in the application automatically. Je moet de informatie eenmaal invullen bij de eerste login om het subaccount in het systeem te activeren.
Er is geen actie-item voor u in deze sectie.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel TextMagic in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de TextMagic waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer TextMagic met Azure AD](https://aad.portal.azure.com/)

