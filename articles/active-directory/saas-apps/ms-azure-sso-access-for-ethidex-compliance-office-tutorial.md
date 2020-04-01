---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met MS Azure SSO Access voor Ethidex Compliance Office™ | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en MS Azure SSO Access voor Ethidex Compliance Office™.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58b2e70f-d1dd-47b6-b91f-f77581df01c6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00cbb5ff4b9354d0d1702161460b2646669f34c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "70844365"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met MS Azure SSO Access voor Ethidex Compliance Office™

In deze zelfstudie leert u hoe u MS Azure SSO Access voor Ethidex Compliance Office ™ integreert met Azure AD (Azure AD). Wanneer u MS Azure SSO Access voor Ethidex Compliance Officeintegreert™ met Azure AD, u:

* Beheer in Azure AD die toegang heeft tot MS Azure SSO Access voor Ethidex Compliance Office™.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij MS Azure SSO Access voor Ethidex Compliance Office™ met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* MS Azure SSO Access for Ethidex Compliance Office™ single sign-on (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* MS Azure SSO Access voor Ethidex Compliance Office™ ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>MS Azure SSO Access toevoegen voor Ethidex Compliance Office™ vanuit de galerie

Als u de integratie van MS Azure SSO Access voor Ethidex Compliance Office™ in Azure AD wilt configureren, moet u MS Azure SSO Access voor Ethidex Compliance Office™ vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **MS Azure SSO Access voor Ethidex Compliance Officein™** in de sectie **Toevoegen vanuit de galeriein** het zoekvak.
1. Selecteer **MS Azure SSO Access voor Ethidex Compliance Office™** van het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Azure AD-singlesign-aan configureren en testen voor MS Azure SSO Access voor Ethidex Compliance Office™

Azure AD SSO configureren en testen met MS Azure SSO Access voor Ethidex Compliance Office™ met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in MS Azure SSO Access voor Ethidex Compliance Office™.

Als u Azure AD SSO wilt configureren en testen met MS Azure SSO Access voor Ethidex Compliance Office™ de volgende bouwstenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Ms Azure SSO Access configureren voor Ethidex Compliance Office SSO](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** - om de instellingen voor één aanmelding aan toepassingszijde te configureren.
    1. **[Ms Azure SSO Access maken voor Ethidex Compliance Office-testgebruiker](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** - om een tegenhanger van B.Simon te hebben in MS Azure SSO Access voor Ethidex Compliance Office™ dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **MS Azure SSO Access for Ethidex Compliance Office™** toepassingsintegratiepagina, zoek de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`com.ethidex.prod.<CLIENTID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.ethidex.com/saml2/sp/acs/<CLIENTID>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met [MS Azure SSO Access voor Ethidex Compliance Office™ ondersteuningsteam](mailto:support@ethidex.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. MS Azure SSO Access for Ethidex Compliance Office™ applicatie toepassing verwacht dat de SAML-beweringen in een specifieke indeling zijn, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. MS Azure SSO Access for Ethidex Compliance Office™ toepassing verwacht **dat naamid** wordt toegewezen aan **user.mail,** dus u moet de kenmerktoewijzing bewerken door op pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

1. Kopieer in de sectie **MS Azure SSO Access for Ethidex Compliance Office™** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon azure single sign-on gebruiken door toegang te verlenen tot MS Azure SSO Access voor Ethidex Compliance Office™.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **MS Azure SSO Access voor Ethidex Compliance Office™**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>MS Azure SSO Access configureren voor Ethidex Compliance Office SSO

Als u eenmalige aanmelding wilt configureren op **MS Azure SSO Access voor Ethidex Compliance Office™** kant, moet u het gedownloade **certificaat (Raw)** en de juiste gekopieerde URL's van Azure portal naar [MS Azure SSO Access voor Ethidex Compliance Office ™ ondersteuningsteam](mailto:support@ethidex.com)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>Ms Azure SSO Access maken voor ethidex Compliance Office-testgebruiker

In deze sectie maakt u een gebruiker genaamd B.Simon in MS Azure SSO Access voor Ethidex Compliance Office™. Werk samen met [MS Azure SSO Access voor Ethidex Compliance Office™ ondersteuningsteam](mailto:support@ethidex.com) om de gebruikers toe te voegen in het MS Azure SSO Access voor Ethidex Compliance Office™ platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel MS Azure SSO Access voor Ethidex Compliance Office klikt™ in het access panel, moet u automatisch worden aangemeld bij het MS Azure SSO Access for Ethidex Compliance Office™ waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer MS Azure SSO Access voor Ethidex Compliance Office™ met Azure AD](https://aad.portal.azure.com/)

