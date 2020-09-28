---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Adobe Creative | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Adobe Creative Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/14/2020
ms.author: jeedes
ms.openlocfilehash: 48009246dd491ca3f4080cf85036c66605a73272
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707005"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Adobe Creative Cloud

> [!NOTE]
> In dit artikel wordt de aangepaste op SAML gebaseerde configuratie van de Adobe-beheerconsole voor Azure Active Directory (Azure AD) beschreven. Voor nieuwe configuraties wordt u aangeraden de [Azure AD-connector](https://helpx.adobe.com/enterprise/using/sso-setup-azure.html) te gebruiken. De Azure AD-connector kan in enkele minuten worden ingesteld en verkort het proces van de domeinclaim, de installatie van eenmalige aanmelding en de gebruikerssynchronisatie.

In deze zelfstudie leert u hoe u Adobe Creative Cloud integreert met Azure Active Directory (Azure AD). Wanneer u Adobe Creative Cloud integreert met Azure AD, kunt u:

* In Azure AD beheren wie toegang tot Adobe Creative Cloud heeft.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Adobe Creative Cloud.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement op Adobe Creative Cloud waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Adobe Creative Cloud ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* Zodra u Adobe Creative Cloud hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adobe Creative Cloud toevoegen vanuit de galerie

Om de integratie van Adobe Creative Cloud met Azure AD te configureren, moet u Adobe Creative Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **Adobe Creative Cloud** in het zoekvak.
1. Selecteer **Adobe Creative Cloud** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-adobe-creative-cloud"></a>Eenmalige aanmelding van Azure AD voor Adobe Creative Cloud configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Adobe Creative Cloud met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Adobe Creative Cloud.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Adobe Creative Cloud te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Adobe Creative Cloud configureren](#configure-adobe-creative-cloud-sso)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
    1. **[Een testgebruiker voor Adobe Creative Cloud maken](#create-adobe-creative-cloud-test-user)** : als u een tegenhanger van B.Simon in Adobe Creative Cloud wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Adobe Creative Cloud** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL: `https://adobe.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > De id-waarde is niet echt. Volg de instructies in stap 4 van de sectie **Eenmalige aanmelding bij Adobe Creative Cloud configureren**. Zodoende kunt u **XML-bestand met federatieve metagegevens** openen en de waarde voor de entiteits-id ophalen. Deze waarde kunt u vervolgens gebruiken als id-waarde in de configuratie van Azure AD. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De Adobe Creative Cloud-toepassing verwacht SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/edit-attribute.png)

1. Bovendien verwacht Adobe Creative Cloud nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |

    > [!NOTE]
    > De waarde voor de claim Email kan alleen worden gevuld in het SAML-antwoord als gebruikers beschikken over een geldige licentie voor Microsoft 365 ExO.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve gegevens** en selecteer **Downloaden** om het certificaat te downloaden en vervolgens op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in het gedeelte **Adobe Creative Cloud instellen** de juiste URL('s) op basis van wat u nodig hebt.

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

In dit gedeelte geeft u B.Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Adobe Creative Cloud.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Adobe Creative Cloud** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-adobe-creative-cloud-sso"></a>Eenmalige aanmelding bij Adobe Creative Cloud configureren

1. Meld u in een ander browservenster als systeembeheerder aan bij [Adobe Admin Console](https://adminconsole.adobe.com).

1. Ga naar **Instellingen** in de bovenste navigatiebalk en kies **Identiteit**. De lijst met mappen wordt geopend. Selecteer de gewenste federatieve map.

1. Selecteer op de pagina **Mapgegevens** de optie **Configureren**.

1. Kopieer de entiteits-id en de ACS URL (Assertion Consumer Service URL of antwoord-URL). Geef in Azure Portal de URL's op in de juiste velden.

    ![Eenmalige aanmelding aan de zijde van de app configureren](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Gebruik in het dialoogvenster **App-instellingen configureren** de waarde voor de Entiteits-id die u van Adobe hebt ontvangen voor **Id**.

    b. Gebruik de waarde voor de ACS URL (Assertion Consumer Service URL) die u van Adobe hebt ontvangen voor **Antwoord-URL** in het dialoogvenster **App-instellingen configureren**.

1. Nabij de onderkant van de pagina uploadt u het **XML-bestand met federatieve gegevens** dat u hebt gedownload uit Azure Portal. 

    ![XML-bestand met federatieve gegevens](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "XML-bestand met IdP-metagegevens")

1. Selecteer **Opslaan**.


### <a name="create-adobe-creative-cloud-test-user"></a>Een testgebruiker voor Adobe Creative Cloud definiëren

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Adobe Creative Cloud, moeten ze worden ingericht in Adobe Creative Cloud. In het geval van Adobe Creative Cloud moet dit handmatig gebeuren.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Ga als volgt te werk om een gebruikersaccount in te richten:

1. Meld u als een beheerder aan bij [Adobe Admin Console](https://adminconsole.adobe.com).

2. Voeg de gebruiker toe als een federatieve id en wijs een productprofiel toe aan de gebruiker. Zie [Add users in Adobe Admin Console](https://helpx.adobe.com/enterprise/using/users.html#Addusers) voor uitgebreide informatie over het toevoegen van gebruikers. 

3. Typ uw e-mailadres/UPN in het aanmeldingsformulier van Adobe en druk op tab om als federatieve gebruiker terug te gaan naar Azure AD:
   * Webtoegang: www\.adobe.com > aanmelden
   * In het hulpprogramma voor desktop-app > aanmelden
   * In de toepassing > help > aanmelden

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Adobe Creative Cloud klikt in het toegangsvenster, wordt u als het goed is automatisch aangemeld bij het exemplaar van Adobe Creative Cloud waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Adobe Creative Cloud proberen met Azure AD](https://aad.portal.azure.com/)

- [Een identiteit instellen (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-identity.html)
  
- [Configure Microsoft Azure for use with Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)
