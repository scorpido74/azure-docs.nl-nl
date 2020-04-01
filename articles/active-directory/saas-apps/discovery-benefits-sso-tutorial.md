---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Discovery Benefits SSO | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Discovery Benefits SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a788cd07-0eed-4067-b79d-19b840e8836d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c4a6811ef5d7ed4f29c7dae89561616895a42a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72266141"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Discovery Benefits SSO

In deze zelfstudie leert u hoe U Discovery Benefits SSO integreren met Azure Active Directory (Azure AD). Wanneer u Discovery Benefits SSO integreert met Azure AD, u het als nog niet doen:

* Beheer in Azure AD die toegang heeft tot Discovery Benefits SSO.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Discovery Benefits SSO met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Discovery Benefits SSO single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Discovery Benefits SSO ondersteunt **IDP** geïnitieerde SSO

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>Discovery Benefits SSO toevoegen vanuit de galerie

Als u de integratie van Discovery Benefits SSO in Azure AD wilt configureren, moet u Discovery Benefits SSO uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **SSO Detectievoordelen sso** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Discovery Benefits SSO** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>Azure AD-aanmelding configureren en testen voor Discovery Benefits SSO

Azure AD SSO configureren en testen met Discovery Benefits SSO met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Discovery Benefits SSO.

Als u Azure AD SSO wilt configureren en testen met Discovery Benefits SSO, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Discovery Benefits SSO SSO](#configure-discovery-benefits-sso-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[SSO-testgebruiker detectievoordelen](#create-discovery-benefits-sso-test-user)** maken - om een tegenhanger van B.Simon te hebben in Discovery Benefits SSO die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **SSO-toepassingsintegratie van Detectievoordelen** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd in de **idp-gestarte** modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Discovery Benefits SSO-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![installatiekopie](common/edit-attribute.png)

    a. Klik op **pictogram Bewerken** om het dialoogvenster Unieke **gebruikersnaam (naam-id)** te openen.

    ![SSO-configuratie van Detectievoordelen](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![SSO-configuratie van Detectievoordelen](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. Klik op **pictogram Bewerken** om het dialoogvenster **Transformatie beheren** te openen.

    c. Typ **in** het tekstvak Transformatie de **touppercase()** die voor die rij wordt weergegeven.

    d. Typ **de** parameter zoals `<Name Identifier value>`.

    e. Klik op**toevoegen**.

    > [!NOTE]
    > Discovery Benefits SSO vereist dat een vaste tekenreekswaarde wordt doorgegeven in het veld **Unieke gebruikersnaam (Name ID)** om deze integratie te laten werken. Azure AD ondersteunt deze functie momenteel niet, dus als een work around u **ToUpper** of **ToLower-transformaties** van NameID gebruiken om een vaste tekenreekswaarde in te stellen zoals hierboven in de schermafbeelding wordt weergegeven.

    f. We hebben de aanvullende claims die nodig zijn voor`SSOInstance` `SSOID`De Configuratie van SSO automatisch ingevuld (en ). Gebruik het pictogram **Bewerken** om de waarden per organisatie in kaart te brengen.

    ![SSO-configuratie van Detectievoordelen](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **SSO Detectievoordelen instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Discovery Benefits SSO.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Discovery Benefits SSO**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-discovery-benefits-sso-sso"></a>Detectievoordelen SSO SSO configureren

Als u eenmalige aanmelding wilt configureren aan de **SSO-kant** van Discovery Benefits, moet u het gedownloade **certificaat (Base64)** en de juiste gekopieerde URL's van Azure-portal naar [het SSO-ondersteuningsteam voor Detectievoordelen](mailto:Jsimpson@DiscoveryBenefits.com)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-discovery-benefits-sso-test-user"></a>Detectievoordelen SSO-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in Discovery Benefits SSO. Werk samen met [het SSO-ondersteuningsteam van Discovery Benefits](mailto:Jsimpson@DiscoveryBenefits.com) om de gebruikers toe te voegen aan het Discovery Benefits SSO-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de SSO-tegel Detectievoordelen klikt in het toegangspaneel, moet u automatisch worden aangemeld bij de Discovery Benefits SSO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Discovery Benefits SSO met Azure AD](https://aad.portal.azure.com/)

