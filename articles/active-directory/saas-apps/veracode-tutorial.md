---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Veracode | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Veracode.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.openlocfilehash: a8c7c70e7956868bf069704eb5fe34db014dd135
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88532463"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Zelfstudie: Integratie van eenmalige aanmelding via Azure Active Directory met Veracode

In deze zelfstudie leert u hoe u Veracode integreert met Azure AD (Azure Active Directory). Wanneer u Veracode integreert met Azure AD, kunt u het volgende doen:

* Beheren in Azure AD wie toegang heeft tot Veracode.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Veracode.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Als u meer wilt weten over de integratie van SaaS-apps (Software as a Service) met Azure AD, gaat u naar [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Veracode waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Veracode ondersteunt door de identiteitsprovider geïnitieerde eenmalige aanmelding en just-in-time gebruikersinrichting.

## <a name="add-veracode-from-the-gallery"></a>Veracode toevoegen vanuit de galerie

Om de integratie van Veracode in Microsoft Azure Active Directory te configureren, voegt u Veracode vanuit de galerie toe aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: 'Veracode'.
1. Selecteer **Veracode** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Veracode

Configureer en test eenmalige aanmelding van Azure AD met Veracode met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppeling tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Veracode.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Veracode, moet u de volgende procedures uitvoeren:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Veracode configureren](#configure-veracode-sso)** als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een Veracode-testgebruiker maken](#create-veracode-test-user)** als u een equivalent van B.Simon in Veracode wilt hebben die gekoppeld is aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Veracode** de sectie **Beheren**. Selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van Eenmalige aanmelding instellen met SAML, met het potloodpictogram gemarkeerd](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. Selecteer **Opslaan**.

1. Ga naar de pagina **Eenmalige aanmelding met SAML instellen** en zoek in de sectie **SAML-handtekeningcertificaat** de optie **Certificaat (Base64)** . Selecteer **Downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![Schermopname van de sectie SAML-handtekeningcertificaat, met de koppeling Downloaden gemarkeerd](common/certificatebase64.png)

1. In Veracode worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Schermopname van de sectie Gebruikerskenmerken en claims](common/default-attributes.png)

1. Bovendien worden in Veracode nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze kenmerken worden ook vooraf ingevuld, maar u kunt deze indien nodig herzien.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | e-mail |User.mail |

1. In de sectie **Veracode instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Schermopname van de sectie Veracode instellen, met configuratie-URL's gemarkeerd](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Eenmalige aanmelding voor Veracode configureren

1. Meld u in een ander browservenster als een beheerder aan bij de bedrijfssite van Veracode.

1. Selecteer **Instellingen** > **Beheerder** in het menu bovenaan.
   
    ![Schermopname van Veracode-beheer met het pictogram Instellingen en Beheer gemarkeerd](./media/veracode-tutorial/ic802911.png "Beheer")

1. Selecteer het tabblad **SAML**.

1. Voer in de sectie **SAML-instellingen van organisatie** de volgende stappen uit:

    ![Schermafbeelding van de sectie SAML-instellingen van de organisatie](./media/veracode-tutorial/ic802912.png "Beheer")

    a.  Plak bij **Verlener** de waarde van **Azure AD-id** die u heeft gekopieerd uit het Azure-portaal.

    b. Selecteer bij **Certificaat voor ondertekening assertie** **Bestand kiezen** om uw gedownloade certificaat vanuit het Azure-portaal te uploaden.

    c. Selecteer bij **Zelfregistratie** **Zelfregistratie inschakelen**.

1. Voer in de sectie **Instellingen voor zelfregistratie** de volgende stappen uit en selecteer vervolgens **Opslaan**:

    ![Schermopname van sectie Instellingen voor zelfregistratie, met verschillende opties gemarkeerd](./media/veracode-tutorial/ic802913.png "Beheer")

    a. Selecteer bij **Activering nieuwe gebruiker** **Geen activering vereist**.

    b. Selecteer bij **Updates gebruikersgegevens** **Voorkeuren Veracode-gebruikersgegevens**.

    c. Selecteer bij **Details SAML-kenmerk** het volgende:
      * **Gebruikersrollen**
      * **Beleidsbeheerder**
      * **Revisor**
      * **Beveiligingslead**
      * **Leidinggevende**
      * **Indiener**
      * **Maker**
      * **Alle scantypen**
      * **Teamlidmaatschappen**
      * **Standaard team**

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** >**Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:

   1. Voer `B.Simon` in bij **Naam**.  
   1. Voer bij **Gebruikersnaam** de username@companydomain.extension in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Selecteer **Wachtwoord weergeven** en noteer de weergegeven waarde.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

Geef in deze sectie B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Veracode.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **Veracode** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Schermopname van de sectie Beheren, met Gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Schermopname van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer **B.Simon** bij **Gebruikers** in het dialoogvenster **Gebruikers en groepen**. Kies vervolgens **Selecteren** onderaan het scherm.
1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **Selecteren** onderaan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-veracode-test-user"></a>Veracode-testgebruiker maken

Om zich aan te melden bij Veracode, moeten Azure AD-gebruikers worden ingericht in Veracode. Deze taak is geautomatiseerd en u hoeft niets handmatig te doen. Gebruikers worden automatisch aangemaakt als dit nodig is bij de eerste aanmeldingspoging.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van gebruikersaccounts of API's van Veracode gebruiken om Microsoft Azure Active Directory-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u in het toegangsvenster **Veracode** selecteert, wordt u automatisch aangemeld bij de instantie van Veracode waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Veracode met Azure AD uitproberen](https://aad.portal.azure.com/)