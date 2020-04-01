---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f4311031-5a4b-468e-be58-324d06220869
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74b56e30eb8851913f91cbee377204a575311375
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76293671"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE

In deze zelfstudie leert u hoe u SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE integreert met Azure Active Directory (Azure AD). Wanneer u SSOGEN - Azure AD SSO Gateway voor Oracle E-Business Suite - EBS, PeopleSoft en JDE integreert met Azure AD, u:

* Beheer in Azure AD die toegang heeft tot SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE ondersteunt **SP en IDP** die SSO hebben geïnitieerd.
* Zodra u de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE hebt geconfigureerd, u sessiecontroles afdwingen, die exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebesturingselementen zijn van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE vanuit de galerie

Om de integratie van SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE in Azure AD te configureren, moet u SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE** in de sectie **Toevoegen in de galerie** in het zoekvak.
1. Selecteer **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE** uit het resultatenpaneel en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Azure AD-singlesign-aan configureren en testen voor SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE

Azure AD SSO configureren en testen met SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE.

Als u Azure AD SSO wilt configureren en testen met SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE, u de volgende bouwstenen voltooien:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[SSOGEN Azure AD SSO Gateway configureren voor Oracle E Business Suite EBS, PeopleSoft en JDE SSO](#configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso)** - om de instellingen voor één aanmelding aan toepassingszijde te configureren.
    * **[Maak SSOGEN Azure AD SSO Gateway voor Oracle E Business Suite EBS, PeopleSoft en JDE-testgebruiker](#create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user)** - om een tegenhanger van B.Simon in SSOGEN te hebben - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS-, PeopleSoft- en** JDE-toepassingsintegratiepagina de sectie **Beheer** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    Typ in het tekstvak **URL beantwoorden** een URL met het volgende patroon:`https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Neem contact op met [SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE Client support team](mailto:support@ssogen.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Uw SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste attribuuttoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE application expects **nameidentifier** to be mapped with **user.onpremisessamaccountname**, so you need to beed the attribute mapping by clicking on **Edit** icon and change the attribute mapping.

    ![installatiekopie](common/edit-attribute.png)

1. Klik op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** op de knop Kopiëren om **de url van de appfederatie-metagegevens** te kopiëren en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie u B.Simon azure single sign-on gebruiken door toegang te verlenen tot SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer In de lijst met toepassingen de optie **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso"></a>SSOGEN Azure AD SSO Gateway configureren voor Oracle E Business Suite EBS, PeopleSoft en JDE SSO

Als u eenmalige aanmelding wilt configureren op **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE-kant,** vindt u hieronder onderstaande toepassingsspecifieke SSO-registratiedocumentatie:

* Oracle EBS - Azure AD SSO-integratie:[https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft - Azure AD SSO-integratie:[https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards - Azure AD SSO-integratie:[https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Apache - Azure AD SSO-integratie:[https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user"></a>SSOGEN Azure AD SSO Gateway voor Oracle E Business Suite EBS, PeopleSoft en JDE-testgebruiker

Azure AD stuurt unieke gebruikers-id (naam-id) naar de gebruikerstoepassing, nadat de verificatie is geslaagd.  Zorg ervoor dat de unieke gebruikersnaam (naam-id) overeenkomt met de gebruikersrecord in uw toepassing, FND_USER. USER_NAME in Oracle EBS bijvoorbeeld.

Neem [info@ssogen.com](mailto:info@ssogen.com) contact [support@ssogen.com](mailto:support@ssogen.com) op en voor alle ondersteuning.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het access panel op de tegel SSOGEN - Azure AD SSO Gateway voor Oracle E-Business Suite - EBS, PeopleSoft en JDE klikt, moet u automatisch worden aangemeld bij de SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft en JDE beschermen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
