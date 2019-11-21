---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Freshservice | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Freshservice configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e08ef72dca09f873ad1cfcc91e132063b88406b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227523"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Freshservice

In deze zelf studie leert u hoe u Freshservice integreert met Azure Active Directory (Azure AD). Wanneer u Freshservice integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Freshservice.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Freshservice met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Freshservice-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Freshservice biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-freshservice-from-the-gallery"></a>Freshservice toevoegen vanuit de galerie

Om de integratie van Freshservice te configureren in Azure AD, moet u Freshservice vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Freshservice** in het zoekvak.
1. Selecteer **Freshservice** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Freshservice

Azure AD SSO met Freshservice configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Freshservice.

Als u Azure AD SSO wilt configureren en testen met Freshservice, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[FRESHSERVICE SSO configureren](#configure-freshservice-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Freshservice-test gebruiker](#create-freshservice-test-user)** -om een equivalent van B. Simon in Freshservice te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Freshservice** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<democompany>.freshservice.com`

    b. In het tekstvak **Id (entiteits-id)** typt u een URL met het volgende patroon: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantondersteuningsteam van Freshservice](https://support.freshservice.com/) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Freshservice vereist SHA-256-vinger afdruk om SSO te laten werken. Voer de volgende stappen uit om de SHA-256-vinger afdruk op te halen:

    ![Vingerafdruk](./media/freshservice-tutorial/ic790821.png "Vingerafdruk")

    1. Open de [koppeling](https://www.samltool.com/fingerprint.php) in de verschillende webbrowser.

    1. Open het gedownloade certificaat bestand (base64) in het klad blok en plak inhoud in het tekstvak **X. 509-certificaat** .

    1. Voor de algoritme selecteert u **sha256** in de vervolg keuzelijst.

    1. Klik op **vinger afdruk berekenen**.

    1. Klik op het pictogram kopiëren om de gegenereerde **vinger afdruk** te kopiëren en op uw computer op te slaan.

1. Kopieer de juiste URL ('s) op basis van uw vereiste op de **Azure Portal**de sectie **Freshservice instellen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Freshservice.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Freshservice** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-freshservice-sso"></a>Freshservice SSO configureren

1. Als u de configuratie wilt automatiseren in Freshservice, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup Freshservice** gaat u naar de Freshservice-toepassing. Geef de beheerders referenties op om u aan te melden bij Freshservice. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-6 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u Freshservice hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw Freshservice-bedrijfs site als beheerder en voert u de volgende stappen uit:

4. Klik in het menu bovenaan op **Admin**.

    ![Beheerder](./media/freshservice-tutorial/ic790814.png "beheerder")

5. Klik in de **Customer Portal** op **Security**.

    ![Beveiliging](./media/freshservice-tutorial/ic790815.png "Beveiliging")

6. Voer in de sectie **Security** de volgende stappen uit:

    ![Eenmalige aanmelding](./media/freshservice-tutorial/ic790816.png "Eenmalige aanmelding")

    a. Zet **Single Sign On** op ON.

    b. Selecteer **SAML SSO**.

    c. Plak in het tekstvak **SAML Login URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Plak in het tekstvak **Logout URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    e. Plak in het tekstvak **beveiliging certificaat vingerafdruk** de **vingerafdruk** waarde, die u eerder hebt gegenereerd.

    f. Klik op **Opslaan**.

### <a name="create-freshservice-test-user"></a>Een testgebruiker maken voor Freshservice

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij FreshService, moeten ze worden ingericht in FreshService. In het geval van FreshService is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de **FreshService** -bedrijfs site.

2. Klik in het menu bovenaan op **Admin**.

    ![Beheerder](./media/freshservice-tutorial/ic790814.png "beheerder")

3. Klik in de sectie **User Management** op **Requesters**.

    ![Aanvragers](./media/freshservice-tutorial/ic790818.png "Aanvragers")

4. Klik op **New Requester**.

    ![Nieuwe aanvragers](./media/freshservice-tutorial/ic790819.png "Nieuwe aanvragers")

5. Voer de volgende stappen uit in de sectie **New Requester**:

    ![Nieuwe aanvrager](./media/freshservice-tutorial/ic790820.png "Nieuwe aanvrager")  

    a. Typ in de tekstvakken **First Name** en **Email** kenmerken van een geldig Azure Active Directory-account dat u wilt inrichten.

    b. Klik op **Opslaan**.

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt.
    >  

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van FreshService-gebruikers accounts of Api's die worden geleverd door FreshService, gebruiken om Azure AD-gebruikers accounts in te richten.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Freshservice klikt, wordt u automatisch aangemeld bij de instantie van Freshservice waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Freshservice met Azure AD](https://aad.portal.azure.com/)