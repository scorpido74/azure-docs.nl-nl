---
title: 'Zelfstudie: Azure Active Directory-integratie met AirWatch | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en AirWatch.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.openlocfilehash: 049066ffc5ce0aea2af956343dfa7ba97b6b5bb4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542816"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Zelfstudie: AirWatch integreren met Azure Active Directory

In deze zelfstudie leert u hoe u AirWatch kunt integreren met Azure Active Directory (Azure AD). Wanneer u AirWatch integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot AirWatch.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij AirWatch.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Een abonnement op AirWatch waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. AirWatch biedt ondersteuning voor **SP** geïnitieerde eenmalige aanmelding.

## <a name="adding-airwatch-from-the-gallery"></a>AirWatch toevoegen vanuit de galerie

Voor het configureren van de integratie van AirWatch in Azure AD moet u AirWatch vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **AirWatch**.
1. Selecteer **AirWatch** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met AirWatch met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in AirWatch.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met AirWatch te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij AirWatch configureren](#configure-airwatch-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[Een testgebruiker maken in AirWatch](#create-airwatch-test-user)**: om in AirWatch een tegenhanger van Britta Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
5. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **AirWatch** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    1. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Typ in het vak **Id (Entiteits-id)** de waarde als: `AirWatch`

    > [!NOTE]
    > Dit is niet de echte waarde. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [het ondersteuningsteam van AirWatch Client](https://www.air-watch.com/company/contact-us/) om deze waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De AirWatch-toepassing verwacht de SAML-asserties in een specifieke indeling. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![image](common/edit-attribute.png)

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

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het XML-bestand met metagegevens te downloaden. Sla dit vervolgens op de computer op.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **AirWatch instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Eenmalige aanmelding (SSO) configureren voor AirWatch

1. Meld u in een ander webbrowservenster bij uw AirWatch-bedrijfssite aan als beheerder.

1. Op de pagina Instellingen. Selecteer **Instellingen > Bedrijfsintegratiek > Adreslijstservice**.

   ![Instellingen](./media/airwatch-tutorial/ic791921.png "Instellingen")

1. Klik op het tabblad **Gebruiker**. Typ uw domeinnaam in het vak **Basis-DN** en klik op **Opslaan**.

   ![Gebruiker](./media/airwatch-tutorial/ic791922.png "Gebruiker")

1. Klik op het tabblad **Server**.

   ![Server](./media/airwatch-tutorial/ic791923.png "Server")

1. Voer in de sectie **LDAP** de volgende stappen uit:

    ![Uploaden](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. Selecteer **Geen** als **Type adressenlijst**.

    b. Selecteer **SAML gebruiken voor verificatie**.

1. Klik in de sectie **SAML 2.0** op **Uploaden** om het gedownloade certificaat te uploaden.

    ![Uploaden](./media/airwatch-tutorial/ic791932.png "Uploaden")

1. Voer in het gedeelte **Aanvraag** de volgende stappen uit:

    ![Aanvraag](./media/airwatch-tutorial/ic791925.png "Aanvraag")  

    a. Selecteer **POST** als **Aanvraagbindingstype**.

    b. In de Azure-portal, op de dialoogpagina **Eenmalige aanmelding bij AirWatch configureren**, kopieert u de waarde van de **Inlog-URL** en plakt u deze in het tekstvak **URL van ID-provider voor eenmalige aanmelding**.

    c. Als **NameID-indeling** selecteert u **E-mailadres**.

    d. Selecteer **Geen** als **Beveiliging van verificatieaanvraag**.

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

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot AirWatch.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **AirWatch** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-airwatch-test-user"></a>Een testgebruiker maken in AirWatch

Om Azure AD-gebruikers zich te laten aanmelden bij AirWatch, moeten ze worden ingericht in AirWatch. In het geval van AirWatch is inrichten een handmatige taak.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u aan bij uw **AirWatch**-bedrijfssite als beheerder.

2. Klik in het linkernavigatievenster op **Accounts** en vervolgens op **Gebruikers**.
  
   ![Gebruikers](./media/airwatch-tutorial/ic791929.png "Gebruikers")

3. Klik in het menu **Gebruikers** op **Lijstweergave**, en klik vervolgens op **Toevoegen > Gebruiker toevoegen**.
  
   ![Gebruiker toevoegen](./media/airwatch-tutorial/ic791930.png "Gebruiker toevoegen")

4. Voer in het dialoogvenster **Gebruiker toevoegen/bewerken** de volgende stappen uit:

   ![Gebruiker toevoegen](./media/airwatch-tutorial/ic791931.png "Gebruiker toevoegen")

   a. Typ de **Gebruikersnaam**, het **Wachtwoord**, **Wachtwoord bevestigen**, **Voornaam**, **Achternaam**, **E-mailadres** van een geldig Azure Active Directory-account dat u wilt inrichten in de desbetreffende tekstvakken.

   b. Klik op **Opslaan**.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het creëren van AirWatch-gebruikersaccounts of API's van AirWatch gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u de tegel AirWatch in het toegangsvenster selecteert, zou u automatisch moeten worden aangemeld bij de instantie van AirWatch waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
