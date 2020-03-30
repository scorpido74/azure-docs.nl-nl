---
title: 'Zelfstudie: Azure Active Directory-integratie met Atlassian Cloud | Microsoft Documenten'
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
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c48d6f7d751e6d767921fad21e6213c6b1203b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384021"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Zelfstudie: Atlassian Cloud integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Atlassian Cloud integreren met Azure Active Directory (Azure AD). Wanneer u Atlassian Cloud integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Atlassian Cloud.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Atlassian Cloud met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Als je geen abonnement hebt, kun je [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proefperiode van een maand krijgen.
* Atlassian Cloud single sign-on (SSO) ingeschakeld abonnement.
* Als u eenmalige aanmelding van Security Assertion Markup Language (SAML) wilt instellen voor Atlassian Cloud-producten, moet u Atlassian Access instellen. Meer informatie over [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. 

* Atlassian Cloud ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Atlassian Cloud ondersteunt [automatische gebruikersinrichting en -deprovisioning](atlassian-cloud-provisioning-tutorial.md)
* Zodra u Atlassian Cloud hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
## <a name="adding-atlassian-cloud-from-the-gallery"></a>Atlassian Cloud toevoegen vanuit de galerie

Als u de integratie van Atlassian Cloud met Azure AD wilt configureren, voegt u Atlassian Cloud vanuit de galerie toe aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Atlassian Cloud** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Atlassian Cloud** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Atlassian Cloud met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Atlassian Cloud.

Als u Azure AD SSO wilt configureren en testen met Atlassian Cloud, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Atlassian Cloud SSO configureren](#configure-atlassian-cloud-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak Atlassian Cloud-testgebruiker](#create-atlassian-cloud-test-user)** - om een tegenhanger van B.Simon in Atlassian Cloud te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Atlassian Cloud-applicatie-toepassingen** de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://auth.atlassian.com/saml/<unique ID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Klik op **Extra URL's instellen**.

    d. In het tekstvak **Relaystatus** typt u een URL met de volgende notatie: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > De bovenstaande waarden zijn niet echt. Werk deze waarden bij met de werkelijke id en antwoord-URL. U krijgt deze echte waarden uit het **Atlassian Cloud SAML-configuratiescherm,** dat later wordt uitgelegd bij stap 7 van **Atlassian Cloud SSO configureren** in de zelfstudie.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > De waarde van de aanmeldings-URL is niet echt. Plak de waarde van de instantie die u gebruikt om u aan te melden bij de Atlassian Cloud-beheerportal.

    ![Eenmalige aanmelding configureren](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. De Atlassian Cloud-toepassing verwacht SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de Atlassian Cloud-toepassing wordt verwacht dat **nameidentifier** is toegewezen aan **user.mail**. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Atlassian Cloud instellen** de juiste URL('s) overeenkomstig wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Atlassian Cloud.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Atlassian Cloud**in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-atlassian-cloud-sso"></a>Atlassian Cloud SSO configureren

1. Als u de configuratie binnen Atlassian Cloud wilt automatiseren, moet u **de beveiligingsextensie Mijn apps Secure installeren** door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup Atlassian Cloud** en leidt u naar de Atlassian Cloud-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Atlassian Cloud. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Configuratie instellen](common/setup-sso.png)

1. Als u Atlassian Cloud handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich aan bij uw Atlassian Cloud-bedrijfssite als beheerder en voert u de volgende stappen uit:

1. U moet uw domein verifiëren voordat u eenmalige aanmelding configureert. Zie het document [Atlassian-domeinverificatie](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) voor meer informatie.

1. Selecteer in het linkerdeelvenster de optie > **BeveiligingsSAML-eenmalig aanmelding**. **Security** Abonneer u op Atlassian Identity Manager als u dat nog niet had gedaan.

    ![Eenmalige aanmelding configureren](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. Doe het volgende in het venster **SAML-configuratie toevoegen**:

    ![Eenmalige aanmelding configureren](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. Plak in het vak **Identiteitsprovider Entiteit** de **Azure AD-id** die u hebt gekopieerd vanuit de Azure-portal.

    b. Plak in het vak **Url van de identiteitsprovider** de **aanmeldings-URL** die u hebt gekopieerd vanuit de Azure-portal.

    c. Open het certificaat dat u uit Azure Portal hebt gedownload als TXT-bestand, kopieer de waarde (zonder de regels *Begin certificaat* en *Einde certificaat*) en plak deze in het vak **Openbaar X509-certificaat**.

    d. Klik op **Configuratie opslaan**.

1. Om er zeker van te zijn dat u de juiste URL's hebt ingesteld, werkt u de Azure AD-instellingen bij door het volgende te doen:

    ![Eenmalige aanmelding configureren](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. Kopieer in het SAML-venster de **SP-identiteits-id** en plak deze vervolgens in de Azure-portal onder Atlassian Cloud **Basic SAML-configuratie**in het vak **Id.**

    b. Kopieer in het SAML-venster de URL van de **SP-bevestigingsconsumentenservice** en plak deze vervolgens in de Azure-portal onder Atlassian Cloud **Basic SAML-configuratie**in het vak **URL van antwoord.** De aanmeldings-URL is de tenant-URL van uw Atlassian Cloud.

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

### <a name="test-sso"></a>Test SSO

Wanneer u de Atlassian Cloud-tegel selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de Atlassian Cloud waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Atlassian Cloud uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Atlassian Cloud beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)