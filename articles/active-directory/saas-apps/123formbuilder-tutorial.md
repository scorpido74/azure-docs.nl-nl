---
title: 'Zelfstudie: Azure Active Directory-integratie met 123FormBuilder SSO | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en 123FormBuilder SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 947a9d632089b18f6b950c5eecbcb74d061f32eb
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274212"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met 123FormBuilder SSO

In deze zelfstudie leert u hoe u 123FormBuilder SSO integreert met Azure Active Directory (Azure AD). Wanneer u 123FormBuilder SSO integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot 123FormBuilder SSO.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij 123FormBuilder SSO met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* 123FormBuilder SSO single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* 123FormBuilder SSO ondersteunt **SP en IDP** geïnitieerd SSO.
* 123FormBuilder SSO ondersteunt **Just In Time** gebruikersinrichting.
* Zodra u 123FormBuilder SSO hebt geconfigureerd, u sessiecontrole afdwingen, wat exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermt. Sessiebesturingselement strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-123formbuilder-sso-from-the-gallery"></a>123FormBuilder SSO toevoegen vanuit de galerie

Als u de integratie van 123FormBuilder SSO in Azure AD wilt configureren, moet u 123FormBuilder SSO uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **123FormBuilder SSO** in het zoekvak in de sectie **Toevoegen in de galeriesectie.**
1. Selecteer **123FormBuilder SSO** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-123formbuilder-sso"></a>Azure AD-aanmelding configureren en testen voor 123FormBuilder SSO

Azure AD SSO configureren en testen met 123FormBuilder SSO met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in 123FormBuilder SSO.

Voer de volgende bouwstenen uit om Azure AD SSO te configureren en te testen met 123FormBuilder SSO:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer 123FormBuilder SSO](#configure-123formbuilder-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak 123FormBuilder SSO-testgebruiker](#create-123formbuilder-sso-test-user)** - om een tegenhanger van B.Simon te hebben in 123FormBuilder SSO die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **123FormBuilder SSO-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/metadata`


    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/acs`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL's en id, zoals later in de zelfstudie wordt uitgelegd.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **de optie Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **123FormBuilder SSO instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot 123FormBuilder SSO.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **123FormBuilder SSO**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-123formbuilder-sso"></a>123formbuilder SSO configureren

1. Als u eenmalige aanmelding wilt configureren aan de [https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) **123FormBuilder SSO-kant,** gaat u naar en voert u de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/123formbuilder-tutorial/submit.png) 

    a. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld `B.Simon@Contoso.com`.

    b. Klik op **Upload** en blader naar het gedownloade XML-bestand met metagegevens dat u hebt gedownload vanuit de Azure-portal.

    c. Klik op **SUBMIT FORM**.

2. Voer in **Microsoft Azure AD - Eenmalige aanmelding - App-instellingen configureren** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/123formbuilder-tutorial/url3.png)

    a. Als u de toepassing wilt configureren in de **door IDP geïnitieerde modus**, kopieert u de waarde van **IDENTIFIER** voor uw exemplaar en plakt u deze in het tekstvak **Id** in de sectie **SAML-basisconfiguratie** van de Azure-portal.

    b. Als u de toepassing wilt configureren in de **door IDP geïnitieerde modus**, kopieert u de waarde van **REPLY URL** voor uw exemplaar en plakt u deze in het tekstvak **Antwoord-URL** in de sectie **SAML-basisconfiguratie** van de Azure-portal.

    c. Als u de toepassing wilt configureren in de **door SP geïnitieerde modus**, kopieert u de waarde van **SIGN ON URL** voor uw exemplaar en plakt u deze in het tekstvak **Aanmeldings-URL** in de sectie **SAML-basisconfiguratie** van de Azure-portal.

### <a name="create-123formbuilder-sso-test-user"></a>123FormBuilder SSO-testgebruiker maken

In deze sectie wordt een gebruiker genaamd Britta Simon gemaakt in 123FormBuilder SSO. 123FormBuilder SSO ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in 123FormBuilder SSO, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de 123FormBuilder SSO-tegel in het access-paneel klikt, moet u automatisch worden aangemeld bij de 123FormBuilder SSO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer 123FormBuilder SSO met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
