---
title: 'Zelf studie: integratie Azure Active Directory met Atlassian Cloud | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3a54b096019e9e38bc800ae313016a430062dab
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964307"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Zelf studie: atlassian-Cloud integreren met Azure Active Directory

In deze zelf studie leert u hoe u Atlassian Cloud kunt integreren met Azure Active Directory (Azure AD). Wanneer u Atlassian-Cloud integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de cloud van atlassian.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Atlassian Cloud met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proef versie van één maand ontvangen.
* Atlassian-abonnement met eenmalige aanmelding voor de Cloud (SSO).
* Als u eenmalige aanmelding van Security Assertion Markup Language (SAML) wilt instellen voor Atlassian Cloud-producten, moet u Atlassian Access instellen. Meer informatie over [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. 

* Atlassian Cloud ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Atlassian Cloud ondersteunt [automatische gebruikers inrichting en](atlassian-cloud-provisioning-tutorial.md) het ongedaan maken van de inrichting

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Atlassian Cloud toevoegen vanuit de galerie

Als u de integratie van Atlassian Cloud met Azure AD wilt configureren, voegt u Atlassian Cloud vanuit de galerie toe aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Atlassian Cloud** in het zoekvak.
1. Selecteer **Atlassian Cloud** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO met Atlassian Cloud configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Atlassian Cloud.

Als u Azure AD SSO wilt configureren en testen met Atlassian Cloud, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Atlassian Cloud SSO configureren](#configure-atlassian-cloud-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een Atlassian-test gebruiker](#create-atlassian-cloud-test-user)** voor de Cloud, zodat deze een soort is van B. Simon in de Atlassian-Cloud die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Atlassian Cloud** Application Integration de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://auth.atlassian.com/saml/<unique ID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Klik op **Extra URL's instellen**.

    d. In het tekstvak **Relaystatus** typt u een URL met de volgende notatie: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > De bovenstaande waarden zijn niet echt. Werk deze waarden bij met de werkelijke id en antwoord-URL. Deze echte waarden worden opgehaald uit het **configuratie scherm Atlassian Cloud SAML** , dat later wordt uitgelegd in de **eenmalige aanmelding Atlassian Cloud configureren** van zelf studie.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > De waarde van de aanmeldings-URL is niet echt. Plak de waarde van het exemplaar dat u gebruikt om u aan te aanmelden bij de Atlassian-cloud Beheer Portal.

    ![Eenmalige aanmelding configureren](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. De Atlassian Cloud-toepassing verwacht SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de Atlassian Cloud-toepassing wordt verwacht dat **nameidentifier** is toegewezen aan **user.mail**. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![image](common/edit-attribute.png)

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Atlassian Cloud instellen** de juiste URL('s) overeenkomstig wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot de Atlassian-Cloud.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Atlassian Cloud**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-atlassian-cloud-sso"></a>Atlassian Cloud SSO configureren

1. Als u de configuratie in Atlassian Cloud wilt automatiseren, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

1. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op **Setup Atlassian Cloud** wordt u naar de Cloud toepassing Atlassian geleid. Geef de beheerders referenties op om u aan te melden bij de Atlassian-Cloud. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

1. Als u Atlassian-Cloud hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw Atlassian-cloud site als beheerder en voert u de volgende stappen uit:

1. U moet uw domein verifiëren voordat u eenmalige aanmelding configureert. Zie het document [Atlassian-domeinverificatie](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) voor meer informatie.

1. Selecteer in het linkerdeel venster de optie **beveiliging** > **SSO eenmalige aanmelding**. Abonneer u op Atlassian Identity Manager als u dat nog niet had gedaan.

    ![Eenmalige aanmelding configureren](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. Doe het volgende in het venster **SAML-configuratie toevoegen**:

    ![Eenmalige aanmelding configureren](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. Plak de **Azure ad-id** die u hebt gekopieerd uit de Azure Portal in het vak ID **-provider entiteit-** id's.

    b. Plak in het vak SSO-URL van de **ID-provider** de AANMELDINGS- **URL** die u hebt gekopieerd uit de Azure Portal.

    c. Open het certificaat dat u uit Azure Portal hebt gedownload als TXT-bestand, kopieer de waarde (zonder de regels *Begin certificaat* en *Einde certificaat*) en plak deze in het vak **Openbaar X509-certificaat**.

    d. Klik op **Configuratie opslaan**.

1. Om er zeker van te zijn dat u de juiste URL's hebt ingesteld, werkt u de Azure AD-instellingen bij door het volgende te doen:

    ![Eenmalige aanmelding configureren](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. Kopieer in het SAML-venster de **id** van de SP id en plak deze in de Azure portal onder Atlassian Cloud **Basic SAML Configuration**in het vak **id** .

    b. Kopieer in het SAML-venster de **service-URL van de SP Assertion Consumer** en plak deze in de Azure Portal, onder Atlassian Cloud **Basic SAML Configuration**, in het vak **antwoord-URL** . De aanmeldings-URL is de tenant-URL van uw Atlassian Cloud.

    > [!NOTE]
    > Als u een bestaande klant bent, selecteert u na het bijwerken van de **SP-id** en de **URL voor Assertion Consumer Service van de SP** in Azure Portal de optie **Ja, configuratie bijwerken**. Als u een nieuwe klant bent, kunt u deze stap overslaan.

### <a name="create-atlassian-cloud-test-user"></a>Een Atlassian Cloud-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Atlassian Cloud, richt u in Atlassian Cloud de gebruikersaccounts op de volgende manier handmatig in:

1. In het deelvenster **Beheer** selecteert u **Gebruikers**.

    ![De koppeling Gebruikers in Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Als u in Atlassian Cloud een gebruiker wilt maken, selecteert u **Gebruiker uitnodigen**.

    ![Een Atlassian Cloud-gebruiker maken](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. In het vak **E-mailadres** voert u het e-mailadres van de gebruiker in. Wijs dan toegang tot de toepassing toe.

    ![Een Atlassian Cloud-gebruiker maken](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Als u per e-mail een uitnodiging naar de gebruiker wilt verzenden, selecteert u **Gebruikers uitnodigen**. Er wordt een e-mailuitnodiging naar de gebruiker verzonden. Als de gebruiker de uitnodiging heeft geaccepteerd, wordt deze actief in het systeem.

> [!NOTE]
> U kunt ook bulksgewijs gebruikers maken door de optie **Bulksgewijs maken** in het gedeelte **Gebruikers** te selecteren.

### <a name="test-sso"></a>SSO testen

Wanneer u de Atlassian-Cloud tegel in het toegangs venster selecteert, moet u automatisch worden aangemeld bij de Atlassian-Cloud waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Atlassian Cloud met Azure AD](https://aad.portal.azure.com/)
