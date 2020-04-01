---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met ContractSafe Saml2 SSO | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en ContractSafe Saml2 SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9d8c9eba-6a90-4c8f-b387-a6ead4af00af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ab2dc71f39164988e0d229fc994548a00447986
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77185617"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integreren met ContractSafe Saml2 SSO

In deze zelfstudie leert u hoe u ContractSafe Saml2 SSO integreert met Azure Active Directory (Azure AD). Wanneer u ContractSafe Saml2 SSO integreert met Azure AD, u het als:

* Bepaal wie toegang heeft tot ContractSafe Saml2 SSO in Azure AD.
* Stel uw gebruikers in staat om zich automatisch aan te melden bij ContractSafe Saml2 SSO met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory voor](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Een ContractSafe Saml2 SSO-abonnement met SSO ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. ContractSafe Saml2 SSO ondersteunt **IDP**-geïnitieerde SSO.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>ContractSafe Saml2 SSO toevoegen vanuit de galerie

Als u de integratie van ContractSafe Saml2 SSO in Azure AD wilt configureren, moet u ContractSafe Saml2 SSO vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **ContractSafe Saml2 SSO** in het zoekvak in de sectie **Toevoegen vanuit de galerie.**
1. Selecteer **ContractSafe Saml2 SSO** in het resultatenpaneel en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Azure AD SSO configureren en testen voor ContractSafe Saml2 SSO

Azure AD SSO configureren en testen met ContractSafe Saml2 SSO met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in ContractSafe Saml2 SSO.

Als u Azure AD SSO wilt configureren en testen met ContractSafe Saml2 SSO, voert u de volgende bouwstenen uit:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
   * [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om Azure AD SSO te testen met het **B.Simon-account.**
   * [Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user) toe om **B.Simon** in staat te stellen Azure AD SSO te gebruiken.

1. [Configureer ContractSafe Saml2 SSO](#configure-contractsafe-saml2-sso) om de SSO-instellingen aan toepassingszijde te configureren.
   * [Maak een ContractSafe Saml2 SSO-testgebruiker](#create-a-contractsafe-saml2-sso-test-user) om een tegenhanger van **B.Simon** in ContractSafe Saml2 SSO te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
2. [Test SSO](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg de volgende stappen om Azure AD SSO in te schakelen in de Azure-portal:

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **ContractSafe Saml2 SSO-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding.**
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Selecteer op de pagina **Eén aanmelding instellen met SAML** het pictogram bewerken (pen) voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **Eén aanmelding instellen met SAML** de volgende waarden in de bijbehorende velden in:

    a. Voer **in** het tekstvak Id een URL in met de volgende indeling:`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. Voer in het tekstvak **URL beantwoorden** een URL in met de volgende indeling:`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [Ondersteuningsteam van ContractSafe Saml2 SSO Client](mailto:support@contractsafe.com) om deze waarden te krijgen. U ook verwijzen naar de indelingen die worden weergegeven in de sectie **BasisSAML-configuratie** in de Azure-portal.

1. ContractSafe Saml2 SSO verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Algemene standaardkenmerken](common/default-attributes.png)

1. Naast de standaardkenmerken verwacht de ContractSafe Saml2 SSO-toepassing dat er nog een paar kenmerken worden doorgegeven in de SAML-respons. Deze kenmerken zijn vooraf ingevuld, maar u ze bekijken op basis van uw vereisten. In de volgende lijst worden de extra kenmerken weergegeven.

    | Name | Bronkenmerk|
    | ---------------| --------------- |
    | e-mailnaam | user.userprincipalname |
    | e-mail | user.onpremisesuserprincipalname |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de xml **met aalmetagegevens**van federatie . Selecteer **Downloaden** om het certificaat te downloaden en sla het op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **ContractSafe Saml2 SSO** instellen de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd **B.Simon**.

1. Selecteer Azure Active Directory in het linkerdeelvenster in de **Azure-portal**. Selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **Gebruikersnaam** een `username@companydomain.extension` e-mailadres in de indeling in. Een voorbeeld is `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Selecteer **Maken**.

## <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u **B.Simon** Azure SSO laten gebruiken door toegang te verlenen tot ContractSafe Saml2 SSO.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **ContractSafe Saml2 SSO**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **vervolgens Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

   ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst **Gebruikers.** Selecteer vervolgens de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens de knop **Selecteren** onder aan het scherm.
1. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen.**

## <a name="configure-contractsafe-saml2-sso"></a>ContractSafe Saml2 SSO configureren

Als u SSO wilt configureren aan de **Kant van ContractSafe Saml2 SSO,** moet u de gedownloade **Xml met aalmetagegevens** van de Federatie en de juiste gekopieerde URL's van de Azure-portal naar het [Ondersteuningsteam van ContractSafe Saml2 SSO](mailto:support@contractsafe.com)verzenden. Het team is verantwoordelijk voor het goed instellen van de SAML SSO-verbinding aan beide zijden.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>Een ContractSafe Saml2 SSO-testgebruiker maken

Maak een gebruiker genaamd B.Simon in ContractSafe Saml2 SSO. Werk samen met het [ContractSafe Saml2 SSO-ondersteuningsteam](mailto:support@contractsafe.com) om de gebruikers toe te voegen aan het ContractSafe Saml2 SSO-platform. Gebruikers moeten worden gemaakt en geactiveerd voordat u SSO gebruikt.

## <a name="test-sso"></a>Test SSO

Test uw Azure AD SSO-configuratie met behulp van het Access-paneel. Wanneer u de ContractSafe Saml2 SSO-tegel selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de ContractSafe Saml2 SSO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer ContractSafe Saml2 SSO met Azure AD](https://aad.portal.azure.com/)
