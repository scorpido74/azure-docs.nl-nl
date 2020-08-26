---
title: 'Zelfstudie: Azure Active Directory-integratie met LogicMonitor | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en LogicMonitor configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 4c5906d9cca193129a4213f697815f70ec639d8b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549743"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Zelfstudie: Azure Active Directory-integratie met LogicMonitor

In deze zelfstudie leert u hoe u LogicMonitor kunt integreren met Azure Active Directory (Azure AD).
De integratie van LogicMonitor met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot LogicMonitor.
* U kunt uw gebruikers zich automatisch laten aanmelden bij LogicMonitor (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met LogicMonitor hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op LogicMonitor waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* LogicMonitor ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-logicmonitor-from-the-gallery"></a>LogicMonitor toevoegen vanuit de galerie

Voor het configureren van de integratie van LogicMonitor in Azure AD, moet u LogicMonitor vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u LogicMonitor wilt toevoegen vanuit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **LogicMonitor** in het zoekvak, selecteer **LogicMonitor** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![LogicMonitor in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met LogicMonitor op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in LogicMonitor tot stand is gebracht.

Om Azure AD eenmalige aanmelding met LogicMonitor te configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[LogicMonitor voor eenmalige aanmelding configureren](#configure-logicmonitor-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker maken in LogicMonitor](#create-logicmonitor-test-user)** : als u een tegenhanger van Britta Simon in LogicMonitor wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met LogicMonitor moet u de volgende stappen uitvoeren:

1. Selecteer in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de **LogicMonitor**-toepassing de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij LogicMonitor](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.logicmonitor.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<companyname>.logicmonitor.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van LogicMonitor](https://www.logicmonitor.com/contact/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **LogicMonitor instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-logicmonitor-single-sign-on"></a>Eenmalige aanmelding voor LogicMonitor configureren

1. Meld u aan bij uw **LogicMonitor**-bedrijfssite als beheerder.

2. Klik in het menu aan de bovenkant op **Settings**.

    ![Instellingen](./media/logicmonitor-tutorial/ic790052.png "Instellingen")

3. Klik in het navigatievenster aan de linkerkant op **Eenmalige aanmelding**

    ![Single Sign-On](./media/logicmonitor-tutorial/ic790053.png "Eenmalige aanmelding")

4. Voer de volgende stappen uit in het gedeelte **Instellingen voor eenmalige aanmelding**:

    ![Instellingen voor eenmalige aanmelding](./media/logicmonitor-tutorial/ic790054.png "Instellingen voor eenmalige aanmelding")

    a. Schakel het selectievakje **Eenmalige aanmelding inschakelen** in.

    b. Selecteer voor **Standaard roltoewijzing** de optie **Alleen-lezen**.

    c. Open het gedownloade metagegevensbestand in Kladblok en plak de inhoud van het bestand in het tekstvak **Metagegevens van ID-provider** .

    d. Klik op **Wijzigingen opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot LogicMonitor.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **LogicMonitor**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **LogicMonitor** in de lijst met toepassingen.

    ![De koppeling LogicMonitor in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-logicmonitor-test-user"></a>LogicMonitor-testgebruiker maken

Azure AD-gebruikers moeten worden ingericht voor de LogicMonitor-toepassing met hun Azure Active Directory-gebruikersnaam om zich te kunnen aanmelden.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u aan bij uw LogicMonitor-bedrijfssite als beheerder.

2. Klik in de menubalk bovenaan op **Instellingen** en klik vervolgens op **Rollen en gebruikers**.

    ![Rollen en gebruikers](./media/logicmonitor-tutorial/ic790056.png "Rollen en gebruikers")

3. Klik op **Add**.

4. Voer in de sectie **Een account toevoegen** de volgende stappen uit:

    ![Een account toevoegen](./media/logicmonitor-tutorial/ic790057.png "Een account toevoegen")

    a. Typ waarden voor **Gebruikersnaam**, **E-mail**, **Wachtwoord**en **Herhaal wachtwoord** voor de Azure Active Directory-gebruiker die u wilt inrichten in de bijbehorende tekstvakken.

    b. Selecteer **Rollen**, **Weergavemachtigingen**en de **Status**.

    c. Klik op **Submit**

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van LogicMonitor-gebruikersaccounts of API's van LogicMonitor gebruiken om Azure Active Directory-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel LogicMonitor in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van LogicMonitor waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

