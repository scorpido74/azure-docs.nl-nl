---
title: 'Zelf studie: integratie Azure Active Directory met Carbonite-eind punt-back-up | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Carbonite-eind punt.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b78091f1-2f06-4c2c-8541-72aee0b5a322
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e078cb7daa787b9fe5e8bc996b36f0fef198f41c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68879685"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Zelf studie: Carbonite-eind punt-back-up integreren met Azure Active Directory

In deze zelf studie leert u hoe u Carbonite endpoint backup integreert met Azure Active Directory (Azure AD). Wanneer u Carbonite-eind punt back-up integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Carbonite endpoint-back-up.
* Stel in dat uw gebruikers automatisch worden aangemeld voor Carbonite endpoint backup met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Eenmalige aanmelding voor Carbonite-eind punt back-ups (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Carbonite endpoint Backup ondersteunt door **SP en IDP** GEÏNITIEERDe SSO

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Carbonite-eind punt back-up toevoegen vanuit de galerie

Als u de integratie van Carbonite-eind punt-back-up wilt configureren in azure AD, moet u Carbonite-eindpunt back-up toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ **Carbonite endpoint backup** in het zoekvak van de sectie **toevoegen vanuit de galerie** .
1. Selecteer **Carbonite endpoint backup** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test Azure AD SSO met Carbonite endpoint backup met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Carbonite-eind punt back-up.

Als u Azure AD SSO wilt configureren en testen met Carbonite endpoint backup, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[CONFIGUREER SSO van Carbonite-eind punt](#configure-carbonite-endpoint-backup-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Maak een Carbonite-eind punt gebruiker](#create-carbonite-endpoint-backup-test-user)** voor het maken van een back-up, zodat het een soort is van B. Simon in Carbonite endpoint Backup dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de integratie pagina **Carbonite endpoint backup** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Typ in het tekstvak **id** een van de volgende url's:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com`|
    | `https://red-apac.mysecuredatavault.com`|
    | `https://red-fr.mysecuredatavault.com`|
    | `https://red-emea.mysecuredatavault.com`|
    | `https://kamino.mysecuredatavault.com`|
    | | |

    b. Typ in het tekstvak **antwoord-URL** een van de volgende url's:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | | |

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **URL voor aanmelding** een van de volgende url's:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/`|
    | `https://red-apac.mysecuredatavault.com/`|
    | `https://red-fr.mysecuredatavault.com/`|
    | `https://red-emea.mysecuredatavault.com/`|
    | | |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer op de sectie **Carbonite endpoint backup** de juiste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>EENMALIGe back-up van Carbonite-eind punt configureren

1. Als u de configuratie in Carbonite-eind punt back-up wilt automatiseren, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op **Setup Carbonite endpoint backup** wordt u doorgestuurd naar de Carbonite-eindpunt back-uptoepassing. Geef de beheerders referenties op om u aan te melden bij het Carbonite-eind punt. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u Carbonite endpoint backup hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij de bedrijfs site van uw Carbonite-eind punt als beheerder en voert u de volgende stappen uit:

4. Klik op het **bedrijf** in het linkerdeel venster.

    ![Back-upconfiguratie voor Carbonite-eind punt ](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Klik op **eenmalige aanmelding**.

    ![Back-upconfiguratie voor Carbonite-eind punt ](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Klik op **inschakelen** en klik vervolgens op **Instellingen bewerken** om te configureren.

    ![Back-upconfiguratie voor Carbonite-eind punt ](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Voer de volgende stappen uit op de pagina instellingen voor **eenmalige aanmelding** :

    ![Back-upconfiguratie voor Carbonite-eind punt ](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. Plak in het tekstvak naam van de **identiteits provider** de waarde van de **Azure ad-id** , die u hebt gekopieerd van de Azure Portal.

    1. Plak in het tekstvak **ID-provider-URL** de waarde voor de AANMELDINGS- **URL** , die u hebt gekopieerd uit de Azure Portal.

    1. Klik op **bestand kiezen** om het gedownloade **certificaat bestand (base64)** te uploaden van de Azure Portal.

    1. Klik op **Opslaan**.

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

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot Carbonite-eind punt.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Carbonite endpoint backup**in de lijst toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-carbonite-endpoint-backup-test-user"></a>Test gebruiker voor Carbonite-eind punt maken

1. Meld u in een ander webbrowser venster aan bij uw Carbonite endpoint backup-bedrijfs site als beheerder.

1. Klik op **gebruikers** in het linkerdeel venster en klik vervolgens op **gebruiker toevoegen**.

    ![Gebruiker toevoegen in Carbonite-eind punt back-up](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. Voer op de pagina **gebruiker toevoegen** de volgende stappen uit:

    ![Gebruiker toevoegen in Carbonite-eind punt back-up](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Voer de **e-mail adres**, de **voor naam**, de **Achternaam** van de gebruiker en de vereiste machtigingen voor de gebruiker in op basis van de vereisten van de organisatie.

    1. Klik op **gebruiker toevoegen**.

### <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Carbonite endpoint backup in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Carbonite-eind punt back-up waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)