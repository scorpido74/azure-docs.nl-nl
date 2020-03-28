---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Check Point CloudGuard Dome9 Arc | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Check Point CloudGuard Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b46ac34db21576c7e2de2271a468e3e782ff6aa9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73885355"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Check Point CloudGuard Dome9 Arc

In deze zelfstudie leert u hoe u Check Point CloudGuard Dome9 Arc integreert met Azure Active Directory (Azure AD). Wanneer u Check Point CloudGuard Dome9 Arc integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Check Point CloudGuard Dome9 Arc.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Check Point CloudGuard Dome9 Arc met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Controleer het abonnement met Point CloudGuard Dome9 Arc single sign-on (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Check Point CloudGuard Dome9 Arc ondersteunt **SP en IDP** gestart sso

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Check Point CloudGuard Dome9 Arc toevoegen vanuit de galerij

Als u de integratie van Check Point CloudGuard Dome9 Arc in Azure AD wilt configureren, moet u Check Point CloudGuard Dome9 Arc vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ In de sectie **Toevoegen in de galerie** het selectievakje Point **CloudGuard Dome9 Arc** in het zoekvak controleren.
1. Selecteer **Point CloudGuard Dome9 Arc controleren** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-check-point-cloudguard-dome9-arc"></a>Azure AD-aanmelding configureren en testen voor Check Point CloudGuard Dome9 Arc

Azure AD SSO configureren en testen met Check Point CloudGuard Dome9 Arc met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Check Point CloudGuard Dome9 Arc.

Als u Azure AD SSO wilt configureren en testen met Check Point CloudGuard Dome9 Arc, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Check Point CloudGuard Dome9 Arc SSO](#configure-check-point-cloudguard-dome9-arc-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak de testgebruiker van Check Point CloudGuard Dome9-arc](#create-check-point-cloudguard-dome9-arc-test-user)** - om een tegenhanger van B.Simon te hebben in Check Point CloudGuard Dome9 Arc die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina De integratie van de **Check Point CloudGuard Dome9 Arc-toepassing** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ een URL in het tekstvak **Id:**`https://secure.dome9.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke antwoord-URL en aanmeldings-URL. U krijgt `<company name>` de waarde van de sectie **Configure Check Point CloudGuard Dome9 Arc SSO,** die later in de zelfstudie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Check Point CloudGuard Dome9 Arc-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

1. Naast bovenstaande, Check Point CloudGuard Dome9 Arc applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw eis.
    
    | Name |  Bronkenmerk|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    >[!NOTE]
    >Klik [hier](https://docs.microsoft.com/azure/active-directory/saas-apps/apptio-tutorial) om te weten hoe u rollen maken in Azure AD.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Check Point CloudGuard Dome9 Arc** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Check Point CloudGuard Dome9 Arc.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer In de lijst met toepassingen de optie **Point CloudGuard Dome9 Arc**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Check Point CloudGuard Dome9 Arc SSO configureren

1. Als u de configuratie in Check Point CloudGuard Dome9 Arc wilt automatiseren, moet u **de beveiligingsextensie Mijn apps Secure Sign-in** installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup Check Point CloudGuard Dome9 Arc** en leidt u naar de Check Point CloudGuard Dome9 Arc-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Check Point CloudGuard Dome9 Arc. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-6.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Check Point CloudGuard Dome9 Arc handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij uw bedrijf Check Point CloudGuard Dome9 Arc en voert u de volgende stappen uit:

2. Klik op **Profile Settings** in de rechterbovenhoek en vervolgens op **Account Settings**. 

    ![Controleer de configuratie van Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure1.png)

3. Navigeer naar **SSO** en klik vervolgens op **ENABLE**.

    ![Controleer de configuratie van Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure2.png)

4. Voer in de sectie SSO Configuration de volgende stappen uit:

    ![Controleer de configuratie van Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure3.png)

    a. Voer in het tekstvak **Account ID** de bedrijfsnaam in. Deze waarde moet worden gebruikt in de URL **Beantwoorden** en **Aanmelden** die wordt vermeld in de sectie **BasisSAML-configuratie** van azure-portal.

    b. Plak in het tekstvak **Uitgever** de waarde van **Azure AD-id**, die u hebt gekopieerd, vorm de Azure-portal.

    c. Plak in het tekstvak **Idp endpoint url** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Open in Kladblok het met Base64 gecodeerde certificaat dat u hebt gedownload, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **X.509 certificate**.

    e. Klik op **Opslaan**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Testgebruiker Check Point CloudGuard Dome9 maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij Check Point CloudGuard Dome9 Arc, moeten ze worden ingericht in toepassing. Check Point CloudGuard Dome9 Arc ondersteunt just-in-time provisioning, maar om dat goed te laten werken, moet de gebruiker een bepaalde **rol** selecteren en hetzelfde toewijzen aan de gebruiker.

   >[!Note]
   >Neem voor het maken **van rollen** en andere details contact op met [het Ondersteuningsteam van Check Point CloudGuard Dome9 Arc.](mailto:Dome9@checkpoint.com)

**Voor het handmatig inrichten van een gebruikersaccount moet u de volgende stappen uitvoeren:**

1. Meld u aan bij uw bedrijf Check Point CloudGuard Dome9 Arc als beheerder.

2. Klik op **Users & Roles** en vervolgens op **Users**.

    ![Werknemer toevoegen](./media/dome9arc-tutorial/user1.png)

3. Klik op **ADD USER**.

    ![Werknemer toevoegen](./media/dome9arc-tutorial/user2.png)

4. Voer in de sectie **Create User** de volgende stappen uit:

    ![Werknemer toevoegen](./media/dome9arc-tutorial/user3.png)

    a. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld B.Simon@contoso.com.

    b. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker zoals B.

    c. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, bijvoorbeeld Simon.

    d. Zet **SSO User** op **On**.

    e. Klik **op MAKEN**.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de arc-tegel Check Point CloudGuard Dome9 in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Check Point CloudGuard Dome9 Arc waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Check Point CloudGuard Dome9 Arc met Azure AD](https://aad.portal.azure.com/)