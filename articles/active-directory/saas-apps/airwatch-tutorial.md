---
title: 'Zelf studie: integratie Azure Active Directory met het horloge | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 772b37816b83c275bae927d825434dc3ca76a35c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74231991"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Zelf studie: de bewaking met Azure Active Directory integreren

In deze zelf studie leert u hoe u de insluiting integreert met Azure Active Directory (Azure AD). Wanneer u de bewaking met Azure AD integreert, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot het horloge.
* Stel in dat uw gebruikers automatisch worden aangemeld bij het gebruik van hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proef versie van één maand ontvangen.
* Eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. De service Watch ondersteunt de door **SP** geïnitieerde SSO.

## <a name="adding-airwatch-from-the-gallery"></a>AirWatch toevoegen vanuit de galerie

Voor het configureren van de integratie van AirWatch in Azure AD moet u AirWatch vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. **Typ in** het gedeelte **toevoegen vanuit de galerie** de tekst in het zoekvak.
1. Selecteer in het deel venster met resultaten de optie **luchtwatch** en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de gaten.

Als u Azure AD SSO wilt configureren en testen met behulp van de functie voor controle, moet u de volgende bouw stenen door lopen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[Eenmalige aanmelding configureren](#configure-airwatch-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[Een testgebruiker maken in AirWatch](#create-airwatch-test-user)**: om in AirWatch een tegenhanger van Britta Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
5. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina voor de integratie van de **invoeg toepassing,** Zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **basis configuratie van SAML** de waarden in voor de volgende velden:

    1. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Typ in het vak **Id (Entiteits-id)** de waarde als: `AirWatch`

    > [!NOTE]
    > Dit is niet de echte waarde. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [het ondersteuningsteam van AirWatch Client](https://www.air-watch.com/company/contact-us/) om deze waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De AirWatch-toepassing verwacht de SAML-asserties in een specifieke indeling. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![installatiekopie](common/edit-attribute.png)

1. Bewerk in het gedeelte **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** de claims met het **pictogram Bewerken** of voeg de claims toe door met **Nieuwe claim toevoegen** het kenmerk van het SAML-token te configureren, zoals wordt weergegeven in de bovenstaande afbeelding. Hierna voert u de volgende stappen uit:

    | Naam |  Bronkenmerk|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om de meta gegevens-XML te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer de juiste URL ('s) op basis van uw vereiste op de sectie een **Luchtcontrole instellen** .

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Eenmalige SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij de bedrijfs site van uw bedrijf.

1. Op de pagina instellingen. Selecteer **instellingen > Bedrijfsintegratie > Directory Services**.

   ![Instellingen](./media/airwatch-tutorial/ic791921.png "Instellingen")

1. Klik op het tabblad **Gebruiker**. Typ uw domeinnaam in het vak **Basis-DN** en klik op **Opslaan**.

   ![Gebruiker](./media/airwatch-tutorial/ic791922.png "Gebruiker")

1. Klik op het tabblad **Server**.

   ![Server](./media/airwatch-tutorial/ic791923.png "server")

1. Voer de volgende stappen uit in de sectie **LDAP** :

    ![Uploaden](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. Selecteer **Geen** als **Type adressenlijst**.

    b. Selecteer **SAML gebruiken voor verificatie**.

1. Klik in de sectie **SAML 2,0** op **uploaden**om het gedownloade certificaat te uploaden.

    ![Uploaden](./media/airwatch-tutorial/ic791932.png "Uploaden")

1. Voer in het gedeelte **Aanvraag** de volgende stappen uit:

    ![Aanvraag](./media/airwatch-tutorial/ic791925.png "Aanvraag")  

    a. Selecteer **POST** als **Aanvraagbindingstype**.

    b. Kopieer de waarde voor de **aanmeldings-URL** in het Azure Portal op de pagina **eenmalige aanmelding configureren** in het dialoog venster voor de verzen ding en plak deze in het tekstvak **URL-provider voor eenmalige aanmelding** .

    c. Als **NameID-indeling** selecteert u **E-mailadres**.

    d. Selecteer bij **verificatie aanvraag beveiliging**de optie **geen**.

    e. Klik op **Opslaan**.

1. Klik nogmaals op het tabblad **Gebruiker**.

    ![Gebruiker](./media/airwatch-tutorial/ic791926.png "Gebruiker")

1. Voer in het gedeelte **Kenmerk** de volgende stappen uit:

    ![Kenmerk](./media/airwatch-tutorial/ic791927.png "Kenmerk")

    a. Typ `http://schemas.microsoft.com/identity/claims/objectidentifier` in het tekstvak **Object-ID**.

    b. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` in het vak **Gebruikersnaam**.

    c. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` in het vak **Weergavenaam**.

    d. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` in het vak **Voornaam**.

    e. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` in het vak **Achternaam**.

    f. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` in het tekstvak **E-mailadres**.

    g. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan het horloge.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **AirWatch** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-airwatch-test-user"></a>Een testgebruiker maken in AirWatch

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij het gebruik van de functie voor het gebruik van een luchtwatch, moeten ze worden ingericht in het horloge. In het geval van AirWatch is inrichten een handmatige taak.

**Voer de volgende stappen uit om de inrichting van gebruikers te configureren:**

1. Meld u als beheerder aan **bij de bedrijfs** site van uw bedrijf.

2. Klik in het linkernavigatievenster op **Accounts** en vervolgens op **Gebruikers**.
  
   ![Gebruikers](./media/airwatch-tutorial/ic791929.png "Gebruikers")

3. Klik in het menu **gebruikers** op **lijst weergave**en klik vervolgens op **toevoegen > gebruiker toevoegen**.
  
   ![Gebruiker toevoegen](./media/airwatch-tutorial/ic791930.png "Gebruiker toevoegen")

4. Voer in het dialoogvenster **Gebruiker toevoegen/bewerken** de volgende stappen uit:

   ![Gebruiker toevoegen](./media/airwatch-tutorial/ic791931.png "Gebruiker toevoegen")

   a. Typ de **Gebruikersnaam**, het **Wachtwoord**, **Wachtwoord bevestigen**, **Voornaam**, **Achternaam**, **E-mailadres** van een geldig Azure Active Directory-account dat u wilt inrichten in de desbetreffende tekstvakken.

   b. Klik op **Opslaan**.

> [!NOTE]
> U kunt andere hulpprogram ma's voor het maken van gebruikers accounts of Api's die worden geleverd door de gebruiker, gebruiken om Azure AD-gebruikers accounts in te richten.

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel in het toegangs venster selecteert, wordt u automatisch aangemeld bij het horloge waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
