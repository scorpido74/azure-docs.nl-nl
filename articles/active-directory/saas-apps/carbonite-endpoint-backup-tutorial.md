---
title: 'Zelfstudie: Azure Active Directory-integratie met Carbonite Endpoint Backup | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Carbonite Endpoint Backup.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68879685"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Zelfstudie: Carbonite Endpoint Backup integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Carbonite Endpoint Backup integreren met Azure Active Directory (Azure AD). Wanneer u Carbonite Endpoint Backup integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Carbonite Endpoint Backup.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Carbonite Endpoint Backup met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Carbonite Endpoint Backup single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Carbonite Endpoint Backup ondersteunt **SP en IDP** geïnitieerd SSO

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Carbonite Endpoint Backup toevoegen vanuit de galerie

Als u de integratie van Carbonite Endpoint Backup in Azure AD wilt configureren, moet u Carbonite Endpoint Backup vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Carbonite Endpoint Backup** in de sectie **Toevoegen in de galeriesectie** in het zoekvak.
1. Selecteer **Carbonite Endpoint Backup** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Carbonite Endpoint Backup met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Carbonite Endpoint Backup.

Als u Azure AD SSO wilt configureren en testen met Carbonite Endpoint Backup, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Carbonite Endpoint Backup SSO](#configure-carbonite-endpoint-backup-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. Maak de gebruiker van **[de Carbonite Endpoint Backup-test-](#create-carbonite-endpoint-backup-test-user)** om een tegenhanger van B.Simon in Carbonite Endpoint Backup te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de **integratiepagina van carbonite-eindpuntback-uptoepassingen** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een van de volgende URL's:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com`|
    | `https://red-apac.mysecuredatavault.com`|
    | `https://red-fr.mysecuredatavault.com`|
    | `https://red-emea.mysecuredatavault.com`|
    | `https://kamino.mysecuredatavault.com`|
    | | |

    b. Typ in het tekstvak **URL beantwoorden** een van de volgende URL's:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | | |

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **AANmeldings-URL** een van de volgende URL's:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/`|
    | `https://red-apac.mysecuredatavault.com/`|
    | `https://red-fr.mysecuredatavault.com/`|
    | `https://red-emea.mysecuredatavault.com/`|
    | | |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Carbonite Endpoint Backup instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Carbonite Endpoint Backup SSO configureren

1. Als u de configuratie binnen Carbonite Endpoint Backup wilt automatiseren, moet u **de beveiligingsextensie Mijn apps Secure Sign-in** installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u extensie aan de browser hebt toegevoegd, klikt u op **Setup Carbonite Endpoint Backup** en leidt u naar de Carbonite Endpoint Backup-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Carbonite Endpoint Backup. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Carbonite Endpoint Backup handmatig wilt instellen, opent u een nieuw webbrowservenster en meldt u zich aan bij uw Carbonite Endpoint Backup-bedrijfssite als beheerder en voert u de volgende stappen uit:

4. Klik in het linkerdeelvenster op het **bedrijf.**

    ![Carbonite Endpoint Backup-configuratie ](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Klik op **Eenmalig aanmelden**.

    ![Carbonite Endpoint Backup-configuratie ](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Klik op **Inschakelen** en klik vervolgens op **Instellingen bewerken** om te configureren.

    ![Carbonite Endpoint Backup-configuratie ](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Voer op de pagina **Instellingen voor één aanmelding** de volgende stappen uit:

    ![Carbonite Endpoint Backup-configuratie ](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. Plak in het tekstvak naam van de **identiteitsprovider** de azure **AD-id-waarde,** die u hebt gekopieerd van de Azure-portal.

    1. Plak in het **tekstvak URL-provider Identiteit** de **URL-waarde aanmelding,** die u hebt gekopieerd van de Azure-portal.

    1. Klik op **Bestand kiezen** om het gedownloade **certificaatbestand (Base64)** te uploaden vanuit de Azure-portal.

    1. Klik op **Opslaan**.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Carbonite Endpoint Backup.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Carbonite Endpoint Backup**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-carbonite-endpoint-backup-test-user"></a>Gebruiker van Carbonite Endpoint Backup-testmaken

1. Meld u in een ander browservenster aan bij uw carbonite-eindpuntback-upsite als beheerder.

1. Klik op de **gebruikers** in het linkerdeelvenster en klik vervolgens op **Gebruiker toevoegen**.

    ![Gebruiker toevoegen in Carbonite Endpoint Backup](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. Voer op de pagina **Gebruiker toevoegen** de volgende stappen uit:

    ![Gebruiker toevoegen in Carbonite Endpoint Backup](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Voer de **e-mail**, **voornaam**, **achternaam** van de gebruiker in en geef de vereiste machtigingen aan de gebruiker op basis van de organisatorische vereisten.

    1. Klik **op Gebruiker toevoegen**.

### <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Carbonite Endpoint Backup in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Carbonite Endpoint Backup waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)