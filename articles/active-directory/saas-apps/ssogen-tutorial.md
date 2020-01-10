---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE.
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
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 841c2b7067427b703d2aa7647e0c0c3ef2287c6e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775675"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE

In deze zelf studie leert u hoe u SSOGEN-Azure AD SSO-gateway integreert voor Oracle E-Business Suite-EBS, People Soft en JDE met Azure Active Directory (Azure AD). Wanneer u SSOGEN-Azure AD SSO-gateway integreert voor Oracle E-Business Suite-EBS, People Soft en JDE met Azure AD, kunt u het volgende doen:

* Beheer in azure AD die toegang heeft tot SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* SSOGEN: Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE SSO (single sign-on) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE ondersteunt door **SP en IDP** GEÏNITIEERDe SSO

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>SSOGEN toevoegen: Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE uit de galerie

Als u de integratie van SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE wilt configureren in azure AD, moet u SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE van de galerie toevoegen aan uw lijst met beheerde S aaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE** in het zoekvak.
1. Selecteer **SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE** van het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE

Azure AD SSO configureren en testen met SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE.

Als u Azure AD SSO wilt configureren en testen met SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CONFIGUREER SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE SSO](#configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    * **[Maak SSOGEN Azure AD SSO-gateway voor Oracle e Business Suite EBS, People Soft en JDE test User](#create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user)** -om een equivalent van B. Simon te hebben in SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE, die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Neem contact op met [SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE client support team](mailto:support@ssogen.com) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Uw SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE-toepassingen verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE-toepassing verwacht **nameidentifier** te worden toegewezen aan **User. onpremisessamaccountname**, dus u moet de kenmerk toewijzing bewerken door op het pictogram **bewerken** te klikken en de kenmerk toewijzing te wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op de knop kopiëren om de URL van de **app Federation-meta gegevens** te kopiëren en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen **SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso"></a>SSOGEN Azure AD SSO-gateway configureren voor Oracle E Business Suite EBS, People Soft en JDE SSO

Als u eenmalige aanmelding wilt configureren op **SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE** , raadpleegt u de volgende informatie over de SSO-registratie van de toepassing:

* Oracle EBS-Azure AD SSO-integratie: [https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* People Soft-Azure AD SSO-integratie: [https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards-Azure AD SSO-integratie: [https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Apache-Azure AD SSO-integratie: [https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user"></a>SSOGEN Azure AD SSO-gateway maken voor Oracle E Business Suite EBS, People Soft en JDE test User

Azure AD verzendt een unieke gebruikers-id (naam-ID) naar de gebruikers toepassing nadat de verificatie is geslaagd.  Zorg ervoor dat de unieke gebruikers-id (naam-ID) overeenkomt met de gebruikers record in uw toepassing FND_USER. USER_NAME in Oracle EBS bijvoorbeeld.

Neem contact op met [info@ssogen.com](mailto:info@ssogen.com) en [support@ssogen.com](mailto:support@ssogen.com) voor ondersteuning.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE in het toegangs venster klikt, moet u automatisch worden aangemeld bij de SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE waarvoor u s et-SSO. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE met Azure AD](https://aad.portal.azure.com/)