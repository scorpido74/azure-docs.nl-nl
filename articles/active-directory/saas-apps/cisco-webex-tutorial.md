---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Cisco Webex Meetings | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Cisco Webex-vergaderingen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4e497c556bde1be4e498cd85a68282a0e3b2666
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026261"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Cisco Webex Meetings

In deze zelfstudie leert u hoe u Cisco Webex-vergaderingen integreren met Azure Active Directory (Azure AD). Wanneer u Cisco Webex-vergaderingen integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Cisco Webex-vergaderingen.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Cisco Webex-vergaderingen met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Cisco Webex Meetings single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Cisco Webex Meetings ondersteunt **SP en IDP** geïnitieerde SSO

* Cisco Webex Meetings ondersteunt **Just In Time** gebruikersinrichting

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Cisco Webex-vergaderingen toevoegen vanuit de galerie

Als u de integratie van Cisco Webex-vergaderingen in Azure AD wilt configureren, moet u Cisco Webex-vergaderingen vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Cisco Webex-vergaderingen** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Cisco Webex-vergaderingen** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Azure AD-aanmelding voor Cisco Webex-vergaderingen configureren en testen

Azure AD SSO configureren en testen met Cisco Webex Meetings met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Cisco Webex-vergaderingen.

Als u Azure AD SSO wilt configureren en testen met Cisco Webex-vergaderingen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
2. **[Cisco Webex Meetings SSO configureren](#configure-cisco-webex-meetings-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Cisco Webex Meetings-testgebruiker maken](#create-cisco-webex-meetings-test-user)** - om een tegenhanger van B.Simon te hebben in Cisco Webex-vergaderingen die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Cisco Webex** Meetings-toepassingsintegratie de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Op de pagina **Eén aanmelding instellen met SAML** u de toepassing in de **IDP-modus** configureren door het **metagegevensbestand van** de serviceprovider als volgt te uploaden:

    a. Klik op **Metagegevensbestand uploaden**.

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    c. Na het succesvol voltooien van het uploaden van metagegevens van serviceproviders worden de waarden **Id-** en **Antwoord-URL** automatisch ingevuld in de sectie **BasisSAML-configuratie.**

    >[!Note]
    >U krijgt het bestand met metagegevens van serviceprovider configureren van **Cisco Webex Meetings SSO,** dat later in de zelfstudie wordt uitgelegd. 

1. Als u de toepassing in de door **SP** gestarte modus wilt configureren, voert u de volgende stappen uit:  

    a. Klik in de sectie **BasisSAML-configuratie** op het pictogram bewerken/pen.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)
    
    b. Typ de URL in het tekstvak **Aantekenen op URL** met het volgende patroon:` https://<customername>.my.webex.com`

5. Cisco Webex Meetings-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![installatiekopie](common/edit-attribute.png)

6. Naast bovenstaande, Cisco Webex Meetings applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie. In de sectie Gebruikersclaims in het dialoogvenster Gebruikerskenmerken voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel: 

    | Name | Bronkenmerk|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   e-mail       | user.mail |
    |   uid    | user.mail |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Selecteer in de lijst **Bronkenmerk** de kenmerkwaarde die voor die rij wordt weergegeven in de vervolgkeuzelijst.

    f. Klik op **Opslaan**.

4. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Cisco Webex-vergaderingen instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Cisco Webex Meetings.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Cisco Webex Meetings**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-cisco-webex-meetings-sso"></a>Cisco Webex Meetings SSO configureren

1. Ga `https://<customername>.webex.com/admin` naar URL met uw administratiereferenties.

2. Ga naar **Algemene site-instellingen** en navigeer naar **SSO-configuratie**.
 
    ![Eenmalige aanmelding configureren](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. Voer op de pagina **Webex Beheer** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. selecteer **SAML 2.0** als **Federatieprotocol**.

    b. Klik op **SAML-koppeling importeren** om het metagegevensbestand te uploaden dat u hebt gedownload van de Azure-portal.

    c. Klik op de knop **Exporteren** om het bestand met metagegevens van serviceproviders te downloaden en te uploaden in de sectie **BasisSAML-configuratie** op Azure-portal.

    d. Typ `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` en als u de MFA met Azure AD wilt inschakelen in het tekstvak **AuthContextContextClassRef,** zoals`urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Selecteer **Automatisch aanmaken van een account**.

    >[!NOTE]
    >Als u **just-in-time** gebruikersinrichting wilt inschakelen, moet u de **instelling van een automatisch account**controleren. Naast dat SAML token attributen moeten worden doorgegeven in de SAML reactie.

    f. Klik op **Opslaan**.

    >[!NOTE]
    >Deze configuratie is alleen bedoeld voor klanten die Webex UserID gebruiken in e-mailindeling.

### <a name="create-cisco-webex-meetings-test-user"></a>Testgebruiker Cisco Webex-vergaderingen maken

Het doel van deze sectie is het creëren van een gebruiker genaamd B.Simon in Cisco Webex Meetings. Cisco Webex Meetings ondersteunt **just-in-time** provisioning, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Cisco Webex Meetings, wordt er een nieuwe gemaakt wanneer u probeert toegang te krijgen tot Cisco Webex Meetings.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Cisco Webex-vergaderingen in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Cisco Webex-vergaderingen waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [ServiceNow uitproberen met Azure AD](https://aad.portal.azure.com)
