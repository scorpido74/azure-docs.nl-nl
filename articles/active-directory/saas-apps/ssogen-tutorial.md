---
title: 'Zelfstudie: Integratie van eenmalige aanmelding via Azure Active Directory bij SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: 72b91be9875abf07e5f6f828ed32483a5d61eadd
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88524395"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Zelfstudie: Integratie van eenmalige aanmelding via Azure Active Directory bij SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE

In deze zelfstudie integreert u SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE met Azure Active Directory (Azure AD) Wanneer u SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE hebt geïntegreerd met Azure AD, kunt u de volgende zaken doen:

* Bepalen wie toegang heeft tot SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE
* Ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE ondersteunt eenmalige aanmelding met **SP en IDP**.
* Zodra u SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE toevoegen vanuit de galerie

Als u de integratie van SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE met Azure AD wilt configureren moet u SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE** uit het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Eenmalige aanmelding bij SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE via Azure AD configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE configureren](#configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker maken in SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE](#create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user)** : om een tegenhanger voor B.Simon te maken in SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE die is gekoppeld aan de voorstelling van de gebruiker in Azure AD.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE**  de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Neem contact op met [het ondersteuningsteam van SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE](mailto:support@ssogen.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Uw SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE-toepassing verwacht de SAML-asserties in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. De SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE-toepassing verwacht dat **nameidentifier** is toegewezen aan **user.onpremisessamaccountname**, dus u moet de kenmerktoewijzing bewerken door te klikken op het pictogram **Bewerken** en de kenmerktoewijzing wijzigen.

    ![image](common/edit-attribute.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso"></a>Eenmalige aanmelding bij SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE configureren

Als u eenmalige aanmelding aan de zijde van **SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE** wilt configureren, raadpleegt u de onderstaande toepassingsspecifieke registratiedocumentatie voor eenmalige aanmelding:

* Integratie van eenmalige aanmelding bij Oracle EBS - Azure AD: [https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* Integratie van eenmalige aanmelding bij PeopleSoft - Azure AD: [https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* Integratie van eenmalige aanmelding bij JD Edwards - Azure AD: [https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Integratie van eenmalige aanmelding bij Apache - Azure AD: [https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user"></a>Een testgebruiker maken in SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE

Azure AD verzendt een unieke gebruikers-id (naam-id) naar de gebruikerstoepassing nadat de verificatie is geslaagd.  Zorg ervoor dat de unieke gebruikers-id (naam-id) overeenkomt met de gebruikersrecord in uw toepassing, zoals FND_USER.USER_NAME in Oracle EBS.

Neem contact op met [info@ssogen.com](mailto:info@ssogen.com) en [support@ssogen.com](mailto:support@ssogen.com) voor ondersteuning.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE klikt, wordt u als het goed is automatisch aangemeld bij het exemplaar van SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE met Azure AD proberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
