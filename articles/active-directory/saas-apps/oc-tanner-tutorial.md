---
title: 'Zelfstudie: Azure Active Directory-integratie met O.C. Tanner - AppreciateHub | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en O.C. Tanner - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: jeedes
ms.openlocfilehash: 768756e013a4301ea11f5f9478178bdad46f9d67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983993"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oc-tanner---appreciatehub"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met O.C. Tanner - AppreciateHub

In deze zelfstudie leer je hoe je O.C. integreren. Tanner - AppreciateHub met Azure Active Directory (Azure AD). Wanneer u O.C. integreert. Tanner - AppreciateHub met Azure AD, u kunt:

* Beheer in Azure AD die toegang heeft tot O.C. Tanner - AppreciateHub.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij O.C. Tanner - AppreciateHub met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* O.C. Tanner - AppreciateHub single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* O.C. Tanner - AppreciateHub ondersteunt **IDP** geïnitieerde SSO

* Zodra u de O.C. hebt geconfigureerd. Tanner - AppreciateHub u sessiecontroles afdwingen, die exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebesturingselementen zijn van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>O.C. toevoegen. Tanner - AppreciateHub vanuit de galerie

De integratie van O.C. configureren. Tanner - AppreciateHub in Azure AD, u moet O.C. toevoegen. Tanner - AppreciateHub van de galerij naar uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **O.C. Tanner - AppreciateHub** in het zoekvak in de sectie **Toevoegen in de galerijsectie.**
1. Selecteer **O.C. Tanner - AppreciateHub** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-oc-tanner---appreciatehub"></a>Azure AD-eenmaligaanmelding configureren en testen voor O.C. Tanner - AppreciateHub

Azure AD SSO configureren en testen met O.C. Tanner - AppreciateHub met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in O.C. Tanner - AppreciateHub.

Azure AD SSO configureren en testen met O.C. Tanner - AppreciateHub, voltooi de volgende bouwstenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer O.C. Tanner - AppreciateHub SSO](#configure-oc-tanner---appreciatehub-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak O.C. Tanner - AppreciateHub testgebruiker](#create-oc-tanner---appreciatehub-test-user)** - om een tegenhanger van B.Simon in O.C. te hebben. Tanner - AppreciateHub die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **O.C. Tanner - AppreciateHub-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding.**
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Ok-tanner instellen - AppreciateHub** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot O.C. Tanner - AppreciateHub.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **O.C. Tanner - AppreciateHub**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-oc-tanner---appreciatehub-sso"></a>Ok configureren. Tanner - AppreciateHub SSO

Als u eenmalige aanmelding wilt configureren op **de O.C. Tanner - AppreciateHub-kant,** moet u de gedownloade **Xml met federatiemetagegevens** en de juiste gekopieerde URL's van Azure-portal naar [o.c. Tanner - ondersteuningsteam Van AppreciateHub](mailto:sso@octanner.com)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-oc-tanner---appreciatehub-test-user"></a>Create O.C. Tanner - AppreciateHub testgebruiker

Het doel van deze sectie is het creëren van een gebruiker genaamd Britta Simon in O.C. Tanner - AppreciateHub.

**Voer de volgende stappen uit om een gebruiker genaamd Britta Simon te maken in O.C. Tanner - AppreciateHub:**

Vraag uw [O.C. Tanner - AppreciateHub-ondersteuningsteam](mailto:sso@octanner.com) om een gebruiker te maken met als naam-ID dezelfde waarde als de gebruikersnaam van Britta Simon in Azure AD.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de O.C. klikt. Tanner - AppreciateHub tegel in het Access Panel, moet u automatisch worden aangemeld bij de O.C. Tanner - AppreciateHub waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer O.C. Tanner - AppreciateHub met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hoe o.c. Tanner te beschermen - AppreciateHub met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)