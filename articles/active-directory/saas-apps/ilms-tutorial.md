---
title: 'Zelf studie: integratie Azure Active Directory met iLMS | Microsoft Docs'
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68944025"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Zelf studie: iLMS integreren met Azure Active Directory

In deze zelf studie leert u hoe u iLMS integreert met Azure Active Directory (Azure AD). Wanneer u iLMS integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot iLMS.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij iLMS met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proef versie van één maand ontvangen.
* iLMS-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. iLMS ondersteunt SSO die door **SP en IDP** is geïnitieerd

## <a name="adding-ilms-from-the-gallery"></a>ILMS toevoegen uit de galerie

Als u de integratie van iLMS in azure AD wilt configureren, moet u iLMS uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **iLMS** in het zoekvak.
1. Selecteer **iLMS** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test Azure AD SSO met iLMS met behulp van een test gebruiker met de naam **Julia Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in iLMS.

Als u Azure AD SSO wilt configureren en testen met iLMS, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[ILMS SSO configureren](#configure-ilms-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een iLMS-test gebruiker](#create-ilms-test-user)** -om een equivalent van Julia Simon in iLMS te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **ILMS** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u op de pagina **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Plak in het tekstvak **id** de **id** -waarde die u kopieert vanuit de sectie **service provider** van de SAML-instellingen in de iLMS-beheer Portal.

    b. Plak in het tekstvak **antwoord-URL** de **EINDPUNT waarde (URL)** die u kopieert van de sectie **service provider** van de SAML-instellingen in de iLMS-beheer Portal met het volgende patroon`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Plak in het tekstvak **URL voor aanmelding** de **EINDPUNT waarde (URL)** die u kopieert vanuit de sectie **service provider** van de SAML-instellingen in de iLMS-beheer portal als`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Voor het inschakelen van JIT-inrichting verwacht uw iLMS-toepassing de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    > [!NOTE]
    > U moet het **maken van niet-herkende gebruikers account** inschakelen in iLMS om deze kenmerken toe te wijzen. Volg de [onderstaande](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) instructies om een idee te krijgen van de kenmerken van de configuratie.

1. Daarnaast verwacht iLMS toepassing nog enkele kenmerken die in het SAML-antwoord weer worden door gegeven. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:

    | Naam | Bronkenmerk|
    | --------|------------- |
    | daarvan | user.department |
    | regio | gebruiker. State |
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

1. Kopieer op de sectie **ILMS instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-ilms-sso"></a>ILMS SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw **iLMS-beheer Portal** .

2. Klik op **SSO: SAML** onder **instellingen** tabblad om de SAML-instellingen te openen en de volgende stappen uit te voeren:

    ![Eenmalige aanmelding configureren](./media/ilms-tutorial/1.png)

3. Vouw de sectie **service provider** uit en kopieer de waarde voor **id** en **eind punt (URL)** .

    ![Eenmalige aanmelding configureren](./media/ilms-tutorial/2.png) 

4. Klik onder de sectie **ID-provider** op **meta gegevens importeren**.

5. Selecteer het bestand met **federatieve meta gegevens** dat is gedownload uit het gedeelte Azure portal van het **SAML-handtekening certificaat** .

    ![Eenmalige aanmelding configureren](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Als u JIT-inrichting wilt inschakelen voor het maken van iLMS-accounts voor onherkenbare gebruikers, volgt u de onderstaande stappen:

    a. Schakel het selectie vakje **niet-herkende gebruikers account maken**in.

    ![Eenmalige aanmelding configureren](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Wijs de kenmerken in azure AD toe met de kenmerken in iLMS. Geef in de kolom kenmerk de naam van de kenmerken of de standaard waarde op.

    c. Ga naar het tabblad **bedrijfs regels** en voer de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/ilms-tutorial/5.png)

    d. Controleer of u niet **-herkende regio's, afdelingen en afdelingen maakt** om regio's, afdelingen en afdelingen te maken die nog niet bestaan op het moment van eenmalige aanmelding.

    e. Schakel het **gebruikers profiel bijwerken tijdens het aanmelden** in om op te geven of het profiel van de gebruiker wordt bijgewerkt met de eenmalige aanmelding.

    f. Als de optie **lege waarden voor niet-verplichte velden bijwerken in gebruikers profiel** is ingeschakeld, wordt in het iLMS-Profiel van de gebruiker ook lege waarden voor die velden opgeslagen.

    g. Controleer het **e-mail bericht verzend fout** en voer het e-mail adres in van de gebruiker waarvoor u de e-mail fout melding wilt ontvangen.

7. Klik op **Save** om de instellingen op te slaan.

    ![Eenmalige aanmelding configureren](./media/ilms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam Julia Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan iLMS.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **iLMS**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-ilms-test-user"></a>ILMS-test gebruiker maken

De toepassing biedt ondersteuning voor Just-in-time-gebruikers en nadat verificatie gebruikers automatisch in de toepassing zijn gemaakt. JIT werkt als u op het selectie vakje **niet-herkend gebruikers account maken** hebt geklikt tijdens de SAML-configuratie-instelling op de iLMS-beheer Portal.

Als u hand matig een gebruiker moet maken, volgt u de onderstaande stappen:

1. Meld u als beheerder aan bij de iLMS-bedrijfs site.

2. Klik op **gebruiker registreren** onder **gebruikers** tabblad om de pagina **gebruiker registreren** te openen.

   ![Werknemer toevoegen](./media/ilms-tutorial/3.png)

3. Voer de volgende stappen uit op de pagina **gebruiker registreren** .

    ![Werknemer toevoegen](./media/ilms-tutorial/create_testuser_add.png)

    a. Typ in het tekstvak **voor de voor naam** de voor naam, bijvoorbeeld Julia.

    b. Typ in het tekstvak **Achternaam** de achternaam zoals Simon.

    c. Typ in het tekstvak **e-mail-id** het e-mail adres BrittaSimon@contoso.comvan de gebruiker zoals.

    d. Selecteer in de vervolg keuzelijst **regio** de waarde voor regio.

    e. Selecteer in de vervolg keuzelijst voor **delen** de waarde voor divisie.

    f. Selecteer in de vervolg keuzelijst **afdeling** de waarde voor afdeling.

    g. Klik op **Opslaan**.

    > [!NOTE]
    > U kunt een registratie-e-mail naar de gebruiker verzenden door de selectie vakje **registratie E-mail verzenden** in te scha kelen.

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel iLMS in het toegangs venster selecteert, wordt u automatisch aangemeld bij de iLMS waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
