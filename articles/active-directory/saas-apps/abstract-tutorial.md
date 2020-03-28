---
title: 'Zelfstudie: Azure Active Directory-integratie met Abstract | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Abstract.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb167ab-d769-4661-a8cb-ae371cb63d2a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81886fa9165269d89bde8306c5829be41952c190
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68302589"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Zelfstudie: Abstract integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Abstract integreren met Azure Active Directory (Azure AD). Wanneer u Abstract integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Abstract.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Abstract met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Abstract single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Abstract ondersteunt **SP en IDP** geïnitieerd SSO

## <a name="adding-abstract-from-the-gallery"></a>Abstract toevoegen uit de galerie

Als u de integratie van Abstract in Azure AD wilt configureren, moet u Abstract uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ Abstract in het zoekvak in de sectie Toevoegen in de sectie **Toevoegen** in de **galerie.**
1. Selecteer **Abstract** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Abstract met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Abstract.

Als u Azure AD SSO met Abstract wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Abstract SSO configureren](#configure-abstract-sso)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Abstracte testgebruiker maken](#create-abstract-test-user)** - om een tegenhanger van Britta Simon in Abstract te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Abstracte** toepassingsintegratie de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd in de **idp-gestarte** modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://app.abstract.com/signin`

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Abstract SSO configureren

Zorg ervoor dat `App Federation Metadata Url` u `Azure AD Identifier` deze en de azure-portal ophaalt, omdat u deze nodig hebt om SSO op Abstract te configureren.

U vindt deze informatie op de pagina **Eén aanmelding instellen met SAML:**

* De `App Federation Metadata Url` is gelegen in de **SAML Signing Certificate** sectie.
* De `Azure AD Identifier` is gelegen in de **sectie Abstract instellen.**


U bent nu klaar om SSO te configureren op Abstract:

>[!Note]
>U moet zich verifiëren met een organisatiebeheerderaccount om toegang te krijgen tot de SSO-instellingen op Abstract.

1. Open de [abstracte web-app](https://app.abstract.com/).
2. Ga naar de pagina **Machtigingen** in de linkerbalk.
3. Voer in de sectie **SSO configureren** de URL met agegevens en **entiteits-id** in. **Entity ID**
4. Voer eventuele handmatige uitzonderingen in die u mogelijk hebt. E-mails die in de sectie handmatige uitzonderingen worden vermeld, omzeilen SSO en kunnen met e-mail en wachtwoord inloggen. 
5. Klik **op Wijzigingen opslaan**.

>[!Note] 
>U moet primaire e-mailadressen gebruiken in de lijst met handmatige uitzonderingen. SSO-activering mislukt als de e-mail die u opneemt de secundaire e-mail van een gebruiker is. Als dat gebeurt, ziet u een foutbericht met de primaire e-mail voor het falende account. Voeg die primaire e-mail toe aan de lijst met handmatige uitzonderingen nadat u hebt geverifieerd dat u de gebruiker kent.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Abstract.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Abstract**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-abstract-test-user"></a>Abstracte testgebruiker maken

SSO testen op Abstract:

1. Open de [abstracte web-app](https://app.abstract.com/).
2. Ga naar de pagina **Machtigingen** in de linkerbalk.
3. Klik **op Testen met mijn account**. Als de test mislukt, neem dan [contact op met ons ondersteuningsteam.](https://www.abstract.com/help/contact/)

>[!Note]
>U moet zich verifiëren met een organisatiebeheerderaccount om toegang te krijgen tot de SSO-instellingen op Abstract.
Dit organisatiebeheeraccount moet worden toegewezen aan Abstract op de Azure-portal.

### <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Abstract in het access-paneel klikt, moet u automatisch worden aangemeld bij de Samenvatting waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

