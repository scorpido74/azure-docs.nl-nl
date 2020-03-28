---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met IQNavigator VMS | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en IQNavigator VMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f61681fbe3ec93467db67290fde6548ce62425fc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75560457"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iqnavigator-vms"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met IQNavigator VMS

In deze zelfstudie leert u hoe u IQNavigator VMS integreert met Azure Active Directory (Azure AD). Wanneer u IQNavigator VMS integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot IQNavigator VMS.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij IQNavigator VMS met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* IQNavigator VMS single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.


* IQNavigator VMS ondersteunt **IDP** geïnitieerde SSO

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.



## <a name="adding-iqnavigator-vms-from-the-gallery"></a>IQNavigator VMS toevoegen vanuit de galerie

Als u de integratie van IQNavigator VMS in Azure AD wilt configureren, moet u IQNavigator VMS uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ IN de sectie **Toevoegen in de galerie** HET TYPE **IQNavigator VMS** in het zoekvak.
1. Selecteer **IQNavigator VMS** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-iqnavigator-vms"></a>Azure AD-aanmelding voor VMS IQNavigator configureren en testen

Azure AD SSO configureren en testen met VMS van IQNavigator met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in IQNavigator VMS.

Als u Azure AD SSO wilt configureren en testen met VMVmS van IQNavigator, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[CONFIGUREER IQNavigator VMS SSO](#configure-iqnavigator-vms-sso)** - om de instellingen voor één aanmelding aan toepassingszijde te configureren.
    1. **[IQNavigator VMS-testgebruiker maken](#create-iqnavigator-vms-test-user)** - om een tegenhanger van B.Simon in IQNavigator VMS te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **IQNavigator VMS-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. Typ een URL in het tekstvak **Id:**`iqn.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

    c. Klik op **Extra URL's instellen**.

    d. In het tekstvak **Relaystatus** typt u een URL met de volgende notatie: `https://<subdomain>.iqnavigator.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke antwoord-URL en relaystatus. Neem contact op met [het VMS-ondersteuningsteam](https://www.beeline.com/support-iqn/) van IQNavigator om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. IQNavigator-toepassing verwacht de waarde unieke gebruikers-id in de claim Name Identifier. De klant kan de juiste waarde voor de claim voor naam-id toewijzen. In dit geval hebben we de gebruiker in kaart gebracht. UserPrincipalName voor het demodoel. Maar volgens uw organisatie-instellingen moet u de juiste waarde voor het in kaart brengen.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot IQNavigator VMS.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer IN de lijst met toepassingen de optie **IQNavigator VMS**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-iqnavigator-vms-sso"></a>IQNavigator VMS SSO configureren

Als u de **VMS-kant** van IQNavigator wilt configureren, moet u de url van de **app-federatie-metagegevens** verzenden naar [het VMS-ondersteuningsteam van IQNavigator.](https://www.beeline.com/support-iqn/) Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-iqnavigator-vms-test-user"></a>IQNavigator VMS-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in IQNavigator VMS. Werk samen met [het VMS-ondersteuningsteam](https://www.beeline.com/support-iqn/) van IQNavigator om de gebruikers toe te voegen in het IQNavigator VMS-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de IQNavigator VMS-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de VMS van IQNavigator waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [IQNavigator VMS met Azure AD uitproberen](https://aad.portal.azure.com/)

