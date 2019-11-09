---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Check Point CloudGuard Dome9 Arc | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Check Point CloudGuard Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b46ac34db21576c7e2de2271a468e3e782ff6aa9
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885355"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Check Point CloudGuard Dome9 Arc

In deze zelf studie leert u hoe u Check Point CloudGuard Dome9 Arc integreert met Azure Active Directory (Azure AD). Wanneer u Check Point CloudGuard Dome9 Arc integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Check Point CloudGuard Dome9 Arc.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld om Point CloudGuard Dome9 Arc te controleren met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Check Point CloudGuard Dome9-abonnement voor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Check Point CloudGuard Dome9 Arc ondersteunt **SP en IDP** GEÏNITIEERDe SSO

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Controle punt CloudGuard Dome9 Arc toevoegen vanuit de galerie

Als u de integratie van Check Point CloudGuard Dome9 Arc wilt configureren in azure AD, moet u het selectie punt CloudGuard Dome9 Arc toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Check Point CloudGuard Dome9 Arc** in het zoekvak.
1. Selecteer **controle punt CloudGuard Dome9 boog** van het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-check-point-cloudguard-dome9-arc"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Check Point CloudGuard Dome9 Arc

Configureer en test Azure AD SSO met Check Point CloudGuard Dome9 Arc met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in check Point CloudGuard Dome9 Arc.

Als u Azure AD SSO wilt configureren en testen met Check Point CloudGuard Dome9 Arc, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. Het **[controle punt CloudGuard Dome9 Arc SSO configureren](#configure-check-point-cloudguard-dome9-arc-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak Check Point CloudGuard Dome9 ARC test User](#create-check-point-cloudguard-dome9-arc-test-user)** -to een soort equivalent van B. Simon in check Point CloudGuard Dome9 Arc dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **Check Point CloudGuard Dome9 Arc** Application Integration de sectie **Manage (beheren** ) en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL: `https://secure.dome9.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke antwoord-URL en aanmeldings-URL. U krijgt de `<company name>` waarde uit de sectie **Check Point CloudGuard Dome9 Arc SSO configureren** , die verderop in de zelf studie wordt beschreven. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Check Point CloudGuard Dome9 Arc-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/edit-attribute.png)

1. In aanvulling op het bovenstaande wordt met Check Point CloudGuard Dome9 Arc Application nog maar weinig kenmerken door gegeven aan het SAML-antwoord dat hieronder wordt weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereiste.
    
    | Naam |  Bronkenmerk|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    >[!NOTE]
    >Klik [hier](https://docs.microsoft.com/azure/active-directory/saas-apps/apptio-tutorial) voor meer informatie over het maken van rollen in azure AD.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer de gewenste URL ('s) op basis van uw vereiste op de **CloudGuard Dome9 Arc instellen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot Check Point CloudGuard Dome9 Arc.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Check Point CloudGuard Dome9 Arc**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Controle punt CloudGuard Dome9 Arc SSO configureren

1. Als u de configuratie wilt automatiseren binnen Check Point CloudGuard Dome9 Arc, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op **controle punt voor installatie van CloudGuard Dome9 Arc** wordt u doorgestuurd naar de toepassing Check Point CloudGuard Dome9 Arc. Geef de beheerders referenties op om u aan te melden bij Check Point CloudGuard Dome9 Arc. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-6 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u controle punt CloudGuard Dome9-boog hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw locatie van uw Check Point CloudGuard Dome9 Arc-bedrijf als beheerder en voert u de volgende stappen uit:

2. Klik op **Profile Settings** in de rechterbovenhoek en vervolgens op **Account Settings**. 

    ![Controle punt CloudGuard Dome9 Arc-configuratie](./media/dome9arc-tutorial/configure1.png)

3. Navigeer naar **SSO** en klik vervolgens op **ENABLE**.

    ![Controle punt CloudGuard Dome9 Arc-configuratie](./media/dome9arc-tutorial/configure2.png)

4. Voer in de sectie SSO Configuration de volgende stappen uit:

    ![Controle punt CloudGuard Dome9 Arc-configuratie](./media/dome9arc-tutorial/configure3.png)

    a. Voer in het tekstvak **Account ID** de bedrijfsnaam in. Deze waarde moet worden gebruikt in de URL voor **beantwoorden** en **Aanmelden** die wordt vermeld in de sectie **basis configuratie van SAML** van Azure Portal.

    b. Plak in het tekstvak **Uitgever** de waarde van de **Azure ad-id**, die u als Azure Portal hebt gekopieerd.

    c. Plak in het tekstvak **Idp endpoint url** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Open in Kladblok het met Base64 gecodeerde certificaat dat u hebt gedownload, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **X.509 certificate**.

    e. Klik op **Opslaan**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Controle punt CloudGuard Dome9 ARC test gebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Check Point CloudGuard Dome9 Arc, moeten ze worden ingericht in de toepassing. Check Point CloudGuard Dome9 Arc ondersteunt just-in-time-inrichting, maar de gebruiker moet een specifieke **rol** selecteren en deze toewijzen aan de gebruiker.

   >[!Note]
   >Voor het maken van **rollen** en andere gegevens [raadpleegt u het ondersteunings team van het CloudGuard Dome9 Arc-client](mailto:Dome9@checkpoint.com).

**Voor het handmatig inrichten van een gebruikersaccount moet u de volgende stappen uitvoeren:**

1. Meld u aan bij uw Check Point CloudGuard Dome9 Arc-bedrijfs site als beheerder.

2. Klik op **Users & Roles** en vervolgens op **Users**.

    ![Werknemer toevoegen](./media/dome9arc-tutorial/user1.png)

3. Klik op **ADD USER**.

    ![Werknemer toevoegen](./media/dome9arc-tutorial/user2.png)

4. Voer in de sectie **Create User** de volgende stappen uit:

    ![Werknemer toevoegen](./media/dome9arc-tutorial/user3.png)

    a. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld B.Simon@contoso.com.

    b. Typ in het tekstvak **voor naam** de voor naam van de gebruiker zoals B.

    c. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, bijvoorbeeld Simon.

    d. Zet **SSO User** op **On**.

    e. Klik op **MAKEN**.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel controle punt CloudGuard Dome9 op de boog in het toegangs venster klikt, moet u automatisch worden aangemeld bij het controle punt CloudGuard Dome9 Arc waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Check Point CloudGuard Dome9 Arc uit met Azure AD](https://aad.portal.azure.com/)