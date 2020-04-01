---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Jamf Pro | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Splunk Enterprise en Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d83dbe756e8e6acdb58861ac359801bc13a63c4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77373189"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Zelfstudie: Azure Active Directory SSO-integratie met Jamf Pro

In deze zelfstudie leert u hoe u Jamf Pro integreren met Azure Active Directory (Azure AD). Wanneer u Jamf Pro integreert met Azure AD, u het als:

* Gebruik Azure AD om te bepalen wie toegang heeft tot Jamf Pro.
* Meld u automatisch aan bij uw gebruikers bij Jamf Pro met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Enkele aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Een Jamf Pro-abonnement dat eenmalig aanmelden (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. 

* Jamf Pro ondersteunt **door SP geïnitieerde** en **IdP-geïnitieerde** SSO.
* Zodra u Jamf Pro hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="add-jamf-pro-from-the-gallery"></a>Jamf Pro toevoegen vanuit de galerie

Voor het configureren van de integratie van Jamf Pro in Azure AD, moet u Jamf Pro uit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of uw persoonlijke Microsoft-account.
1. Selecteer in het linkerdeelvenster de **Azure Active Directory-service.**
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Voer In de sectie **Toevoegen van de galerie** *Jamf Pro* in het zoekvak in.
1. Selecteer **Jamf Pro** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>SSO configureren en testen in Azure AD voor Jamf Pro

Configureer en test Azure AD SSO met Jamf Pro met behulp van een testgebruiker genaamd B.Simon. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Jamf Pro.

In deze sectie configureert en test u Azure AD SSO met Jamf Pro.

1. [Configureer SSO in Azure AD](#configure-sso-in-azure-ad) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om Azure AD SSO te testen met het B.Simon-account.
    1. [Wijs de Azure AD-testgebruiker toe,](#assign-the-azure-ad-test-user) zodat B.Simon SSO kan gebruiken in Azure AD.
1. [Configureer SSO in Jamf Pro](#configure-sso-in-jamf-pro) om de SSO-instellingen aan de toepassingszijde te configureren.
    1. [Maak een Jamf Pro-testgebruiker](#create-a-jamf-pro-test-user) om een tegenhanger van B.Simon in Jamf Pro te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. [Test de SSO-configuratie](#test-the-sso-configuration) om te controleren of de configuratie werkt.

## <a name="configure-sso-in-azure-ad"></a>SSO configureren in Azure AD

In deze sectie schakelt u Azure AD SSO in in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Jamf** Pro-toepassingsintegratie de sectie **Beheren** en selecteer **Single Sign-On**.
1. Selecteer **SAML**op de pagina **Een enkele aanmeldingsmethode** selecteren .
1. Selecteer op de pagina **Eén aanmelding instellen met SAML** het penpictogram voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Bewerk de pagina BasisSAML-configuratie.](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in **de idp-modus** wilt configureren:

    a. Voer in het tekstvak **Id** een URL in die de volgende formule gebruikt:`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Voer in het tekstvak **URL beantwoorden** een URL in die de volgende formule gebruikt:`https://<subdomain>.jamfcloud.com/saml/SSO`

1. Selecteer **Extra URL's instellen**. Als u de toepassing in de **sp-gestarte** modus wilt configureren, voert u in het tekstvak **URL aanmelden** een URL in die de volgende formule gebruikt:`https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. U krijgt de werkelijke id-waarde van de sectie **Single Sign-On** in jamf Pro-portal, die later in de zelfstudie wordt uitgelegd. U de werkelijke subdomeinwaarde uit de id-waarde halen en die subdomeingegevens gebruiken als url van aanmelding en antwoord. U ook verwijzen naar de formules die worden weergegeven in de sectie **BasisSAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eén aanmelding instellen met SAML** naar de sectie **SAML-ondertekeningscertificaat,** selecteer de **kopieerknop** om **de URL van de appfederatie-metagegevens**te kopiëren en sla deze vervolgens op uw computer op.

    ![De downloadkoppeling saml-ondertekeningscertificaat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory**, selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.
   1. Voer in het veld **Gebruikersnaam** [naam]@[bedrijfsdomein] in. [uitbreiding]. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geef je B.Simon toegang tot Jamf Pro.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Jamf Pro**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De knop Gebruiker toevoegen selecteren](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en selecteer de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker. Selecteer vervolgens de knop **Selecteren** onder aan het scherm.
1. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen.**

## <a name="configure-sso-in-jamf-pro"></a>SSO configureren in Jamf Pro

1. Als u de configuratie binnen Jamf Pro wilt automatiseren, installeert u de **browserextensie Mijn apps Secure Sign-in** door **De extensie installeren te**selecteren.

    ![Pagina Met veilige aanmelding sbrowservan Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, selecteert u **Jamf Pro instellen**. Wanneer de Jamf Pro-toepassing wordt geopend, geeft u de beheerdersreferenties op om zich aan te melden. De browserextensie configureert automatisch de toepassing en automatiseert stap 3 tot en met 7.

    ![Configuratiepagina instellen in Jamf Pro](common/setup-sso.png)

3. Als u Jamf Pro handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich aan bij uw Jamf Pro-bedrijfssite als beheerder. Neem vervolgens de volgende stappen.

4. Selecteer het **pictogram Instellingen** in de rechterbovenhoek van de pagina.

    ![Het pictogram Instellingen selecteren in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Selecteer **Eenmalige aanmelding**.

    ![Eén aanmelding selecteren in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Neem op de pagina **Aanmelding smaken** de volgende stappen.

    ![De single sign-on pagina in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Selecteer **Bewerken**.

    b. Schakel het selectievakje **Eén aanmeldingsverificatie inschakelen** in.

  c. Selecteer **Azure** als optie in het vervolgkeuzemenu **Identity Provider.**

  d. Kopieer de **entiteits-id-waarde** en plak deze in het veld **Id (Entity ID)** in de sectie **BasisSAML-configuratie** in de Azure-portal.

> [!NOTE]
> Gebruik de waarde `<SUBDOMAIN>` in het veld om de URL van de aanmelding en de url van de antwoord-URL te voltooien in de sectie **BasisSAML-configuratie** in de Azure-portal.

  e. Selecteer **de URL van metagegevens** in het vervolgkeuzemenu **Metagegevensbron van de identiteitsprovider.** Plak in het veld dat wordt weergegeven de **url-waarde van de app-federatie-metagegevens** die u hebt gekopieerd van de Azure-portal.

  f. (Optioneel) Bewerk de vervaldatum van het token of selecteer 'Saml-token verlopen uitschakelen'.

7. Schuif op dezelfde pagina omlaag naar de sectie **Gebruikerstoewijzing.** Neem vervolgens de volgende stappen.

    ![De sectie Gebruikerstoewijzing van de single sign-on-pagina in Jamf Pro.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. Selecteer de optie **NameID** voor **gebruikerstoewijzing van identiteitsprovider**. Standaard is deze optie ingesteld op **NameID,** maar u een aangepast kenmerk definiëren.

    b. Selecteer **E-mail** voor **Jamf Pro-gebruikerstoewijzing**. Jamf Pro brengt SAML-kenmerken in kaart die door de IdP eerst door gebruikers en vervolgens door groepen worden verzonden. Wanneer een gebruiker toegang probeert te krijgen tot Jamf Pro, krijgt Jamf Pro informatie over de gebruiker van de Identity Provider en matcht deze met alle Jamf Pro-gebruikersaccounts. Als het binnenkomende gebruikersaccount niet wordt gevonden, probeert Jamf Pro het op groepsnaam te koppelen.

    c. Plak de `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` waarde in het kenmerkNAAM van de **identiteitsprovidergroep.**

    d. Schuif op dezelfde pagina omlaag naar de sectie **Beveiliging** en selecteer **Gebruikers toestaan de verificatie voor één aanmelding te omzeilen.** Als gevolg hiervan worden gebruikers niet doorgestuurd naar de aanmeldingspagina van de identiteitsprovider voor verificatie en kunnen ze zich rechtstreeks aanmelden bij Jamf Pro. Wanneer een gebruiker toegang probeert te krijgen tot Jamf Pro via de id-provider, vindt IdP-geïnitieerde SSO-verificatie en -autorisatie plaats.

    e. Selecteer **Opslaan**.

### <a name="create-a-jamf-pro-test-user"></a>Een Jamf Pro-testgebruiker maken

Als Azure AD-gebruikers zich kunnen aanmelden bij Jamf Pro, moeten ze worden in- en aangesloten bij Jamf Pro. Inrichten in Jamf Pro is een handmatige taak.

Als u een gebruikersaccount wilt inrichten, neemt u de volgende stappen:

1. Meld u aan bij uw Jamf Pro-bedrijfssite als beheerder.

2. Selecteer het pictogram **Instellingen** in de rechterbovenhoek van de pagina.

    ![Het instellingenpictogram in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Selecteer **Jamf Pro-gebruikersaccounts & groepen**.

    ![Het pictogram Jamf Pro-gebruikersaccounts & groepen in de Jamf Pro-instellingen](./media/jamfprosamlconnector-tutorial/user1.png)

4. Selecteer **Nieuw**.

    ![Pagina Jamf Pro-gebruikersaccounts & de systeeminstellingen groepen](./media/jamfprosamlconnector-tutorial/user2.png)

5. Selecteer **Standaard account maken**.

    ![De optie Standaardaccount maken op de pagina Jamf Pro-gebruikersaccounts & groepen](./media/jamfprosamlconnector-tutorial/user3.png)

6. Voer in het dialoogvenster **Nieuw account** de volgende stappen uit:

    ![Nieuwe opties voor het instellen van een account in de systeeminstellingen van Jamf Pro](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Voer **USERNAME** in het `Britta Simon`veld GEBRUIKERSNAAM de volledige naam van de testgebruiker in.

    b. Selecteer de opties voor **TOEGANGSNIVEAU,** **PRIVILEGE SET**en **TOEGANGSSTATUS** die in overeenstemming zijn met uw organisatie.

    c. Voer in het veld `Britta Simon`VOLLEDIGE **NAAM** de grootst.

    d. Voer in het veld **E-mailadres** het e-mailadres van het account van Britta Simon in.

    e. Voer **in** het veld WACHTWOORD het wachtwoord van de gebruiker in.

    f. Voer in het veld **WACHTWOORD VERIFIËREN** het wachtwoord van de gebruiker opnieuw in.

    g. Selecteer **Opslaan**.

## <a name="test-the-sso-configuration"></a>De SSO-configuratie testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de Jamf Pro-tegel selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij het Jamf Pro-account waarvoor u SSO hebt geconfigureerd. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Probeer Jamf Pro met Azure AD](https://aad.portal.azure.com/)