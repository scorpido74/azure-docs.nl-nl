---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Workday | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 4867a1735f091085f64bbe7010969bd086f820a1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527169"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Workday

In deze zelfstudie leert u hoe u Workday integreert met Azure Active Directory (Azure AD). Wanneer u Workday integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie toegang heeft tot Workday.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Workday.
* Uw accounts op één centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Workday-abonnement met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Workday ondersteunt door **SP** geïnitieerde eenmalige aanmelding.

* Zodra u Workday hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in real time worden beschermd. Sessiebeheer, uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>Workday toevoegen vanuit de galerie

Als u de integratie van Workday met Azure AD wilt configureren, dient u Workday vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Workday** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Workday** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Workday

Configureer en test eenmalige aanmelding van Azure AD met Workday met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Workday.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Workday te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Configureer Workday](#configure-workday)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. **[Een Workday-testgebruiker maken](#create-workday-test-user)** : als u een equivalent van B.Simon in Workday wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Workday** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `http://www.workday.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Dit zijn niet de echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Uw antwoord-URL moet een subdomein hebben, zoals www, wd2, wd3, wd3-impl, wd5 of wd5-impl.
    > Iets zoals `http://www.myworkday.com` gebruiken werkt, maar `http://myworkday.com` niet. Neem contact op met het [Workday-ondersteuningsteam](https://www.workday.com/en-us/partners-services/services/support.html) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. In de Workday-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de Workday-toepassing wordt verwacht dat **nameidentifier** is toegewezen aan **user.mail**, **UPN** enzovoort. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Hier hebben we de Naam-id als standaard toegewezen aan UPN (user.userprincipalname). U moet de naam-ID toewijzen aan uw echte Gebruikers-id in uw Workday-account (uw e-mail, UPN enzovoort) om eenmalige aanmelding te doen werken.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Om de **Handtekeningopties** aan te passen aan uw vereisten, klikt u op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Selecteer **SAML-antwoord en -bewering ondertekenen** voor **Optie voor ondertekening**.

    b. Klik op **Opslaan**.

1. In de sectie **Workday instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Workday.

1. Selecteer in de Azure-portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. In de lijst met toepassingen selecteert u **Workday**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-workday"></a>Workday configureren

1. Meld u in een andere browser als beheerder aan bij uw bedrijfssite in Workday.

2. Zoek in het **Zoekvak** met de naam **Tenant-instelling bewerken - Beveiliging** in de linkerbovenhoek van de startpagina.

    ![Tenant-beveiliging bewerken](./media/workday-tutorial/IC782925.png "Tenant-beveiliging bewerken")

3. Voer de volgende stappen uit in de sectie **Omleidings-URL's**:

    ![Omleidings-URL's](./media/workday-tutorial/IC7829581.png "Omleidings-URL's")

    a. Klik op **Rij toevoegen**.

    b. Plak de **Aanmeldings-URL** die u gekopieerd heeft van de sectie **Workday instellen** van Azure Portal in het tekstvak **Omleidings-URL voor aanmelding**, **Omleidings-URL voor time-out** en **Omleidings-URL voor mobiele apparaten**.

    c. Plak de **Afmeldings-URL** die u gekopieerd heeft van de sectie **Workday instellen** van Azure Portal in het tekstvak **Omleidings-URL voor afmelding**.

    d. Selecteer de naam van de omgeving in het tekstvak **Gebruikt voor omgevingen**.  

   > [!NOTE]
   > De waarde van de eigenschap Omgeving is gekoppeld aan de waarde van de tenant-URL:  
   > \- Als de domeinnaam van de tenant-URL van Workday begint met impl, bijvoorbeeld *https://www.myworkday.com/"tenant"/login-saml2.htmld*), dan moet de eigenschap **Omgeving** op Implementatie staan.  
   > \- Als de domeinnaam begint met iets anders, neem dan contact op met het [Workday-ondersteuningsteam](https://www.workday.com/en-us/partners-services/services/support.html) om de overeenkomstige waarde voor **Omgeving** te krijgen.

4. Voer in de sectie **SAML-installatie** de volgende stappen uit:

    ![SAML-installatie](./media/workday-tutorial/IC782926.png "SAML-installatie")

    a.  Selecteer **SAML-verificatie inschakelen**.

    b.  Klik op **Rij toevoegen**.

5. Voer in de sectie **SAML-id-providers** de volgende stappen uit:

    ![SAML-id-providers](./media/workday-tutorial/IC7829271.png "SAML-id-providers")

    a. Typ in het tekstvak **Id-providernaam** de naam van een provider (bijvoorbeeld *SPInitiatedSSO*).

    b. Kopieer in de sectie **Workday instellen** van het Azure Portal de **Azure AD-id**-waarde en plak deze in het tekstvak **Verlener**.

    ![SAML-id-providers](./media/workday-tutorial/IC7829272.png "SAML-id-providers")

    c. Kopieer in de sectie **Workday instellen** van het Azure Portal de **Afmeldings-URL**-waarde en plak deze in het tekstvak **Afmeldingsantwoord-URL**.

    d. Kopieer in de sectie **Workday instellen** van het Azure Portal de **Aanmeldings-URL**-waarde en plak deze in het tekstvak **IdP SSO Service-URL**.

    e. Selecteer de naam van de omgeving in het tekstvak **Gebruikt voor omgevingen**.

    f. Klik op **Certificaat met een openbare sleutel van id-provider** en vervolgens op **Maken**.

    ![Maken](./media/workday-tutorial/IC782928.png "Maken")

    g. Klik op **Openbare sleutel (x509) maken**.

    ![Maken](./media/workday-tutorial/IC782929.png "Maken")

6. Voer in de sectie **Openbare sleutel (x509) weergeven** de volgende stappen uit:

    ![Openbare sleutel (x509) weergeven](./media/workday-tutorial/IC782930.png "Openbare sleutel (x509) weergeven")

    a. Typ in het tekstvak **Naam** een naam voor uw certificaat (bijvoorbeeld *PPE\_SP*).

    b. Typ in het tekstvak **Geldig vanaf** de waarde voor de eigenschap Geldig vanaf van uw certificaat.

    c.  Typ in het tekstvak **Geldig tot** de waarde voor de eigenschap Geldig tot van uw certificaat.

    > [!NOTE]
    > U kunt de Geldig vanaf- en Geldig tot-datum verkrijgen door te dubbelklikken op het gedownloade certificaat.  De data worden weergegeven onder het tabblad **Details**.
    >
    >

    d.  Open uw met base 64 gecodeerde certificaat in Kladblok en kopieer de inhoud.

    e.  Plak de inhoud van uw klembord in het tekstvak **Certificaat**.

    f.  Klik op **OK**.

7. Voer de volgende stappen uit:

    ![SSO-configuratie](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO-configuratie")

    a.  Typ **http://www.workday.com** in het tekstvak **Serviceprovider-id**.

    b. Selecteer **SP-geïnitieerde verificatieaanvraag niet verkleinen**.

    c. Selecteer **SHA256** als **Handtekeningmethode voor verificatieaanvraag**.

    ![Handtekeningmethode voor verificatieaanvraag](./media/workday-tutorial/WorkdaySSOConfiguration.png "Handtekeningmethode voor verificatieaanvraag")

    d. Klik op **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Zorg ervoor dat u eenmalige aanmelding juist instelt. Als u eenmalige aanmelding met verkeerde instellingen inschakelt, kan het zijn dat u geen toegang krijgt tot de toepassing met uw referenties en buitengesloten wordt. In dat geval biedt Workday een reserve-aanmeldings-URL waar gebruikers zich kunnen aanmelden met hun normale gebruikersnaam en wachtwoord in de volgende indeling: [Uw Workday-URL]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>Workday-testgebruiker maken

In deze sectie gaat u in Workday een gebruiker maken met de naam B.Simon. Werk samen met het [ondersteuningsteam van Workday](https://www.workday.com/en-us/partners-services/services/support.html) om de gebruikers toe te voegen in het Workday-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u de tegel Workday selecteert in het toegangsvenster, zou u automatisch moeten worden aangemeld bij het exemplaar van Workday toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Workday proberen met Azure AD](https://aad.portal.azure.com)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Workday beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/protect-workday)