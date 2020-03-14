---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met InVision | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en InVision.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02487206-30b0-4b1d-ae99-573c3d2ef9b0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/09/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63ec58711c5dd142064e2d5cb29ccaa9eb9c0ed9
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300653"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-invision"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met InVision

In deze zelf studie leert u hoe u InVision integreert met Azure Active Directory (Azure AD). Wanneer u InVision integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot InVision.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij InVision met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* InVision-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* InVision ondersteunt SSO die door **SP en IDP** is geïnitieerd
* Nadat u InVision hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-invision-from-the-gallery"></a>InVision toevoegen uit de galerie

Als u de integratie van InVision in azure AD wilt configureren, moet u InVision uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Invision** in het zoekvak.
1. Selecteer **Invision** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-invision"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor InVision

Azure AD SSO met InVision configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in InVision.

Als u Azure AD SSO wilt configureren en testen met InVision, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[INVISION SSO configureren](#configure-invision-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een Invision-test gebruiker](#create-invision-test-user)** -om een equivalent van B. Simon in Invision te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Invision** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.invisionapp.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.invisionapp.com//sso/auth`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.invisionapp.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van InVision-clients](mailto:support@invisionapp.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Invision instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan InVision.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Invision**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-invision-sso"></a>InVision SSO configureren

1. Meld u in een ander webbrowser venster aan bij de InVision-site als beheerder.

1. Klik op **team** en selecteer **instellingen**.

    ![InVision-configuratie](./media/invision-tutorial/config1.png)

1. Schuif omlaag naar **eenmalige aanmelding** en klik vervolgens op **wijzigen**.

    ![InVision-configuratie](./media/invision-tutorial/config3.png)

1. Voer op de pagina **eenmalige aanmelding** de volgende stappen uit:

    ![InVision-configuratie](./media/invision-tutorial/config4.png)

    a. **De wijziging vereist SSO voor elk lid van < account naam >** in **op**.

    b. Voer in het tekstvak **naam** de naam in zoals `azureadsso`.

    c. Voer de waarde voor de aanmeldings-URL in het tekstvak **URL voor aanmelden** in.

    d. Plak in het tekstvak voor de **Afmeldings-URL** de waarde **voor de afmeldings** -URL, die u hebt gekopieerd uit de Azure Portal.

    e. Open in het tekstvak **SAML-certificaat** het gedownloade **certificaat (base64)** in Klad blok, kopieer de inhoud en plak deze in het tekstvak SAML-certificaat.

    f. Selecteer **SHA-256** in de vervolg keuzelijst voor het **hash-algoritme**.

    g. Voer de juiste naam in voor het label voor de **SSO-knop**.

    h. Zorg ervoor **dat just-in-time-inrichting is toegestaan** op.

    i. Klik op **Update**.

### <a name="create-invision-test-user"></a>InVision-test gebruiker maken

1. Meld u in een ander webbrowser venster aan bij de InVision-site als beheerder.

1. Klik op **team** en selecteer **personen**.

    ![InVision-configuratie](./media/invision-tutorial/config2.png)

1. Klik op het **pictogram +** om een nieuwe gebruiker toe te voegen.

    ![InVision-configuratie](./media/invision-tutorial/user1.png)

1. Voer het e-mail adres van de gebruiker in en klik op **volgende**.

    ![InVision-configuratie](./media/invision-tutorial/user2.png)

1. Controleer het e-mail adres en klik vervolgens op **uitnodigen**.

    ![InVision-configuratie](./media/invision-tutorial/user3.png)

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel InVision in het toegangs venster klikt, moet u automatisch worden aangemeld bij de InVision waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer InVision met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [InVision beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)