---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met Dynatrace | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Dynatrace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 267ad37f-feda-4fac-bd15-7610174caf45
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 275215a69f97d93a1b1741a8fa8f3ca752c8c9ef
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758979"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-dynatrace"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Dynatrace

In deze zelfstudie leert u hoe u Dynatrace integreren met Azure Active Directory (Azure AD). Wanneer u Dynatrace integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Dynatrace.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Dynatrace met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Dynatrace single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Dynatrace ondersteunt **SP en IDP** geïnitieerd eS
* Dynatrace ondersteunt **Just In Time** gebruikersinrichting

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde. Er kan slechts één exemplaar in één tenant worden geconfigureerd.

## <a name="adding-dynatrace-from-the-gallery"></a>Dynatrace toevoegen vanuit de galerie

Als u de integratie van Dynatrace in Azure AD wilt configureren, moet u Dynatrace uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen**en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Dynatrace** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Dynatrace** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-dynatrace"></a>Azure AD-aanmelding voor Dynatrace configureren en testen

Azure AD SSO configureren en testen met Dynatrace met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Dynatrace.

Als u Azure AD SSO met Dynatrace wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Dynatrace SSO](#configure-dynatrace-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak Dynatrace-testgebruiker](#create-dynatrace-test-user)** - om een tegenhanger van B.Simon in Dynatrace te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina Met de integratie van **de Dynatrace-toepassing** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd in de **idp-gestarte** modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Klik **op Extra URL's instellen** en voer de volgende stap uit om de toepassing in de gestarte **SP-modus** te configureren:

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://sso.dynatrace.com/`

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de xml **met aalmetagegevens**van federatie . Selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Selecteer in de sectie **SAML-ondertekeningscertificaat** de knop **Bewerken** om het dialoogvenster **SAML-ondertekeningscertificaat** te openen. Voltooi de volgende stappen:

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

    a. De instelling **Ondertekeningsoptie** is vooraf ingevuld. Controleer de instellingen volgens uw organisatie.

    b. Klik op **Opslaan**.

    ![Communifire-optie voor ondertekenen](./media/dynatrace-tutorial/tutorial-dynatrace-signing-option.png)

1. Kopieer in de sectie **Dynatrace instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Dynatrace.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Dynatrace**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-dynatrace-sso"></a>Dynatrace SSO configureren

Als u eenmalige aanmelding wilt configureren aan de **Dynatrace-kant,** moet u het gedownloade **XML-bestand met federatiemetagegevens** en de juiste gekopieerde URL's van de Azure-portal naar [Dynatrace](https://www.dynatrace.com/support/help/shortlink/users-sso-hub)verzenden. U de instructies op de Dynatrace-website volgen om de SAML SSO-verbinding aan beide zijden te configureren.

### <a name="create-dynatrace-test-user"></a>Dynatrace-testgebruiker maken

In deze sectie wordt een gebruiker genaamd B.Simon gemaakt in Dynatrace. Dynatrace ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Dynatrace, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Dynatrace-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Dynatrace, waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Dynatrace met Azure AD](https://aad.portal.azure.com/)
