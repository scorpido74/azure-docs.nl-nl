---
title: 'Zelf studie: integratie Azure Active Directory met Palo Alto-netwerken-gebruikers interface voor beheer | Microsoft Docs'
description: Hier vindt u informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Palo Alto Networks Admin UI.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c77678c20cb9eed01e2cfa86014963f453a4839
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160181"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Zelf studie: integratie Azure Active Directory met Palo Alto-netwerken-gebruikers interface van beheerder

In deze zelfstudie leert u hoe u Palo Alto Networks - Admin UI integreert met Azure Active Directory (Azure AD).
De integratie van Palo Alto Networks - Admin UI met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang tot Palo Alto Networks - Admin UI heeft.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Palo Alto Networks - Admin UI (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met Palo Alto Networks - Admin UI te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor Palo Alto Networks - Admin UI waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Palo Alto Networks - Admin UI ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* Palo Alto Networks - Admin UI ondersteunt **just-in-time**-gebruikersinrichting

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Palo Alto Networks - Admin UI toevoegen vanuit de galerie

Als u de integratie van Palo Alto Networks - Admin UI met Azure AD wilt configureren, moet u Palo Alto Networks - Admin UI vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Palo Alto Networks - Admin UI vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Palo Alto Networks - Admin UI**, selecteer **Palo Alto Networks - Admin UI** in het deelvenster met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Palo Alto Networks - Admin UI toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Palo Alto Networks - Admin UI op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Palo Alto Networks - Admin UI tot stand is gebracht.

Voer de volgende procedures uit om eenmalige aanmelding van Azure AD met Palo Alto Networks - Admin UI te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met Palo Alto Networks - Admin UI configureren](#configure-palo-alto-networks---admin-ui-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Palo Alto Networks - Admin UI maken](#create-palo-alto-networks---admin-ui-test-user)** : als u een tegenhanger van Britta Simon in Palo Alto Networks - Admin UI wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Palo Alto Networks - Admin UI te configureren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina van **Palo Alto Networks - Admin UI** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor Palo Alto Networks - Admin UI-domein en -URL's](common/sp-identifier-reply.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<Customer Firewall FQDN>/php/login.php`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. In he tekstvak **Antwoord-URL** typt u de URL van ACS (Assertion Consumer Service) in de volgende indeling: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [ondersteuningsteam van Palo Alto Networks - Admin UI](https://support.paloaltonetworks.com/support) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. De toepassing Palo Alto Networks - Admin UI verwacht de SAML-asserties in een specifieke indeling. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![image](common/edit-attribute.png)

   > [!NOTE]
   > Omdat de kenmerkwaarden slechts voorbeelden zijn, moet u de juiste waarden opgeven voor *username* en *adminrole*. Er is een ander optioneel kenmerk, *accessdomain*, dat wordt gebruikt om beheerderstoegang tot specifieke virtuele systemen in de firewall te beperken.
   >

6. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** configureert u het kenmerk van het SAML-token zoals wordt weergegeven in de bovenstaande afbeelding en voert u de volgende stappen uit:

    | Naam |  Bronkenmerk|
    | --- | --- |
    | gebruikersnaam | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

    > [!NOTE]
    > Zie de volgende artikelen voor meer informatie over de kenmerken:
    > * [Configure an Admin Role Profile (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Device > Access Domain (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)

7. Ga op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** en klik op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

8. In het gedeelte **Palo Alto Networks - Admin UI instellen** kopieert u de URL('s) die u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-palo-alto-networks---admin-ui-single-sign-on"></a>Eenmalige aanmelding voor Palo Alto Networks - Admin UI configureren

1. Open de gebruikersinterface voor firewallbeheer van Palo Alto Networks als beheerder in een nieuw browservenster.

2. Selecteer het tabblad **Device**.

    ![Het tabblad Device](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Selecteer **SAML Identity Provider** in het linkerdeelvenster en selecteer vervolgens **Import** om het metagegevensbestand te importeren.

    ![De knop voor het importeren van het bestand met metagegevens](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Ga als volgt te werk in het venster **SAML Identify Provider Server Profile Import**:

    ![Het venster SAML Identify Provider Server Profile Import](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Typ een naam in het vak **Profile Name** (bijvoorbeeld **AzureAD Admin UI**).
    
    b. Selecteer **Browse** naast **Identity Provider Metadata** en selecteer het bestand metadata.xml file dat u eerder uit de Azure-portal hebt gedownload.
    
    c. Schakel het selectievakje **Validate Identity Provider Certificate** uit.
    
    d. Selecteer **OK**.
    
    e. Selecteer **Commit** om de configuraties door te voeren op de firewall.

5. Selecteer in het linkerdeelvenster **SAML Identity Provider** en selecteer vervolgens het profiel van de SAML-identiteitsprovider (bijvoorbeeld **AzureAD Admin UI**) dat u in de vorige stap hebt gemaakt.

    ![Het profiel van de SAML-identiteitsprovider](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Ga als volgt te werk in het venster **SAML Identify Provider Server**:

    ![Het venster SAML Identify Provider Server](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Vervang in het vak **Identity Provider SLO URL** de eerder geïmporteerde SLO-URL door de volgende URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Selecteer **OK**.

7. Selecteer in de gebruikersinterface voor firewallbeheer van Palo Alto Networks **Device** en selecteer vervolgens **Admin Roles**.

8. Selecteer de knop **Add**.

9. Geef in het venster **Admin Role Profile**, in het vak **Name**, een naam op voor de beheerdersrol (bijvoorbeeld **fwadmin**). De naam van de beheerdersrol moet overeenkomen met de naam van het kenmerk SAML Admin Role dat is verzonden door de id-provider. De naam en waarde van de beheerdersrol zijn gemaakt in de sectie **Gebruikerskenmerken** in de Azure-portal.

    ![Beheerdersrol van Palo Alto Networks configureren](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. Selecteer in de gebruikersinterface voor firewallbeheer **Device** en vervolgens **Authentication Profile**.

11. Selecteer de knop **Add**.

12. Ga als volgt te werk in het venster **Authentication Profile**: 

    ![Het venster Authentication Profile](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Typ een naam in het vak **Name** (bijvoorbeeld **AzureSAML_Admin_AuthProfile**).

    b. Selecteer **SAML** in de vervolgkeuzelijst **Type**. 

    c. Selecteer in de vervolgkeuzelijst **IdP Server Profile** het juiste serverprofiel van de SAML-identiteitsprovider (bijvoorbeeld **AzureAD Admin UI**).

    c. Schakel het selectievakje **Enable Single Logout** in.

    d. Voer in het vak **Admin Role Attribute** de naam van het kenmerk in (bijvoorbeeld **adminrole**).

    e. Selecteer het tabblad **Advanced** en selecteer vervolgens **Add** onder **Allow List**.

    ![De knop Add op het tabblad Advanced](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Schakel het selectievakje **All** in of selecteer de gebruikers en groepen die kunnen worden geverifieerd met dit profiel.  
    Wanneer een gebruiker zich wil verifiëren, vergelijkt de firewall de gekoppelde gebruikersnaam of groep met de vermeldingen in deze lijst. Als u geen vermeldingen toevoegt, kunnen er geen gebruikers worden geverifieerd.

    g. Selecteer **OK**.

13. Om beheerders in staat te stellen eenmalige aanmelding van SAML te gebruiken via Azure, selecteert u **Device** > **Setup**. Selecteer in het deelvenster **Setup** het tabblad **Management** en selecteer vervolgens onder **Authentication Settings** de knop **Settings** (tandwielpictogram).

    ![De knop Settings](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Selecteer het profiel voor SAML-verificatie dat u in het venster Authentication Profile hebt gemaakt (bijvoorbeeld **AzureSAML_Admin_AuthProfile**).

    ![Het veld Authentication Profile](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Selecteer **OK**.

16. Selecteer **Commit** om de configuratie vast te leggen.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. Typ in het veld **gebruikers naam** **brittasimon\@yourcompanydomain. extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Palo Alto Networks - Admin UI.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Palo Alto Networks - Admin UI**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Palo Alto Networks - Admin UI** in de lijst met toepassingen.

    ![De koppeling Palo Alto Networks - Admin UI in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Testgebruiker voor Palo Alto Networks - Admin UI maken

Palo Alto Networks - Admin UI ondersteunt just-in-time-gebruikersinrichting. Als een gebruiker nog niet bestaat, wordt deze na geslaagde verificatie automatisch gemaakt in het systeem. Er is geen actie van u vereist om de gebruiker te maken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel van Palo Alto Networks - Admin UI klikt in het toegangsvenster, moet u automatisch worden aangemeld bij de instantie van Palo Alto Networks - Admin UI waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
