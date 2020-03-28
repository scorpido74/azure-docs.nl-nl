---
title: 'Zelfstudie: Azure Active Directory-integratie met ProNovos Ops Manager | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en ProNovos Ops Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6eb0780c-ff52-464f-8998-e24ea6bbf600
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95c898dd36387d4474f5129943c6bd76f754ffe2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "70213527"
---
# <a name="tutorial-integrate-pronovos-ops-manager-with-azure-active-directory"></a>Zelfstudie: ProNovos Ops Manager integreren met Azure Active Directory

In deze zelfstudie leert u hoe u ProNovos Ops Manager integreert met Azure Active Directory (Azure AD). Wanneer u ProNovos Ops Manager integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot ProNovos Ops Manager.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij ProNovos Ops Manager met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* ProNovos Ops Manager single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* ProNovos Ops Manager ondersteunt **SP en IDP** geïnitieerd sso

## <a name="adding-pronovos-ops-manager-from-the-gallery"></a>ProNovos Ops Manager toevoegen vanuit de galerie

Als u de integratie van ProNovos Ops Manager in Azure AD wilt configureren, moet u ProNovos Ops Manager vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **ProNovos Ops Manager** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **ProNovos Ops Manager** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met ProNovos Ops Manager met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in ProNovos Ops Manager.

Als u Azure AD SSO wilt configureren en testen met ProNovos Ops Manager, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer ProNovos Ops Manager SSO](#configure-pronovos-ops-manager-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Maak de testgebruiker van ProNovos Ops Manager](#create-pronovos-ops-manager-test-user)** - om een tegenhanger van B.Simon te hebben in ProNovos Ops Manager die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **ProNovos Ops** Manager-toepassingsintegratie de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd in de **idp-gestarte** modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://gly.smartsubz.com/saml2/acs`


4. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

6. Kopieer in de sectie **ProNovos Ops Manager instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-pronovos-ops-manager-sso"></a>ProNovos Ops Manager SSO configureren

Als u eenmalige aanmelding wilt configureren aan de kant van **ProNovos Ops Manager,** moet u het gedownloade **certificaat (Raw)** en de juiste gekopieerde URL's van Azure-portal naar [het ondersteuningsteam van ProNovos Ops Manager](mailto:support@pronovos.com)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.
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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot ProNovos Ops Manager.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **ProNovos Ops Manager**in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-pronovos-ops-manager-test-user"></a>Testgebruiker ProNovos Ops Manager maken

In deze sectie maakt u een gebruiker genaamd B.Simon in ProNovos Ops Manager. Werk samen met [pronovos Ops Manager support team](mailto:support@pronovos.com) om de gebruikers toe te voegen in het ProNovos Ops Manager-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel ProNovos Ops Manager in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de ProNovos Ops Manager waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

