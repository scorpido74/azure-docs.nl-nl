---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met Coda | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Coda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: acaf2012-ef2e-4ce0-8467-ceece3bae50e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74da278dbbc0ac32407c345524e224ca5f7616da
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77194598"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Coda

In deze zelfstudie leert u hoe u Coda integreert met Azure Active Directory (Azure AD). Wanneer u Coda integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Coda.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Coda met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Coda single sign-on (SSO) ingeschakeld abonnement (Enterprise) met GDrive integratie uitgeschakeld. Neem contact op met [het ondersteuningsteam van Coda](mailto:support@coda.io) om gdrive-integratie voor uw organisatie uit te schakelen als dit momenteel is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Coda ondersteunt **IDP** geïnitieerde SSO

* Coda ondersteunt **Just In Time** gebruikersinrichting

* Zodra u Coda hebt geconfigureerd, u sessiebesturingselementen afdwingen die exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebesturingselementen zijn van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-coda-from-the-gallery"></a>Coda toevoegen uit de galerie

Als u de integratie van Coda in Azure AD wilt configureren, moet u Coda uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Coda** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Coda** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Azure AD-aanmelding voor Coda configureren en testen

Azure AD SSO configureren en testen met Coda met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Coda.

Als u Azure AD SSO met Coda wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Begin met de configuratie van Coda SSO](#begin-configuration-of-coda-sso)** - om te beginnen met de configuratie van SSO in Coda.
1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
   * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
   * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Coda SSO](#configure-coda-sso)** - om de configuratie van enkele aanmeldingsinstellingen in Coda te voltooien.
   * **[Coda-testgebruiker maken](#create-coda-test-user)** - om een tegenhanger van B.Simon in Coda te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="begin-configuration-of-coda-sso"></a>Configuratie van Coda SSO starten

Volg deze stappen in Coda om te beginnen.

1. Open in Coda het deelvenster **Organisatie-instellingen.**

   ![Organisatie-instellingen openen](media/coda-tutorial/org-settings.png)

1. Controleer of uw organisatie GDrive-integratie heeft uitgeschakeld. Als dit momenteel is ingeschakeld, neemt u contact op met het [coda-ondersteuningsteam](mailto:support@coda.io) om u te helpen bij het migreren van GDrive.

   ![GDrive uitgeschakeld](media/coda-tutorial/gdrive-off.png)

1. Selecteer **onder Verifiëren met SSO (SAML)** de optie **SAML configureren.**

   ![Saml-instellingen](media/coda-tutorial/saml-settings-link.png)

1. Let op de waarden voor **Entiteit-id** en **SAML-antwoord-URL,** die u in de volgende stappen nodig hebt.

   ![Url van entiteits-id en SAML-antwoord die moet worden gebruikt in Azure](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Coda-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **Eén aanmelding instellen met SAML** de waarden in voor de volgende velden:

   a. Voer **in** het tekstvak Id de 'Entiteits-id' van bovenaf in. Het moet het patroon volgen:`https://coda.io/samlId/<CUSTOMID>`

   b. Voer in het tekstvak **Antwoord-URL** de URL 'SAML-antwoord' van bovenaf in. Het moet het patroon volgen:`https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > Uw waarden zullen afwijken van het bovenstaande; u uw waarden vinden in coda's Configure SAML console. Werk deze waarden bij met de werkelijke id en antwoord-URL.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Coda instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Coda.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Coda**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

   ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-coda-sso"></a>Coda SSO configureren

Als u de installatie wilt voltooien, voert u waarden uit Azure Active Directory in het deelvenster **Saml van** Coda configureren in.

1. Open in Coda het deelvenster **Organisatie-instellingen.**
1. Selecteer **onder Verifiëren met SSO (SAML)** de optie **SAML configureren.**
1. **SAML-provider** instellen op **Azure Active Directory**.
1. Plak in **de url van aanmeldingvan identiteitsprovider**de **aanmeldings-URL** van de Azure-console.
1. Plak in **De uitgever van de identiteitsprovider**de Azure **AD-id** van de Azure-console.
1. Selecteer **in het openbaar certificaat van de identiteitsprovider**de optie Certificaat **uploaden** en selecteer het certificaatbestand dat u eerder hebt gedownload.
1. Selecteer **Opslaan**.

Hiermee wordt het werk voltooid dat nodig is voor de SAML SSO-verbinding.

### <a name="create-coda-test-user"></a>Coda-testgebruiker maken

In deze sectie wordt een gebruiker genaamd Britta Simon gemaakt in Coda. Coda ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Coda, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de coda-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Coda waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Coda met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Coda beschermen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
