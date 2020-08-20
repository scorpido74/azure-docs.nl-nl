---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Lenses.io | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Lenses.io configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 0b8d736ab169ad07bd23a21d3a420bb6a044bf01
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528617"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met de Lenses.io DataOps-portal.

In deze zelfstudie leert u hoe u de [Lenses.io](https://lenses.io/) DataOps-portal kunt integreren met Azure Active Directory (Azure AD). Wanneer u Lenses.io integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot de Lenses.io-portal.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Lenses.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een instantie van een Lenses-portal. U kunt een lenzen-Portal implementeren op [verschillende manieren](https://lenses.io/product/deployment/).
* Een [Lenses.io-licentie](https://lenses.io/product/pricing/) die ondersteuning biedt voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Lenses.io biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

* Zodra u Lenses.io hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-lensesio-from-the-gallery"></a>Lenses.io toevoegen vanuit de galerie

Om de integratie van Lenses.io in Azure AD te configureren, moet u Lenses.io vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Lenses.io** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Lenses.io** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Eenmalige aanmelding van Azure AD voor Lenses.io configureren en testen

Configureer en test eenmalige aanmelding van Azure AD voor Lenses.io-portal met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Lenses.io.

Voltooi de volgende bouwstenen als u eenmalige aanmelding van Azure AD met Lenses.io wilt configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-testgebruiker en -groep](#create-an-azure-ad-test-user-and-group)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Lenses.io configureren](#configure-lensesio-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Machtigingen voor Lenses.io-testgroepen maken](#create-lensesio-test-group-permissions)** : hiermee bepaalt u waartoe B.Simon toegang heeft in Lenses.io (autorisatie).
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/), op de integratiepagina van de toepassing **Lenses.io**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<CUSTOMER_LENSES_BASE_URL>`, bijvoorbeeld `https://lenses.my.company.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<CUSTOMER_LENSES_BASE_URL>`, bijvoorbeeld `https://lenses.my.company.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client`
    bijvoorbeeld `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, antwoord-URL en id, op basis van de basis-URL van uw instantie van de Lenses-portal. Raadpleeg de [Lenses.io SSO-documentatie](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0) voor meer informatie.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Gebruik in de sectie **Lenses.io instellen** het eerder beschreven XML-bestand om Lenses te configureren voor eenmalige aanmelding via Azure.

### <a name="create-an-azure-ad-test-user-and-group"></a>Een Azure AD-testgebruiker en -groep maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal. U maakt ook een testgroep voor B.Simon. Hiermee bepaalt u welke toegang B.Simon heeft in Lenses.
U kunt in de [Lenses-documentatie voor eenmalige aanmelding](https://docs.lenses.io/install_setup/configuration/security.html#id3) achterhalen hoe Lenses toewijzing van groepslidmaatschap gebruikt voor autorisatie

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

De groep maken:
1. Ga terug naar **Azure Active Directory** en selecteer **Groepen**
1. Selecteer bovenaan het scherm **Nieuwe groep**.
1. Volg in **Eigenschappen van groep** de volgende stappen:
   1. Selecteer in het veld **Groepstype** de optie `Security`.
   1. Voer in het veld **Groepsnaam** de tekst `LensesUsers` in
   1. Klik op **Create**.
1. Selecteer de groep `LensesUsers` en noteer de **Object-id** (bijvoorbeeld `f8b5c1ec-45de-4abd-af5c-e874091fb5f7`). Deze id wordt in Lenses gebruikt om gebruikers van deze groep toe te wijzen aan de [juiste machtigingen](https://docs.lenses.io/install_setup/configuration/security.html#id3).  
   
De groep toewijzen aan de testgebruiker: 
1. Ga terug naar **Azure Active Directory** en selecteer **Gebruikers**.
1. Selecteer de testgebruiker `B.Simon`.
1. Selecteer **Groepen**.
1. Selecteer aan de bovenkant van het scherm **Lidmaatschappen toevoegen**.
1. Zoek en selecteer `LensesUsers`.
1. Klik op **Selecteren**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Lenses.io.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Lenses.io** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-lensesio-sso"></a>Eenmalige aanmelding voor Lenses.io configureren

Als u eenmalige aanmelding wilt configureren op de **Lenses.io**-portal, installeert u het gedownloade **XML-bestand met federatieve metagegevens** op uw instantie van Lenses en [configureert u Lenses om eenmalige aanmelding in te schakelen](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses). 

### <a name="create-lensesio-test-group-permissions"></a>Machtigingen voor een Lenses.io-testgroep maken

In deze sectie maakt u een groep in Lenses met behulp van de **object-id** van de groep `LensesUsers` die we hebben genoteerd in de sectie voor het [maken van gebruikers](#create-an-azure-ad-test-user-and-group).
U wijst de gewenste machtigingen toe die `B.Simon` in Lenses moet hebben.
Zie [Azure - Lenses-groepstoewijzing](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Lenses.io klikt, wordt u automatisch aangemeld bij de instantie van de Lenses.io-portal waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ Eenmalige aanmelding instellen in uw instantie van Lenses.io ](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Lenses.io met Azure AD uitproberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Lenses.io beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
