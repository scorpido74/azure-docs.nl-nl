---
title: 'Zelf studie: integratie Azure Active Directory met Sage Intacct | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Sage Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b28390dba009226d493f5bfc6a5270b067f5bba0
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2019
ms.locfileid: "73570468"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Zelf studie: Sage Intacct integreren met Azure Active Directory

In deze zelf studie leert u hoe u de Sage Intacct integreert met Azure Active Directory (Azure AD). Wanneer u Sage Intacct integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Sage Intacct.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij een Sage Intacct met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Sage Intacct-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Sage Intacct ondersteunt door **IDP** GEÏNITIEERDe SSO

## <a name="adding-sage-intacct-from-the-gallery"></a>Sage Intacct toevoegen vanuit de galerie

Als u de integratie van Sage Intacct in azure AD wilt configureren, moet u Sage Intacct vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **Sage Intacct** in het zoekvak.
1. Selecteer **Sage Intacct** uit het paneel resultaten en voeg de app vervolgens toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sage-intacct"></a>Eenmalige aanmelding van Azure AD voor Sage Intacct configureren en testen

Azure AD SSO met een Sage Intacct configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Sage Intacct.

Als u Azure AD SSO wilt configureren en testen met Sage Intacct, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
2. **[Sage INTACCT SSO configureren](#configure-sage-intacct-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Sage Intacct-test gebruiker](#create-sage-intacct-test-user)** -om een tegen hanger te hebben van B. Simon in Sage Intacct dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **Sage Intacct** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    Typ in het tekstvak **antwoord-URL** een URL: `https://www.intacct.com/ia/acct/sso_response.phtml`

1. Sage Intacct-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op pictogram **bewerken** om het dialoog venster gebruikers kenmerken te openen...

    ![image](common/edit-attribute.png)

1. Behalve hierboven verwachtte Sage Intacct-toepassingen nog enkele kenmerken die kunnen worden door gegeven aan het SAML-antwoord. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:

    | Naam  |  Bronkenmerk|
    | ---------------| --------------- |
    | Bedrijfsnaam | **Intacct-bedrijfs-ID voor Sage** |
    | naam | De waarde moet gelijk zijn aan de Sage Intacct **-gebruikers-id**, die u invoert in de sectie gebruiker voor het maken van de **Sage Intacct-test**, die verderop in de zelf studie wordt beschreven |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | De waarde moet gelijk zijn aan de **gebruikers-id**van de Sage INTACCT Federated SSO, die u in de **sectie gebruiker voor het maken van Sage Intacct-test**hebt opgegeven, die verderop in de zelf studie wordt beschreven |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Sage Intacct instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Sage Intacct.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Sage Intacct**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sage-intacct-sso"></a>Sage Intacct SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw Sage Intacct-bedrijfs site.

1. Klik op het tabblad **bedrijf** en klik vervolgens op **Bedrijfs gegevens**.

    ![Company] (Bedrijf)(./media/intacct-tutorial/ic790037.png "Company") (Bedrijf)

1. Klik op het tabblad **beveiliging** en klik vervolgens op **bewerken**.

    ![Beveiliging](./media/intacct-tutorial/ic790038.png "Beveiliging")

1. Voer de volgende stappen uit in de sectie **eenmalige aanmelding (SSO)** :

    Eenmalige aanmelding ![bij](./media/intacct-tutorial/ic790039.png "eenmalige aanmelding")

    a. Selecteer **eenmalige aanmelding inschakelen**.

    b. Als **ID-provider type**, selecteert u **SAML 2,0**.

    c. Plak in het tekstvak **URL van uitgever** de waarde van de **Azure ad-id**die u van Azure Portal hebt gekopieerd.

    d. Plak in het tekstvak **Login URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    e. Open uw met **base 64** versleutelde certificaat in Klad blok, kopieer de inhoud ervan naar het klem bord en plak het in het vak **certificaat** .

    f. Klik op **Opslaan**.

### <a name="create-sage-intacct-test-user"></a>Een Sage Intacct-test gebruiker maken

Als u Azure AD-gebruikers wilt instellen zodat ze zich kunnen aanmelden bij Sage Intacct, moeten ze worden ingericht in Sage Intacct. Voor Sage Intacct is inrichten een hand matige taak.

**Voer de volgende stappen uit om gebruikers accounts in te richten:**

1. Meld u aan bij uw **Sage Intacct** -Tenant.

1. Klik op het tabblad **bedrijf** en klik vervolgens op **gebruikers**.

    ![Gebruikers](./media/intacct-tutorial/ic790041.png "Gebruikers")

1. Klik op het tabblad **toevoegen** .

    ![Add](./media/intacct-tutorial/ic790042.png "Add")

1. Voer de volgende stappen uit in de sectie **gebruikers gegevens** :

    (./media/intacct-tutorial/ic790043.png "Gebruikers gegevens") voor ![gebruikers gegevens]

    a. Voer de **gebruikers-id**, de **Achternaam**, de **voor naam**, het **e-mail adres**, de **titel**en de **telefoon** van een Azure ad-account in die u in de sectie **gebruikers gegevens** wilt inrichten.

    > [!NOTE]
    > Zorg ervoor dat de **gebruikers-id** in de bovenstaande scherm afbeelding en de **bron kenmerk** waarde die is toegewezen aan het **naam** kenmerk in het gedeelte **gebruikers kenmerken** in het Azure Portal hetzelfde moeten zijn.

    b. Selecteer de **beheerders bevoegdheden** van een Azure ad-account dat u wilt inrichten.

    c. Klik op **Opslaan**. 
    
    d. De houder van het Azure AD-account ontvangt een e-mail en volgt een koppeling om het account te bevestigen voordat het actief wordt.

1. Klik op het tabblad **eenmalige aanmelding** en zorg ervoor dat de id van de **federatieve SSO-gebruiker** in onderstaande scherm afbeelding en de **bron kenmerk** waarde die is toegewezen aan de `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` in het gedeelte **gebruikers kenmerken** in de Azure Portal hetzelfde moet zijn.

    (./media/intacct-tutorial/ic790044.png "Gebruikers gegevens") voor ![gebruikers gegevens]

> [!NOTE]
> Voor het inrichten van Azure AD-gebruikers accounts kunt u andere Sage Intacct-hulpprogram ma's voor het maken van gebruikers accounts of Api's gebruiken die worden geleverd door Sage Intacct.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Sage Intacct-tegel in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Sage Intacct waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

