---
title: 'Zelfstudie: Azure Active Directory-integratie met Atlassian Cloud | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Atlassian Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/04/2020
ms.author: jeedes
ms.openlocfilehash: ca75aea393e4850eb302cb03914296d7c1eaa951
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88517645"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Zelfstudie: Atlassian Cloud met Azure Active Directory integreren

In deze zelfstudie leert u hoe u Atlassian Cloud integreert met Azure Active Directory (Azure AD). Wanneer u Atlassian Cloud integreert met Azure AD, kunt u:

* Beheren in Azure AD wie toegang heeft tot Atlassian Cloud.
* Ervoor zorgen dat uw gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Atlassian Cloud.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Abonnement op Atlassian Cloud waarvoor eenmalige aanmelding (SSO) is ingeschakeld.
* Als u eenmalige aanmelding van Security Assertion Markup Language (SAML) wilt instellen voor Atlassian Cloud-producten, moet u Atlassian Access instellen. Meer informatie over [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. 

* Atlassian Cloud ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Atlassian Cloud ondersteunt [automatisch inrichten van gebruikers](atlassian-cloud-provisioning-tutorial.md)
* Zodra u Atlassian Cloud hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Atlassian Cloud toevoegen vanuit de galerie

Als u de integratie van Atlassian Cloud met Azure AD wilt configureren, voegt u Atlassian Cloud vanuit de galerie toe aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het zoekvak in de sectie **Toevoegen uit de galerie** **Atlassian Cloud**.
1. Selecteer **Atlassian Cloud** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Atlassian Cloud met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Atlassian Cloud.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Atlassian Cloud te configureren en te testen:

1. **[Configureer Azure AD met eenmalige aanmelding voor Atlassian Cloud](#configure-azure-ad-sso)** : om uw gebruikers in staat te stellen om eenmalige aanmelding met SAML voor Azure AD met Atlassian Cloud te gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Een testgebruiker voor Atlassian Cloud maken](#create-atlassian-cloud-test-user)** : een tegenhanger voor B.Simon definiëren in Atlassian Cloud die is gekoppeld aan de Azure AD-versie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Als u de configuratie in Atlassian Cloud wilt automatiseren, moet u de **browseruitbreiding Beveiligde aanmelding voor Mijn Apps** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **Atlassian Cloud instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Atlassian Cloud-toepassing. Geef hier de referenties voor een beheerder op om u aan te melden bij Atlassian Cloud. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u Atlassian Cloud handmatig wilt instellen, meldt u zich aan bij de Atlassian-bedrijfswebsite als beheerder en voert u de volgende stappen uit.

1. Ga voordat u begint naar uw Atlassian-productinstantie en kopieer/sla de URL van de instantie op
   > [!NOTE]
   > De URL moet passen in het `https://<instancename>.atlassian.net`-patroon

   ![image](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. Open de [Atlassian-beheerportal](https://admin.atlassian.com/) en klik op de naam van uw organisatie

   ![image](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. U moet uw domein verifiëren voordat u eenmalige aanmelding configureert. Zie het document [Atlassian-domeinverificatie](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) voor meer informatie.
1. Selecteer in het scherm van de Atlassian-beheerportal de optie **Beveiliging** in de linkerlade

   ![image](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. Selecteer in het scherm van de Atlassian-beheerportal de optie **Eenmalige aanmelding met SAML** in de linkerlade

   ![image](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. Klik op **SAML-configuratie toevoegen** en houd de pagina open

   ![image](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![image](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Atlassian Cloud** de sectie **Beheren** en selecteer **Eenmalige aanmelding instellen**.

   ![image](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.

   ![image](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. Schuif op de pagina **Eenmalige aanmelding instellen met SAML** omlaag naar **Atlassian Cloud instellen**
   
   a. Klik op **Configuratie-URL's**

   ![image](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. Kopieer de waarde van de **Azure AD-id** uit Azure Portal en plak deze in het tekstvak **Entiteits-id van id-provider** in Atlassian
   
   c. Kopieer de waarde van de **aanmeldings-URL** uit Azure Portal en plak deze in het tekstvak **SSO-URL van id-provider** in Atlassian

   ![image](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![image](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![image](./media/atlassian-cloud-tutorial/certificate.png)

   ![image](./media/atlassian-cloud-tutorial/certificate-1.png)

1. De SAML-configuratie **Toevoegen/Opslaan** in Atlassian

1. Als u de toepassing wilt configureren in de door **IDP** geïnitieerde modus, bewerkt u de sectie **Basis SAML-configuratie** van de pagina **Eenmalige aanmelding met SAML instellen** in Azure en opent u de pagina **Eenmalige aanmelding met SAML** in de beheerportal van Atlassian

   a. Kopieer de waarde van de **SP-entiteits-id** uit Atlassian, plak deze in het vak **Id (entiteits-id)** in Azure en stel deze als standaard in
   
   b. Kopieer de waarde van **URL voor Assertion Consumer Service** uit Atlassian, plak deze in de **Antwoord-URL (URL voor Assertion Consumer Service)** in Azure en stel deze als standaard in
   
   c. Kopieer de waarde van uw **instantie-URL** die u in stap 1 hebt gekopieerd en plak deze in het vak **Relaystatus** in Azure

   ![image](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![image](./media/atlassian-cloud-tutorial/edit-button.png)

   ![image](./media/atlassian-cloud-tutorial/urls.png)
   
1. Als u de toepassing wilt configureren in de door **SP** geïnitieerde modus, bewerkt u de sectie **Basis SAML-configuratie** van de pagina **Eenmalige aanmelding met SAML instellen** in Azure. Kopieer uw **instantie-URL** (uit stap 1) en plak deze in het vak **Aanmeldings-URL** in Azure

   ![image](./media/atlassian-cloud-tutorial/edit-button.png)

   ![image](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. De Atlassian Cloud-toepassing verwacht SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. U kunt de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken. 

   ![image](./media/atlassian-cloud-tutorial/default-attributes.png)
   
   1. Kenmerktoewijzing voor een Azure AD-Tenant met een Office 365-licentie
      
      a. Klik op de claim van de **unieke gebruikers-id (naam-id)**

      ![image](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. Atlassian Cloud verwacht dat de **nameidentifier** (**unieke gebruikers-id**) moet worden toegewezen aan het e-mailadres van de gebruiker (**user.email**). Bewerk het **bronkenmerk** en wijzig dit in **user.mail**. Sla de wijzigingen op in de claim.

      ![image](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. De definitieve kenmerktoewijzingen moeten er als volgt uitzien.

      ![image](common/default-attributes.png)
      
   1. Kenmerktoewijzing voor een Azure AD-tenant met een Office 365-licentie 

      a. Klik op de claim **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

      ![image](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. Azure vult het kenmerk **user.mail** niet in voor gebruikers die zijn gemaakt in Azure AD-tenants zonder Office 365-licenties en slaat de e-mail voor dergelijke gebruikers op in het kenmerk **userprincipalname**. Atlassian Cloud verwacht dat de **nameidentifier** (**unieke gebruikers-id**) wordt toegewezen aan het e-mailadres van de gebruiker (**user.userprincipalname**).  Bewerk het **bronkenmerk** en wijzig dit in **user.userprincipalname**. Sla de wijzigingen op in de claim.

      ![image](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. De definitieve kenmerktoewijzingen moeten er als volgt uitzien.

      ![image](common/default-attributes.png)
     
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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Atlassian Cloud.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Atlassian Cloud** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](./media/atlassian-cloud-tutorial/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

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

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u in het toegangsvenster de tegel Atlassian Cloud selecteert, wordt u automatisch aangemeld bij het exemplaar van Atlassian Cloud waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Atlassian Cloud met Azure AD](https://aad.portal.azure.com/)

- [Atlassian Cloud beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)