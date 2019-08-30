---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met zoomen | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Zoom.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0d5a87d4723bcc21b75db1b31ada72823abdf02
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171411"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met zoomen

In deze zelf studie leert u hoe u zoomen integreert met Azure Active Directory (Azure AD). Wanneer u inzoomen integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot inzoomen.
* Stel in dat gebruikers automatisch kunnen worden aangemeld om te zoomen met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Inzoomen op eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Zoom ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-zoom-from-the-gallery"></a>Zoom toevoegen vanuit de galerie

Voor het configureren van de integratie van Zoom in Azure AD moet u Zoom uit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **toevoegen vanuit de galerie** de tekst inzoomen in het zoekvak.
1. Selecteer **zoomen** in het deel venster resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor inzoomen

Azure AD SSO configureren en testen met zoomen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in-/uitzoomen.

Als u Azure AD SSO wilt configureren en testen met zoomen, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Inzoomen configureren](#configure-zoom-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een gebruiker](#create-zoom-test-user)** met de zoom functie voor het maken van een tegen hanger van B. Simon in-/uitzoomen dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
3. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **zoomen** op toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.zoom.us`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `<companyname>.zoom.us`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [Zoom-ondersteuningsteam](https://support.zoom.us/hc/en-us) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/certificatebase64.png)

1. Kopieer de gewenste URL ('s) op basis van uw vereiste in het gedeelte **zoomen instellen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

> [!NOTE]
> Zie voor meer informatie over het configureren van de rol in azure AD [de claim configureren die is uitgegeven in het SAML-token voor zakelijke toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

> [!NOTE]
> In inzoomen kan een groepclaim worden verwacht in de SAML-nettolading. Als u groepen hebt gemaakt, neemt u contact op met het ondersteunings [team voor client ondersteuning](https://support.zoom.us/hc/en-us) met de groeps informatie zodat de groeps informatie aan hun einde kan worden geconfigureerd. U moet ook de object-ID opgeven om het [client ondersteunings team](https://support.zoom.us/hc/en-us) te zoomen zodat de object-id aan hun einde kan worden geconfigureerd. Zie [zoomen met Azure configureren](https://support.zoom.us/hc/en-us/articles/115005887566)om de object-id op te halen.

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** aan de bovenkant van het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
    1. Voer in het veld **Naam** `B.Simon` in.  
    1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
    1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
    1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen voor inzoomen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen deoptie Inzoomen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

    ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-zoom-sso"></a>Inzoomen configureren

1. Meld u in een ander browser venster aan bij uw inzoom-bedrijfs site als beheerder.

2. Klik op het tabblad **Eenmalige aanmelding**.

    ![Tabblad Eenmalige aanmelding](./media/zoom-tutorial/ic784700.png "Eenmalige aanmelding")

3. Klik op het tabblad **Beveiligingsbeheer** en ga vervolgens naar de instellingen voor **Eenmalige aanmelding**.

4. Voer in de sectie Eenmalige aanmelding de volgende stappen uit:

    ![Sectie Eenmalige aanmelding](./media/zoom-tutorial/ic784701.png "Eenmalige aanmelding")

    a. Plak in het tekstvak **Aanmeldingspagina-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    b. Voor de **URL-** waarde van de afmeldings pagina moet u naar de Azure portal gaan en op **Azure Active Directory** aan de linkerkant klikken en vervolgens naar **app-registraties**navigeren.

    ![De Azure Active Directory-knop](./media/zoom-tutorial/appreg.png)

    c. Klik op **eind punten**

    ![De knop eind punt](./media/zoom-tutorial/endpoint.png)

    d. Kopieer het **SAML-P-AFmeldings eindpunt** en plak het in het tekstvak URL voor de afmeldings **pagina** .

    ![De knop eind punt kopiëren](./media/zoom-tutorial/endpoint1.png)

    e. Open het base-64 gecodeerde certificaat in Kladblok, kopieer de inhoud ervan naar het klembord en plak het in het tekstvak **Id-providercertificaat**.

    f. Plak in het tekstvak **Uitgever** de waarde van de **Azure ad-id** die u van Azure Portal hebt gekopieerd. 

    g. Klik op **Opslaan**.

    > [!NOTE]
    > Ga naar de Zoom-documentatie voor meer informatie[https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Zoom-testgebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij inzoomen, moeten ze worden in-/uitzoomen. In het geval van Zoom is inrichten een handmatige taak.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

1. Meld u aan bij uw inzoom-bedrijfs site als beheerder.

2. Klik op het tabblad **Accountbeheer** en klik vervolgens op **Gebruikersbeheer**.

3. Klik in de sectie Gebruikersbeheer op **Gebruikers toevoegen**.

    ![Gebruikersbeheer](./media/zoom-tutorial/ic784703.png "Gebruikersbeheer")

4. Voer op de pagina **Gebruikers toevoegen** de volgende stappen uit:

    ![Gebruikers toevoegen](./media/zoom-tutorial/ic784704.png "Gebruikers toevoegen")

    a. Selecteer als **Gebruikerstype**, **Standaard**.

    b. Typ in het tekstvak **E-mails** het e-mailadres van een geldig Azure AD-account dat u wilt inrichten.

    c. Klik op **Toevoegen**.

> [!NOTE]
> U kunt alle andere hulpprogramma's of API's voor het maken van Zoom-gebruikersaccounts gebruiken die worden geleverd door Zoom voor het inrichten van Azure Active Directory-gebruikersaccounts.

## <a name="test-sso"></a>SSO testen 

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u op de tegel Zoom in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Zoom waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer in te zoomen met Azure AD](https://aad.portal.azure.com/)
