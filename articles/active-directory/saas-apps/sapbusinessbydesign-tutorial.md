---
title: 'Zelfstudie: Azure Active Directory-integratie met SAP Business ByDesign | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en SAP Business ByDesign configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 6959b7e819ad906f04a18661d6bd4aa2d82f8984
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543353"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Zelfstudie: Azure Active Directory-integratie met SAP Business ByDesign

In deze zelfstudie leert u hoe u SAP Business ByDesign kunt integreren met Azure Active Directory (Azure AD).
De integratie van SAP Business ByDesign met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot SAP Business ByDesign.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij SAP Business ByDesign (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van de Azure AD-integratie met SAP Business ByDesign hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Abonnement op SAP Business ByDesign waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP Business ByDesign biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>SAP Business ByDesign toevoegen vanuit de galerie

Voor het configureren van de integratie van SAP Business ByDesign met Azure AD moet u SAP Business ByDesign vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Ga als volgt te werk om SAP Business ByDesign vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SAP Business ByDesign** in het zoekvak, selecteer **SAP Business ByDesign** in het deelvenster met resultaten en klik vervolgens op **Toevoegen** om de toepassing toe te voegen.

    ![SAP Business ByDesign toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met SAP Business ByDesign op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in SAP Business ByDesign tot stand is gebracht.

Om eenmalige aanmelding bij SAP Business ByDesign met Azure AD te configureren en testen, moet u de volgende procedures voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij SAP Business ByDesign configureren](#configure-sap-business-bydesign-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor SAP Business ByDesign maken](#create-sap-business-bydesign-test-user)** : als u een tegenhanger van Britta Simon in SAP Business ByDesign wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met SAP Business ByDesign moet u de volgende stappen uitvoeren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de overzichtspagina van de integratie voor **SAP Business ByDesign** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij SAP Business ByDesign](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<servername>.sapbydesign.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Voor de SAP Business ByDesign-toepassing worden de SAML-asserties in een bepaalde indeling verwacht. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![image](common/edit-attribute.png)

6. Klik op het pictogram **Bewerken** om de **waarde van Naam-ID** te bewerken.

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Voer in de sectie **Gebruikersclaims beheren** de volgende stappen uit: ![afbeelding](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Selecteer **Transformatie** als **bron**.

    b. Selecteer in de vervolgkeuzelijst **Transformatie** **ExtractMailPrefix()** .

    c. Selecteer in de vervolgkeuzelijst **Parameter 1** het gebruikerskenmerk dat u wilt gebruiken voor uw implementatie. Als u bijvoorbeeld de werknemer-id wilt gebruiken als unieke gebruikers-id en u de waarde van het kenmerk in de ExtensionAttribute2 hebt opgeslagen, selecteert u vervolgens user.extensionattribute2.

    d. Klik op **Opslaan**.

8. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

9. Kopieer onder **SAP Business ByDesign instellen** de URL('s) die u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Eenmalige aanmelding van SAP Business ByDesign configureren

1. Meld u aan bij uw SAP Business ByDesign-portal met beheerdersrechten.

2. Navigeer naar **Algemene toepassings- en gebruikersbeheertaak** en klik op het tabblad **ID-provider**.

3. Klik op **Nieuwe ID-provider** en selecteer het XML-bestand voor metagegevens dat u hebt gedownload vanuit Azure Portal. Door het importeren van de metagegevens worden automatisch het vereiste handtekeningcertificaat en versleutelingscertificaat geüpload.

    ![Eenmalige aanmelding configureren](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Selecteer **Assertion Consumer Service URL opnemen** als u de **Assertion Consumer Service URL** in de SAML-aanvraag wilt opnemen.

5. Klik op **Eenmalige aanmelding activeren**.

6. Sla uw wijzigingen op.

7. Klik op het tabblad **Mijn systeem**.

    ![Eenmalige aanmelding configureren](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Plak in het tekstvak **Azure AD-aanmeldings-URL** de waarde van de **aanmeldings-URL** die u uit de Azure Portal hebt gekopieerd.

    ![Eenmalige aanmelding configureren](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Geef aan of de werknemer handmatig kan kiezen tussen het aanmelden met gebruikersnaam en wachtwoord of met eenmalige aanmelding door het selecteren van de **Handmatige selectie van ID-provider**.

10. Geef in de sectie **SSO-URL** de URL op die moet worden gebruikt door de werknemer om zich aan te melden bij het systeem.
    In de vervolgkeuzelijst URL verzonden naar de werknemer kunt u kiezen tussen de volgende opties:

    **Niet-SSO-URL**

    Alleen de normale systeem-URL wordt naar de werknemer verstuurd. De werknemer kan niet aanmelden met eenmalige aanmelding, maar moet in plaats daarvan gebruikmaken van een wachtwoord of certificaat.

    **SSO-URL**

    Alleen de SSO-URL wordt naar de werknemer verzonden. De werknemer kan zich aanmelden met behulp van eenmalige aanmelding. Een verificatieaanvraag wordt omgeleid via de IdP.

    **Automatische selectie**

    Als eenmalige aanmelding niet actief is, wordt alleen de normale systeem-URL naar de werknemer verstuurd. Als eenmalige aanmelding actief is, controleert het systeem of de werknemer een wachtwoord heeft. Als er een wachtwoord beschikbaar is, worden zowel de SSO-URL als de niet-SSO-URL naar de werknemer verzonden. Als de werknemer geen wachtwoord heeft, wordt echter alleen de URL voor eenmalige aanmelding naar de werknemer verzonden.

11. Sla uw wijzigingen op.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie zorgt u ervoor dat Britta Simon van eenmalige aanmelding met Azure gebruik kan maken door haar toegang te verlenen tot SAP Business ByDesign.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **SAP Business ByDesign**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SAP Business ByDesign** in de lijst met toepassingen.

    ![De koppeling SAP Business ByDesign in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sap-business-bydesign-test-user"></a>Testgebruiker voor SAP Business ByDesign maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in SAP Business ByDesign. Werk samen met het [klantondersteuningsteam van SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) om de gebruikers toe te voegen in het SAP Business ByDesign-platform. 

> [!NOTE]
> Zorg ervoor dat NameID-waarde met het gebruikersnaamveld in het SAP Business ByDesign-platform overeenkomt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SAP Business ByDesign klikt in het toegangsvenster, wordt u automatisch aangemeld bij het exemplaar van SAP Business ByDesign waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
