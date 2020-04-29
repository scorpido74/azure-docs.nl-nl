---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Veracode | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcec326ddab1e74f43e1bb7ef446998a40799fd0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73043559"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Veracode

In deze zelf studie leert u hoe u Veracode integreert met Azure Active Directory (Azure AD). Wanneer u Veracode integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Veracode.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Veracode met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een Veracode-abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. Veracode ondersteunt eenmalige aanmelding met de ID-provider en just-in-time-gebruikers inrichting.

## <a name="add-veracode-from-the-gallery"></a>Veracode toevoegen vanuit de galerie

Als u de integratie van Veracode in azure AD wilt configureren, voegt u Veracode van de galerie toe aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ ' Veracode ' in het zoekvak van de sectie **toevoegen vanuit de galerie** .
1. Selecteer **Veracode** in het deel venster resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Veracode

Azure AD SSO met Veracode configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppeling tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Veracode.

Als u Azure AD SSO wilt configureren en testen met Veracode, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. **[Configureer VERACODE SSO](#configure-veracode-sso)** voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een Veracode-test gebruiker](#create-veracode-test-user)** om een tegen hanger te hebben van B. Simon in Veracode gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Veracode** de sectie **beheren** . Selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het potlood pictogram voor de basis- **SAML-configuratie** om de instellingen te bewerken.

   ![Scherm opname van eenmalige aanmelding met SAML instellen, waarbij het potlood pictogram is gemarkeerd](common/edit-urls.png)

1. In de sectie **basis configuratie van SAML** is de toepassing vooraf geconfigureerd en de benodigde url's zijn al vooraf ingevuld met Azure. Selecteer **Opslaan**.

1. Ga op de pagina **eenmalige aanmelding met SAML instellen** naar **certificaat (base64)** in het gedeelte **SAML-handtekening certificaat** . Selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![Scherm afbeelding van het certificaat van het SAML-Ondertekeningscertificaat, met de download koppeling gemarkeerd](common/certificatebase64.png)

1. Veracode verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Scherm opname van gebruikers kenmerken & sectie claims](common/default-attributes.png)

1. Veracode verwacht ook nog enkele kenmerken die opnieuw moeten worden door gegeven in het SAML-antwoord. Deze kenmerken zijn ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | firstname |Gebruiker. voor rang |
    | lastname |Gebruikers naam |
    | e-mail |User.mail |

1. Op de sectie **Veracode instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Scherm afbeelding van de sectie Veracode instellen, met configuratie-Url's gemarkeerd](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Veracode SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw Veracode-bedrijfs site.

1. Selecteer in het menu aan de bovenkant **instellingen** > **beheerder**.
   
    ![Scherm opname van Veracode-beheer met instellingen pictogram en beheerder gemarkeerd](./media/veracode-tutorial/ic802911.png "Beheer")

1. Selecteer het tabblad **SAML** .

1. Voer de volgende stappen uit in de sectie instellingen van de **organisatie-SAML** :

    ![Scherm afbeelding van de sectie SAML-instellingen van de organisatie](./media/veracode-tutorial/ic802912.png "Beheer")

    a.  Plak voor de **Uitgever**de waarde van de **Azure ad-id** die u hebt gekopieerd uit de Azure Portal.

    b. Selecteer bij **bevestigings handtekening certificaat**de **optie bestand kiezen** om uw gedownloade certificaat te uploaden van de Azure Portal.

    c. Selecteer voor **zelf registratie**de optie **zelf registratie inschakelen**.

1. Voer in de sectie **instellingen voor zelf registratie** de volgende stappen uit en selecteer vervolgens **Opslaan**:

    ![Scherm afbeelding van de sectie instellingen voor zelf registratie, waarbij verschillende opties zijn gemarkeerd](./media/veracode-tutorial/ic802913.png "Beheer")

    a. Selecteer **geen activering vereist**voor **nieuwe gebruikers activering**.

    b. Selecteer **Voorkeurs Veracode gebruikers gegevens**voor het **bijwerken van gebruikers gegevens**.

    c. Voor **informatie over het SAML-kenmerk**selecteert u het volgende:
      * **Gebruikers rollen**
      * **Beleids beheerder**
      * **Revisor**
      * **Beveiligings lead**
      * **Leidinggevende**
      * **Indien**
      * **Kunsten**
      * **Alle typen scans**
      * **Team lidmaatschappen**
      * **Standaard team**

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer **Azure Active Directory** >**gebruikers** > van**alle gebruikers**in het linkerdeel venster van de Azure Portal.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :

   1. Voer **Name** `B.Simon`in bij naam.  
   1. Voer de username@companydomain.extensionin bij **gebruikers naam**. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Selecteer **wacht woord weer geven**en noteer de waarde die wordt weer gegeven.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Veracode.

1. Selecteer in het Azure Portal**alle toepassingen**in **bedrijfs toepassingen** > .
1. Selecteer in de lijst toepassingen de optie **Veracode**.
1. Zoek op de pagina overzicht van de app de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![Scherm opname van de sectie beheren met gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![Scherm opname van de pagina gebruikers en groepen, met gemarkeerde gebruiker toevoegen](common/add-assign-user.png)

1. Klik in het dialoog venster **gebruikers en groepen** van **gebruikers**op **B. Simon**. Kies vervolgens **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **selecteren** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-veracode-test-user"></a>Veracode-test gebruiker maken

Als u zich wilt aanmelden bij Veracode, moeten Azure AD-gebruikers worden ingericht in Veracode. Deze taak is geautomatiseerd en u hoeft niets hand matig te doen. Gebruikers worden automatisch gemaakt als dit nodig is tijdens de eerste poging tot eenmalige aanmelding.

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van Veracode-gebruikers accounts of Api's die worden geleverd door Veracode, gebruiken om Azure AD-gebruikers accounts in te richten.

## <a name="test-sso"></a>SSO testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u **Veracode** selecteert in het toegangs venster, moet u automatisch worden aangemeld bij de Veracode waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Veracode met Azure AD](https://aad.portal.azure.com/)