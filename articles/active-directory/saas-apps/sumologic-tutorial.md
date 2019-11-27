---
title: 'Zelf studie: integratie Azure Active Directory met SumoLogic | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 7548d7d7b808472b3f5446fadfe800584f61b1df
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233356"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Zelf studie: integratie Azure Active Directory met SumoLogic

In deze zelf studie leert u hoe u SumoLogic integreert met Azure Active Directory (Azure AD).
Het integreren van SumoLogic met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot SumoLogic.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij SumoLogic (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met SumoLogic wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding SumoLogic ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SumoLogic ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-sumologic-from-the-gallery"></a>SumoLogic toevoegen uit de galerie

Als u de integratie van SumoLogic in azure AD wilt configureren, moet u SumoLogic uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SumoLogic toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SumoLogic**in het zoekvak, selecteer **SumoLogic** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![SumoLogic in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met SumoLogic op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SumoLogic tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met SumoLogic, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[SumoLogic eenmalige aanmelding configureren](#configure-sumologic-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een SumoLogic-test gebruiker](#create-sumologic-test-user)** -om een equivalent van Julia Simon in SumoLogic te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met SumoLogic:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **SumoLogic** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor SumoLogic domein en Url's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenantname>.SumoLogic.com`

   b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon:

    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteunings team van SumoLogic-clients](https://www.sumologic.com/contact-us/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer op de sectie **SumoLogic instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-sumologic-single-sign-on"></a>Eenmalige aanmelding voor SumoLogic configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw SumoLogic-bedrijfs site.

1. Ga naar **beheer \> beveiliging**.

    ![Beheren](./media/sumologic-tutorial/ic778556.png "Beheren")

1. Klik op **SAML**.

    ![Globale beveiligings instellingen](./media/sumologic-tutorial/ic778557.png "Globale beveiligings instellingen")

1. Selecteer in de **een configuratie selecteren of een nieuwe lijst maken de** optie **Azure AD**en klik vervolgens op **configureren**.

    ![SAML 2,0 configureren](./media/sumologic-tutorial/ic778558.png "SAML 2,0 configureren")

1. Voer de volgende stappen uit in het dialoog venster **SAML 2,0 configureren** :

    ![SAML 2,0 configureren](./media/sumologic-tutorial/ic778559.png "SAML 2,0 configureren")

    a. In het tekstvak **configuratie naam** typt u **Azure AD**.

    b. Selecteer de **foutopsporingsmodus**.

    c. Plak in het tekstvak **Uitgever** de waarde van de **Azure ad-id**die u van Azure Portal hebt gekopieerd.

    d. Plak de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd in het tekstvak **Authe aanvraag-URL** .

    e. Open uw met base 64 versleutelde certificaat in Klad blok, kopieer de inhoud ervan naar het klem bord en plak het hele certificaat in het tekstvak **X. 509-certificaat** .

    f. Selecteer als **e-mail kenmerk** **gebruik van SAML subject**.  

    g. Selecteer door **SP geïnitieerde aanmeldings configuratie**.

    h. Typ in het tekstvak **aanmeldings pad** **Azure** en klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension** .  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SumoLogic.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **SumoLogic**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **SumoLogic**.

    ![De koppeling SumoLogic in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sumologic-test-user"></a>SumoLogic-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij SumoLogic, moeten ze worden ingericht op SumoLogic. In het geval van SumoLogic is inrichting een hand matige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **SumoLogic** -Tenant.

1. Ga naar **Beheren \> Gebruikers**.

    ![Gebruikers](./media/sumologic-tutorial/ic778561.png "Gebruikers")

1. Klik op **Toevoegen**.

    ![Gebruikers](./media/sumologic-tutorial/ic778562.png "Gebruikers")

1. Voer de volgende stappen uit in het dialoog venster **nieuwe gebruiker** :

    ![Nieuwe gebruiker](./media/sumologic-tutorial/ic778563.png "Nieuwe gebruiker") 

    a. Typ de gerelateerde gegevens van het Azure AD-account dat u wilt inrichten in de tekst vakken **voor naam**, **Achternaam**en **e-mail adres** .
  
    b. Selecteer een rol.
  
    c. Selecteer **actief**als **status**.
  
    d. Klik op **Opslaan**.

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van SumoLogic-gebruikers accounts of Api's die worden geleverd door SumoLogic, gebruiken om Azure AD-gebruikers accounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SumoLogic in het toegangs venster klikt, moet u automatisch worden aangemeld bij de SumoLogic waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

