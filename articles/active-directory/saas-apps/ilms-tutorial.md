---
title: 'Zelfstudie: Azure Active Directory-integratie met iLMS | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50097aec1c4a003d3494029e8f25bb13b564f207
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944025"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Zelfstudie: iLMS integreren met Azure Active Directory

In deze zelfstudie leert u hoe u iLMS integreert met Azure Active Directory (Azure AD). Wanneer u iLMS integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot iLMS.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij iLMS met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Als je geen abonnement hebt, kun je [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proefperiode van een maand krijgen.
* iLMS single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. iLMS ondersteunt **SP en IDP** geïnitieerd sso

## <a name="adding-ilms-from-the-gallery"></a>ILMS toevoegen vanuit de galerie

Als u de integratie van iLMS in Azure AD wilt configureren, moet u iLMS vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **iLMS** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **iLMS** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met iLMS met behulp van een testgebruiker genaamd **Britta Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in iLMS.

Als u Azure AD SSO wilt configureren en testen met iLMS, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer iLMS SSO](#configure-ilms-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak iLMS-testgebruiker](#create-ilms-test-user)** - om een tegenhanger van Britta Simon in iLMS te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **iLMS-toepassingsintegratie** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **BasisSAML-configuratie** de waarden in voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Plak in het tekstvak **Id** de **id-waarde** die u kopieert uit de sectie **Serviceprovider** van SAML-instellingen in de iLMS-beheerportal.

    b. Plak in het tekstvak **URL beantwoorden** de waarde **Endpoint (URL)** plakken die u kopieert vanuit de sectie **Serviceprovider** van SAML-instellingen in de iLMS-beheerportal met het volgende patroon`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Plak in het tekstvak URL aanmelding de waarde **Endpoint (URL)** plakken die u kopieert vanuit de sectie **Serviceprovider** van **SAML-instellingen** in de iLMS-beheerportal als`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Om JIT-inrichting in te schakelen, verwacht uw iLMS-toepassing de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    > [!NOTE]
    > U moet **Niet-herkende gebruikersaccount maken** in iLMS inschakelen om deze kenmerken in kaart te brengen. Volg [hier](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) de instructies om een idee te krijgen over de kenmerken configuratie.

1. Naast bovenstaande, iLMS applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:

    | Name | Bronkenmerk|
    | --------|------------- |
    | Divisie | user.department |
    | regio | user.state |
    | department | user.jobtitle |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **ILMS instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-ilms-sso"></a>ILMS SSO configureren

1. Meld u in een ander browservenster aan bij uw **iLMS-beheerportal** als beheerder.

2. Klik **op SSO:SAML** onder het tabblad **Instellingen** om SAML-instellingen te openen en de volgende stappen uit te voeren:

    ![Eenmalige aanmelding configureren](./media/ilms-tutorial/1.png)

3. Vouw de sectie **Serviceprovider** uit en kopieer de waarde **Id** en **Eindpunt (URL).**

    ![Eenmalige aanmelding configureren](./media/ilms-tutorial/2.png) 

4. Klik onder de sectie **Identiteitsprovider** op **Metagegevens importeren**.

5. Selecteer het bestand **Federation Metadata** dat is gedownload van de Azure-portal in de sectie **SAML-ondertekeningscertificaat.**

    ![Eenmalige aanmelding configureren](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Als u JIT-inrichting wilt inschakelen om iLMS-accounts te maken voor niet-herkennende gebruikers, voert u de volgende stappen uit:

    a. Schakel **Niet-herkende gebruikersaccount maken in**.

    ![Eenmalige aanmelding configureren](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Breng de kenmerken in Azure AD in kaart met de kenmerken in iLMS. Geef in de kenmerkkolom de naam van de kenmerken of de standaardwaarde op.

    c. Ga naar het tabblad **Bedrijfsregels** en voer de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/ilms-tutorial/5.png)

    d. Schakel **Niet-erkende regio's, afdelingen en afdelingen maken** om regio's, afdelingen en afdelingen te maken die nog niet bestaan op het moment van eenmalig aanmelden.

    e. Schakel **Gebruikersprofiel bijwerken tijdens aanmelding** in om aan te geven of het profiel van de gebruiker wordt bijgewerkt met elke enkele aanmelding.

    f. Als de optie **Lege waarden bijwerken voor niet-verplichte velden in gebruikersprofiel** is ingeschakeld, worden optionele profielvelden die leeg zijn bij het aanmelden, er ook voor gezorgd dat het iLMS-profiel van de gebruiker lege waarden voor die velden bevat.

    g. Schakel **E-mail met foutmelding verzenden in** en voer de e-mail in van de gebruiker waar u de e-mail met foutmelding wilt ontvangen.

7. Klik op **Save** om de instellingen op te slaan.

    ![Eenmalige aanmelding configureren](./media/ilms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd Britta Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot iLMS.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **iLMS**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **In** het dialoogvenster Gebruikers en groepen De optie **Britta Simon** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-ilms-test-user"></a>ILMS-testgebruiker maken

Toepassing ondersteunt Just in time user provisioning en after authentication users are created in the application automatically. JIT werkt als u tijdens de SAML-configuratie-instelling op het selectievakje **Niet-herkende gebruikersaccount** maken hebt geklikt bij de iLMS-beheerportal.

Als u een gebruiker handmatig wilt maken, volgt u de volgende stappen:

1. Meld u aan bij uw iLMS-bedrijfssite als beheerder.

2. Klik **op Gebruiker registreren** onder het tabblad **Gebruikers** om de pagina Gebruiker **registreren te** openen.

   ![Werknemer toevoegen](./media/ilms-tutorial/3.png)

3. Voer op de pagina **Gebruiker registreren** de volgende stappen uit.

    ![Werknemer toevoegen](./media/ilms-tutorial/create_testuser_add.png)

    a. Typ in het tekstvak **Voornaam** de voornaam zoals Britta.

    b. Typ in het tekstvak **Achternaam** de achternaam zoals Simon.

    c. Typ in het tekstvak **E-id** het BrittaSimon@contoso.come-mailadres van de gebruiker als .

    d. Selecteer **in** de vervolgkeuzelijst Regio de waarde voor regio.

    e. Selecteer **in** de vervolgkeuzelijst Divisie de waarde voor divisie.

    f. Selecteer **in** de vervolgkeuzelijst Afdeling de waarde voor de afdeling.

    g. Klik op **Opslaan**.

    > [!NOTE]
    > U registratiemail naar de gebruiker verzenden door het selectievakje **Registratiee-mail verzenden** te selecteren.

### <a name="test-sso"></a>Test SSO

Wanneer u de iLMS-tegel in het toegangspaneel selecteert, moet u automatisch worden aangemeld bij de iLMS waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
