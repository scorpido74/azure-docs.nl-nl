---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Catchpoint'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Catchpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b19e286d299811a950df05f93d221bd710676ea
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743493"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Zelfstudie: Azure Active Directory-integratie met enkele aanmelding strekken met Catchpoint

In deze zelfstudie leert u hoe u Catchpoint integreert met Azure Active Directory (Azure AD). Wanneer u Catchpoint integreert met Azure AD, u het als:

* Beheer de toegang van gebruikers tot Catchpoint vanuit Azure AD.
* Automatische aanmelding voor Catchpoint inschakelen voor gebruikers met Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory voor](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)meer informatie over de integratie van de SaaS-app met Azure AD. .

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Een Catchpoint-abonnement met single sign-on (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Catchpoint ondersteunt door SP geïnitieerde en IDP-geïnitieerde SSO.
* Catchpoint ondersteunt just-in-time (JIT) gebruikersinrichting.
* Nadat u Catchpoint hebt geconfigureerd, u sessiebeheer afdwingen. Deze voorzorgsmaatregel beschermt in realtime tegen exfiltratie en infiltratie van gevoelige gegevens van uw organisatie. Sessiebeheer is een uitbreiding van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-catchpoint-from-the-gallery"></a>Catchpoint toevoegen vanuit de galerie

Als u de integratie van Catchpoint in Azure AD wilt configureren, voegt u Catchpoint toe aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk-, school- of persoonlijk Microsoft-account.
1. Selecteer in het linkerdeelvenster de **Azure Active Directory-service.**
1. Ga naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Catchpoint** in het zoekvak in de sectie **Toevoegen in de sectie Toevoegen in de galerie.**
1. Selecteer **Catchpoint** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Azure AD-aanmelding voor Catchpoint configureren en testen

Als SSO kan werken, moet u een Azure AD-gebruiker koppelen aan een gebruiker in Catchpoint. Voor deze zelfstudie configureren we een testgebruiker genaamd **B.Simon**. 

Vul de volgende secties in:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso)om deze functie voor uw gebruikers in te schakelen.
    * [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)om azure AD-enkele aanmelding te testen met B.Simon.
    * [Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. [Configureer Catchpoint SSO](#configure-catchpoint-sso)om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * [Maak catchpoint-testgebruiker](#create-a-catchpoint-test-user)om koppeling van het B.Simon Azure AD-testaccount toe te staan aan een vergelijkbaar gebruikersaccount in Catchpoint.
1. [Test SSO](#test-sso), om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen in de Azure-portal om Azure AD SSO in te schakelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zoek op de pagina **Catchpoint-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Selecteer op de pagina **Eén aanmelding instellen met SAML** het penpictogram om de **basis-SAML-configuratie-instellingen** te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Configureer de gestarte modus voor Catchpoint:
   - Voer voor de **iDP-modus**de waarden voor de volgende velden in:
     - Voor **identificatie:**`https://portal.catchpoint.com/SAML2`
     - Voor **de URL van antwoord:**`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - Selecteer **SP**voor sp-gestarte modus De optie **Extra URL's instellen** en voer de volgende waarde in:
     - Voor **aanmeldings-URL:**`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. De Catchpoint-toepassing verwacht dat de SAML-beweringen in een specifiek formaat worden weergegeven. Voeg aangepaste kenmerktoewijzingen toe aan uw configuratie van SAML-tokenkenmerken. De volgende tabel bevat de lijst met standaardkenmerken:

    | Name | Bronkenmerk|
    | ------------ | --------- |
    | Givenname | user.givenneame |
    | Achternaam | user.surname |
    | Emailaddress | user.mail |
    | Name | user.userprincipalname |
    | Unieke gebruikers-id | user.userprincipalname |

    ![Schermafbeelding van de lijst met gebruikerskenmerken & claims](common/default-attributes.png)

1. De Catchpoint-toepassing verwacht ook dat een ander kenmerk wordt doorgegeven in een SAML-reactie. Zie de volgende tabel. Dit kenmerk is ook vooraf ingevuld, maar u het bekijken en bijwerken om aan uw vereisten te voldoen.

    | Name | Bronkenmerk|
    | ------------ | --------- |
    | naamruimte | user.assignedrole |

    > [!NOTE]
    > De `namespace` claim moet in kaart worden gebracht met de accountnaam. Deze accountnaam moet worden ingesteld met een rol in Azure AD om terug te worden gegeven in SAML-reactie. Zie [De rolclaim configureren die is uitgegeven in het SAML-token voor bedrijfstoepassingen voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)meer informatie over rollen in Azure AD.

1. Ga naar de pagina **Eén aanmelding instellen met SAML.** Zoek in de sectie **SAML-ondertekeningscertificaat** **het certificaat (Base64).** Selecteer **Downloaden** om het certificaat op uw computer op te slaan.

    ![De koppeling certificaatdownload](common/certificatebase64.png)

1. Kopieer in de sectie **Catchpoint instellen** de URL's die u in een latere stap nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gebruikt u de Azure-portal om een Azure AD-testgebruiker met de naam B.Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory** > **Users** > **All users .**
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Geef bijvoorbeeld `B.Simon@contoso.com` op.
   1. Schakel het selectievakje **Wachtwoord weergeven** in. Let op de weergegeven wachtwoordwaarde.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Catchpoint.

1. Selecteer in de Azure-portal Alle**bedrijfstoepassingen** **.** > 
1. Selecteer **Catchpoint**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst met gebruikers. Klik onder aan het scherm op **Selecteren.**
1. Als u een rolwaarde verwacht in de SAML-bewering, kijkt u in het dialoogvenster **Rol selecteren** en kiest u de rol van de gebruiker in de lijst. Klik op de knop **Selecteren** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-catchpoint-sso"></a>Catchpoint SSO configureren

1. Meld u in een ander browservenster aan bij de Catchpoint-toepassing als beheerder.

1. Selecteer het pictogram **Instellingen** en vervolgens **SSO Identity Provider**.

    ![Schermafbeelding van de Catchpoint-instellingen met SSO-identiteitsprovider geselecteerd](./media/catchpoint-tutorial/configuration1.png)

1. Voer op de pagina **Eén aanmelding** de volgende velden in:

   ![Schermafbeelding van de schermafbeelding van de pagina Van Catchpoint Single Sign On](./media/catchpoint-tutorial/configuration2.png)

   Veld | Waarde
   ----- | ----- 
   **Namespace** | Een geldige naamruimtewaarde.
   **Uitgever van identiteitsprovider** | De `Azure AD Identifier` waarde van de Azure-portal.
   **Url voor eenmalig aanmelding** | De `Login URL` waarde van de Azure-portal.
   **Certificaat** | De inhoud van `Certificate (Base64)` het gedownloade bestand van de Azure-portal. Gebruik Kladblok om te bekijken en te kopiëren.

   U ook de **XML met federatiemetagegevens uploaden** door de optie **Metagegevens uploaden** te selecteren.

1. Selecteer **Opslaan**.

### <a name="create-a-catchpoint-test-user"></a>Een Catchpoint-testgebruiker maken

Catchpoint ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Je hebt geen actie-items in deze sectie. Als B.Simon nog niet bestaat als gebruiker in Catchpoint, wordt deze gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO

In deze sectie test u uw Azure AD-configuratie met eenmalige aanmelding met de portal Mijn apps.

Wanneer u de catchpoint-tegel selecteert in de portal Mijn apps, moet u automatisch worden aangemeld bij de Catchpoint-app met SSO geconfigureerd. Zie Apps aanmelden en apps [starten via de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)voor meer informatie over de portal Mijn apps.

> [!NOTE]
> Wanneer u bent aangemeld bij de Catchpoint-toepassing via de aanmeldingspagina, nadat u **Catchpoint-referenties**hebt verstrekt, voert u de geldige **naamruimtewaarde** in het veld **Bedrijfsreferenties (SSO)** in en selecteert u **Aanmelding**.
> 
> ![Catchpoint-configuratie](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Catchpoint uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
