---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Workday | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2f39b6f58b250d68a3b2ce962f158c7df36d812
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77046604"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Workday

In deze zelfstudie leert u hoe u Workday integreert met Azure Active Directory (Azure AD). Wanneer u Workday integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Workday.
* Stel uw gebruikers in om automatisch te worden aangemeld bij Workday met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Abonnement op Single Sign-on (Workday Single Sign -on) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Workday ondersteunt **SP** geïnitieerdse SSO.

* Zodra u Workday hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>Werkdag toevoegen vanuit de galerie

Als u de integratie van Workday in Azure AD wilt configureren, moet u Workday vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Workday** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Werkdag** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Azure AD-aanmelding voor Workday configureren en testen

Azure AD SSO configureren en testen met Workday met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Workday.

Als u Azure AD SSO wilt configureren en testen met Workday, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met B.Simon te testen.
    1. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
2. **[Workday configureren](#configure-workday)** om de SSO-instellingen aan toepassingszijde te configureren.
    1. **[Maak de workday-testgebruiker](#create-workday-test-user)** om een tegenhanger van B.Simon in Workday te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Workday-toepassingsintegratie** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Typ **in** het tekstvak Id een URL met het volgende patroon:`http://www.workday.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Dit zijn niet de echte waarden. Werk deze waarden bij met de werkelijke URL voor aanmelding en de URL van het antwoord. Uw antwoord-URL moet een subdomein hebben bijvoorbeeld: www, wd2, wd3, wd3-impl, wd5, wd5-impl).
    > Met behulp `http://www.myworkday.com` van `http://myworkday.com` iets als werkt, maar niet. Neem contact op met [workday client support team](https://www.workday.com/en-us/partners-services/services/support.html) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Uw Workday-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. Workday-toepassing verwacht **dat naam-id** wordt toegewezen met **user.mail,** **UPN,** enz., zodat u de toewijzing van het kenmerk moet bewerken door op pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![installatiekopie](common/edit-attribute.png)

    > [!NOTE]
    > Hier hebben we standaard de naam-id met UPN (user.userprincipalname) in kaart gebracht. U moet de naam-id in kaart brengen met de werkelijke gebruikersnaam in uw Workday-account (uw e-mail, UPN, enz.) voor een succesvolle werking van SSO.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Als u de **ondertekeningsopties** wilt wijzigen volgens uw vereiste, klikt u op De knop **Bewerken** om het dialoogvenster **SAML-ondertekeningscertificaat** te openen.

    ![installatiekopie](common/edit-certificate.png) 

    ![installatiekopie](./media/workday-tutorial/signing-option.png)

    a. Selecteer **SAML-antwoord en -bewering ondertekenen** voor **de tekenoptie**.

    b. Klik **op Opslaan**

1. Kopieer in de sectie **Workday instellen** de juiste URL(s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Workday.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Workday**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-workday"></a>Werkdag configureren

1. Meld u in een ander browservenster aan bij uw bedrijfssite van Workday als beheerder.

2. Zoek in **het vak Zoeken** met de naam **Tenantsetup bewerken – Beveiliging** linksboven op de startpagina.

    ![Tenantbeveiliging bewerken](./media/workday-tutorial/IC782925.png "Tenantbeveiliging bewerken")

3. Voer in de sectie **URL's voor omleiding** de volgende stappen uit:

    ![URL's voor omleiding](./media/workday-tutorial/IC7829581.png "URL's voor omleiding")

    a. Klik **op Rij toevoegen**.

    b. Plak in het **tekstvak Vooraanmeldingsomleiding**, **TIMEout Redirect** URL en **Mobile Redirect URL** de **aanmeldings-URL** die u hebt gekopieerd uit de sectie **Workday instellen** van Azure-portal.

    c. Plak in het tekstvak **URL-omleiden** de **URL van afmelden** die u hebt gekopieerd uit de sectie **Workday instellen** van Azure-portal.

    d. Selecteer in het tekstvak **Gebruikt voor omgevingen** de omgevingsnaam.  

   > [!NOTE]
   > De waarde van het kenmerk Omgeving is gekoppeld aan de waarde van de tenant-URL:  
   > -Als de domeinnaam van de URL van de Workday-tenant begint met impl bijvoorbeeld: * https://www.myworkday.com/"tenant"/login-saml2.htmld),* moet het kenmerk **Omgeving** worden ingesteld op Implementatie.  
   > -Als de domeinnaam met iets anders begint, moet u contact opnemen met [het workday-ondersteuningsteam client](https://www.workday.com/en-us/partners-services/services/support.html) om de bijbehorende **omgevingswaarde** te krijgen.

4. Voer in de sectie **SAML Setup** de volgende stappen uit:

    ![SAML-installatie](./media/workday-tutorial/IC782926.png "SAML-installatie")

    a.  Selecteer **SAML-verificatie inschakelen**.

    b.  Klik **op Rij toevoegen**.

5. Voer in de sectie **SAML-identiteitsproviders** de volgende stappen uit:

    ![SAML-id-providers](./media/workday-tutorial/IC7829271.png "SAML-id-providers")

    a. Typ in het tekstvak **Naam van de identiteitsprovider** een providernaam (bijvoorbeeld: *SPInitiatedSSO*).

    b. Kopieer in de Azure-portal in de sectie **Werkdag instellen** de waarde **van de Azure AD-id** en plak deze vervolgens in het tekstvak **Van de uitgever.**

    ![SAML-id-providers](./media/workday-tutorial/IC7829272.png "SAML-id-providers")

    c. Kopieer in de Azure-portal in de sectie **Workday instellen** de **URL-waarde van afmelden** en plak deze vervolgens in het tekstvak Voor de URL van de **aanmeldingsreactie.**

    d. Kopieer in de Azure-portal in de sectie **Workday instellen** de waarde van de **inlog-URL** en plak deze vervolgens in het URL-tekstvak van de **IdP SSO-service.**

    e. Selecteer in het tekstvak **Gebruikt voor omgevingen** de omgevingsnaam.

    f. Klik **op het public key-certificaat van de identiteitsprovider**en klik vervolgens op **Maken**.

    ![Maken](./media/workday-tutorial/IC782928.png "Maken")

    g. Klik **op De openbare sleutel van x509 maken**.

    ![Maken](./media/workday-tutorial/IC782929.png "Maken")

6. Voer in de sectie **Openbare sleutel bekijken x509** de volgende stappen uit:

    ![Bekijk x509 openbare sleutel](./media/workday-tutorial/IC782930.png "Bekijk x509 openbare sleutel")

    a. Typ in het tekstvak **Naam** een naam voor uw certificaat (bijvoorbeeld *\_PPE SP).*

    b. Typ **in het** tekstvak Geldig van het vak de geldige van kenmerkwaarde van uw certificaat.

    c.  Typ **in** het tekstvak Geldig naar tekst de waarde geldig op kenmerk van uw certificaat.

    > [!NOTE]
    > U de geldige vanaf datum en het geldige tot op heden van het gedownloade certificaat krijgen door erop te dubbelklikken.  De datums worden vermeld onder het tabblad **Details.**
    >
    >

    d.  Open uw basis-64 gecodeerd certificaat in kladblok en kopieer de inhoud ervan.

    e.  Plak in het tekstvak **Certificaat** de inhoud van het klembord.

    f.  Klik op **OK**.

7. Voer de volgende stappen uit:

    ![SSO-configuratie](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO-configuratie")

    a.  Typ in het tekstvak **http://www.workday.com** **Service Provider-id** .

    b. Selecteer **Niet laten leeglopen sp-geïnitieerde verificatieaanvraag**.

    c. Selecteer **SHA256**als **verificatieaanvraaghandtekeningmethode**.

    ![Verificatieaanvraaghandtekeningmethode](./media/workday-tutorial/WorkdaySSOConfiguration.png "Verificatieaanvraaghandtekeningmethode")

    d. Klik op **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Zorg ervoor dat u eenmalige aanmelding correct instelt. Als u eenmalige aanmelding inschakelt met een onjuiste instelling, u de toepassing mogelijk niet invoeren met uw referenties en worden vergrendeld. In deze situatie biedt Workday een url voor het inloggen met een back-up waar gebruikers zich kunnen aanmelden met hun normale gebruikersnaam en wachtwoord in de volgende indeling:[Uw URL van de werkdag]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>Gebruiker van workday-test maken

In deze sectie maakt u een gebruiker genaamd B.Simon in Workday. Werk samen met [workday Client support team](https://www.workday.com/partners-services/services/support.html) om de gebruikers toe te voegen in het Workday-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Test SSO

Wanneer u de tegel Werkdag selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de werkdag waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Workday uitproberen met Azure AD](https://aad.portal.azure.com)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Workday beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/protect-workday)