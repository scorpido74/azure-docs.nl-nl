---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met Firmex VDR | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Firmex VDR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 670ff192-c23e-49e4-8fd1-516e02d8856c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdfb857d3a68081fda84aef33e6b5a4b4d1bce28
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76761236"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Firmex VDR

In deze zelfstudie leert u hoe u Firmex VDR integreren met Azure Active Directory (Azure AD). Wanneer u Firmex VDR integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Firmex VDR.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Firmex VDR met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Met Firmex VDR single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Firmex VDR ondersteunt **SP en IDP** geïnitieerd sso

* Zodra u de Firmex configureert, u sessiecontroles afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebesturingselementen zijn van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-firmex-vdr-from-the-gallery"></a>Firmex VDR toevoegen vanuit de galerie

Als u de integratie van Firmex VDR in Azure AD wilt configureren, moet u Firmex VDR uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Firmex VDR** in het zoekvak in de sectie **Toevoegen van de galerie.**
1. Selecteer **Firmex VDR** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>Azure AD-aanmelding voor Firmex VDR configureren en testen

Azure AD SSO configureren en testen met Firmex VDR met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Firmex VDR.

Als u Azure AD SSO wilt configureren en testen met Firmex VDR, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Firmex VDR SSO](#configure-firmex-vdr-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak Firmex VDR-testgebruiker](#create-firmex-vdr-test-user)** - om een tegenhanger van B.Simon in Firmex VDR te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de **integratiepagina van Firmex VDR-toepassingen** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ de URL in het tekstvak **AANmeldings-URL:**`https://login.firmex.com`

1. Klik op **Opslaan**.

1. Firmex VDR-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, Firmex VDR applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name | Bronkenmerk|
    | ------------ | --------- |
    | e-mail | user.mail |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **federatiemetagegevens** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Firmex VDR** instellen de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Firmex VDR.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Firmex VDR**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-firmex-vdr-sso"></a>Firmex VDR SSO configureren

### <a name="before-you-get-started"></a>Voordat u aan de slag gaat

#### <a name="what-youll-need"></a>Wat je nodig hebt

-   Een actief Firmex-abonnement
-   Azure AD als Uw SSO-service
-   Uw IT-beheerder om SSO te configureren
-   Zodra SSO is ingeschakeld, moeten alle gebruikers van uw bedrijf inloggen op Firmex met behulp van SSO en geen login/wachtwoord gebruiken.

#### <a name="how-long-will-this-take"></a>Hoe lang zal dit duren?

Het implementeren van SSO duurt enkele minuten. Er is vrijwel geen downtime tussen Firmex Support waardoor SSO voor uw site, en gebruikers van uw bedrijf authenticating met behulp van SSO. Volg de onderstaande stappen.

### <a name="step-1---identify-your-companys-domains"></a>Stap 1 - De domeinen van uw bedrijf identificeren

Identificeer de domeinen waarmee de gebruikers van uw bedrijf inloggen.

Bijvoorbeeld:

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>Stap 2 - Neem contact op met Firmex Support met uw domeinen

[E-mail Firmex Support Team](mailto:support@firmex.com) of bel 1888 688 4042 x.11 om te spreken met Firmex Support. Geef uw domeingegevens door. Firmex Support voegt de domeinen toe aan uw VDR als **geclaimde domeinen.** Uw beheerder moet nu SSO configureren.

Waarschuwing: Totdat uw sitebeheerder de geclaimde domeinen configureert, kunnen de gebruikers van uw bedrijf zich niet aanmelden bij de VDR. Niet-bedrijfsgebruikers (dat wil zeggen gastgebruikers) kunnen nog steeds inloggen met hun e-mailadres/wachtwoord. Configuratie duurt een paar minuten.

### <a name="step-3---configure-the-claimed-domains"></a>Stap 3 - De geclaimde domeinen configureren

1. Log in bij Firmex als sitebeheerder.
1. Klik linksboven op uw bedrijfslogo.
1. Selecteer het tabblad **SSO.** Selecteer vervolgens **SSO-configuratie**. Klik op het domein dat u wilt configureren.

    ![Geclaimde domeinen](./media/firmex-vdr-tutorial/edit-sso.png)  

1. Laat uw IT-beheerder de volgende velden invullen. De velden moeten worden overgenomen van uw identiteitsprovider:  

    ![SSO-configuratie](./media/firmex-vdr-tutorial/SSO-config.png)

    a. Plak in het tekstvak **Entiteit-id** de azure **AD-id-waarde** die u hebt gekopieerd van de Azure-portal.

    b. Plak in het **tekstvak URL van de identiteitsprovider** de **URL-waarde aanmelding,** die u hebt gekopieerd van de Azure-portal.

    c. **Certificaat van openbare sleutels** - Voor verificatiedoeleinden kan een SAML-bericht digitaal worden ondertekend door de uitgever. Om de handtekening op het bericht te verifiëren, gebruikt de berichtontvanger een openbare sleutel waarvan bekend is dat deze bij de uitgever hoort. Om een bericht te versleutelen, moet ook een openbare encryptiesleutel van de uiteindelijke ontvanger bekend zijn bij de uitgever. In beide situaties , ondertekenen en versleuteling, moeten vertrouwde openbare sleutels vooraf worden gedeeld.  Dit is het **X509Certificate** van **Xml met metagegevens** van De Federatie

    d. Klik **op Opslaan** om de SSO-configuratie te voltooien. Wijzigingen worden onmiddellijk van kracht.

1. Op dit moment is SSO ingeschakeld voor uw site.

### <a name="create-firmex-vdr-test-user"></a>Firmex VDR-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd B.Simon in Firmex. Werk samen met [Firmex Support Team](mailto:support@firmex.com) om de gebruikers toe te voegen aan het Firmex-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Firmex VDR-tegel in het Access Panel klikt, moet u automatisch worden aangemeld bij de Firmex VDR waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Firmex VDR met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hoe firmex te beschermen met geavanceerde zichtbaarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
