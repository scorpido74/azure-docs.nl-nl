---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met TINFOIL-beveiliging | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en TINFOIL-beveiliging.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d3078b553843922cd51e4e0f43ea84b6dcde16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74170769"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met TINFOIL-beveiliging

In deze zelf studie leert u hoe u TINFOIL SECURITY integreert met Azure Active Directory (Azure AD). Wanneer u TINFOIL-beveiliging integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot TINFOIL-beveiliging.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld voor TINFOIL-beveiliging met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Eenmalige aanmelding (SSO) met TINFOIL-beveiliging ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* TINFOIL SECURITY ondersteunt **IDP** GEÏNITIEERDe SSO

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-tinfoil-security-from-the-gallery"></a>TINFOIL-beveiliging toevoegen vanuit de galerie

Als u de integratie van TINFOIL-beveiliging in azure AD wilt configureren, moet u TINFOIL-beveiliging van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **TINFOIL beveiliging** in het zoekvak.
1. Selecteer **TINFOIL beveiliging** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor TINFOIL-beveiliging

Configureer en test Azure AD SSO met TINFOIL-beveiliging met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in TINFOIL-beveiliging.

Als u Azure AD SSO wilt configureren en testen met TINFOIL-beveiliging, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[TINFOIL Security SSO configureren](#configure-tinfoil-security-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een TINFOIL-gebruiker voor beveiligings test](#create-tinfoil-security-test-user)** : als u een equivalent van B. Simon wilt hebben in TINFOIL-beveiliging dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina integratie van **beveiligings** toepassing van TINFOIL de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **basis configuratie van SAML** is de toepassing vooraf geconfigureerd en de benodigde url's zijn al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door te klikken op de knop **Opslaan** .

1. Voor de meest bezochte toepassing worden de SAML-beweringen in een specifieke indeling verwacht. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. In aanvulling op de bovenstaande stappen verwachtte de toepassing nog maar weinig kenmerken om te worden door gegeven in een SAML-respons die hieronder wordt weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam | Bronkenmerk |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    > [!NOTE]
    > Verderop in de zelf studie wordt de waarde van accountid beschreven.

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer de waarde van de **vinger afdruk** in de sectie **SAML-handtekening certificaat** en sla deze op uw computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. Op de **TINFOIL-beveiliging instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot TINFOIL-beveiliging.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **TINFOIL Security**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-tinfoil-security-sso"></a>TINFOIL SECURITY SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij de TINFOIL SECURITY-bedrijfs site.

1. Klik in de werk balk bovenaan op **Mijn account**.

    ![Dashboard](./media/tinfoil-security-tutorial/ic798971.png "Dashboard")

1. Klik op **Beveiliging**.

    ![Beveiliging](./media/tinfoil-security-tutorial/ic798972.png "Beveiliging")

1. Voer op de configuratiepagina **Single Sign-On** de volgende stappen uit:

    ![Eenmalige aanmelding](./media/tinfoil-security-tutorial/ic798973.png "Eenmalige aanmelding")

    a. Selecteer **SAML inschakelen**.

    b. Klik op **hand matige configuratie**.

    c. Plak in het tekstvak **SAML post-URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit Azure Portal

    d. Plak in het tekstvak **SAML-certificaat vingerafdruk** de waarde van de **vinger afdruk** die u hebt gekopieerd uit de sectie **SAML-handtekening certificaat** .
  
    e. Kopieer de waarde van **uw account-id** en plak de waarde in het tekstvak **bron kenmerk** onder **gebruikers kenmerken & sectie claims** in azure Portal.

    f. Klik op **Opslaan**.

### <a name="create-tinfoil-security-test-user"></a>TINFOIL-BEVEILIGINGS test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij TINFOIL-beveiliging, moeten ze worden ingericht in TINFOIL-beveiliging. In het geval van TINFOIL-beveiliging is inrichting een hand matige taak.

**Als u een gebruiker wilt inrichten, voert u de volgende stappen uit:**

1. Als de gebruiker deel uitmaakt van een bedrijfs account, moet u [contact opnemen met het ondersteunings team van TINFOIL](https://www.tinfoilsecurity.com/contact) om het gebruikers account te maken.

1. Als de gebruiker een gewone TINFOIL beveiliging SaaS-gebruiker is, kan de gebruiker een samen werker toevoegen aan een van de sites van de gebruiker. Hiermee wordt een proces geactiveerd om een uitnodiging naar het opgegeven e-mail bericht te verzenden om een nieuw TINFOIL-BEVEILIGINGS gebruikers account te maken.

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van TINFOIL-gebruikers accounts of Api's van TINFOIL SECURITY gebruiken om Azure AD-gebruikers accounts in te richten.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel TINFOIL beveiliging in het toegangs venster klikt, moet u automatisch worden aangemeld bij de TINFOIL-beveiliging waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [TINFOIL beveiliging proberen met Azure AD](https://aad.portal.azure.com/)