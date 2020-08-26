---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Prisma Cloud SSO | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Prisma Cloud SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.openlocfilehash: 2b81da563ff4662f6ff99351d1dbfa98afff7a7d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552317"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-prisma-cloud-sso"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Prisma Cloud SSO

In deze zelfstudie leert u hoe u Prisma Cloud SSO kunt integreren met Azure AD (Active Directory). Wanneer u Prisma Cloud SSO integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Prisma Cloud SSO.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Prisma Cloud SSO.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Prisma Cloud SSO-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Prisma Cloud SSO biedt ondersteuning voor met **IDP** geïnitieerde eenmalige aanmelding

* Prisma Cloud SSO biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers

* Zodra u Prisma Cloud SSO hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens in uw organisatie in realtime worden beveiligd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-prisma-cloud-sso-from-the-gallery"></a>Prisma Cloud SSO toevoegen vanuit de galerie

Om de integratie van Prisma Cloud SSO te configureren in Azure AD moet u Prisma Cloud SSO vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Prisma Cloud SSO**.
1. Selecteer **Prisma Cloud SSO** in het resultatenpaneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-prisma-cloud-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Prisma Cloud SSO

Configureer en test eenmalige aanmelding van Azure AD met Prisma Cloud SSO met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Prisma Cloud SSO.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Prisma Cloud SSO, voert u de volgende procedures uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Prisma Cloud SSO configureren](#configure-prisma-cloud-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Prisma Cloud SSO-testgebruiker maken](#create-prisma-cloud-sso-test-user)** : als u een tegenhanger van B.Simon in Prisma Cloud SSO wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Prisma Cloud SSO** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://app2.prismacloud.io/customer/<CUSTOMERID>`

    b. De waarden voor **Antwoord-URL** zijn vaste waarden en al vooraf ingevuld in Azure Portal. U moet de gewenste URL selecteren die voldoet uw vereisten.

    > [!NOTE]
    > De id-waarde is niet echt. Werk de waarde bij met de werkelijke id. Neem contact op met het [klantondersteuningsteam van Prisma Cloud SSO](mailto:support@paloaltonetworks.com) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Prisma Cloud SSO instellen** de juiste URL('s) op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Prisma Cloud SSO.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Prisma Cloud SSO** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-prisma-cloud-sso"></a>Eenmalige aanmelding bij Prisma Cloud SSO configureren

Als u eenmalige aanmelding aan de zijde van **Prisma Cloud SSO** wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste gekopieerde URL's uit Azure Portal verzenden naar het [ondersteuningsteam van Prisma Cloud SSO](mailto:support@paloaltonetworks.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-prisma-cloud-sso-test-user"></a>Een Prisma Cloud SSO-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Prisma Cloud SSO. Prisma Cloud SSO biedt ondersteuning voor het Just-In-Time inrichten van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Prisma Cloud SSO bestaat, wordt er een nieuwe gemaakt wanneer u Prisma Cloud SSO opent.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Prisma Cloud SSO klikt, wordt u automatisch aangemeld bij de instantie van Prisma Cloud SSO waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Prisma Cloud SSO uitproberen met Azure AD](https://aad.portal.azure.com/)
