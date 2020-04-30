---
title: 'Zelf studie: integratie Azure Active Directory met SAP Business ByDesign | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b830f0760b768826b2d937b4a8b2ffbd8e9e2a2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67091678"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Zelfstudie: Azure Active Directory-integratie met SAP Business ByDesign

In deze zelf studie leert u hoe u SAP Business ByDesign integreert met Azure Active Directory (Azure AD).
Het integreren van SAP Business ByDesign met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot SAP Business ByDesign.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij SAP Business ByDesign (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met SAP Business ByDesign wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor eenmalige aanmelding voor SAP Business ByDesign

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP Business ByDesign ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>SAP Business ByDesign toevoegen vanuit de galerie

Als u de integratie van SAP Business ByDesign wilt configureren in azure AD, moet u SAP Business ByDesign toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SAP Business ByDesign toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SAP Business ByDesign**in het zoekvak, selecteer **SAP Business ByDesign** van result panel en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

    ![SAP Business ByDesign in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD met SAP Business ByDesign configureren en testen op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAP Business ByDesign tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met SAP Business ByDesign, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. Configureer de eenmalige **[aanmelding voor SAP Business ByDesign](#configure-sap-business-bydesign-single-sign-on)** -voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een gebruiker van SAP Business ByDesign test](#create-sap-business-bydesign-test-user)** , zodat deze een equivalent is van Julia Simon in SAP Business ByDesign dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met SAP Business ByDesign:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **SAP Business ByDesign** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor SAP Business ByDesign-domein en Url's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<servername>.sapbydesign.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteunings team van SAP Business ByDesign-client](https://www.sap.com/products/cloud-analytics.support.html) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. SAP Business ByDesign-toepassing verwacht de SAML-beweringen in een specifieke indeling. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![installatiekopie](common/edit-attribute.png)

6. Klik op het **bewerkings** pictogram om de **naam-id-waarde**te bewerken.

    ![installatiekopie](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Voer de volgende stappen uit in de sectie **gebruikers claims beheren** : ![afbeelding](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Selecteer **trans formatie** als **bron**.

    b. Selecteer in de vervolg keuzelijst **Transformeren** de optie **ExtractMailPrefix ()**.

    c. Selecteer in de vervolg keuzelijst **para meter 1** het gebruikers kenmerk dat u wilt gebruiken voor uw implementatie. Als u bijvoorbeeld de werknemer-id wilt gebruiken als unieke gebruikers-id en u de waarde van het kenmerk in de ExtensionAttribute2 hebt opgeslagen, selecteert u vervolgens user.extensionattribute2.

    d. Klik op **Opslaan**.

8. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

9. Kopieer op de sectie **SAP Business ByDesign instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Eenmalige aanmelding voor SAP Business ByDesign configureren

1. Meld u aan bij uw SAP Business ByDesign-Portal met beheerders rechten.

2. Ga naar de **algemene taak voor toepassings-en gebruikers beheer** en klik op het tabblad **ID-provider** .

3. Klik op **nieuwe ID-provider** en selecteer het XML-bestand met meta gegevens dat u hebt gedownload van de Azure Portal. Door het importeren van de metagegevens worden automatisch het vereiste handtekeningcertificaat en versleutelingscertificaat geüpload.

    ![Eenmalige aanmelding configureren](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Als u de URL van de service voor de **Bewerings verbruiker** wilt toevoegen aan de SAML-aanvraag, selecteert u de service-URL voor de **Assertion Consumer**.

5. Klik op **Eenmalige aanmelding activeren**.

6. Sla uw wijzigingen op.

7. Klik op het tabblad **Mijn systeem**.

    ![Eenmalige aanmelding configureren](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Plak in het tekstvak **URL voor aanmelden bij Azure AD** de waarde voor de **aanmeldings-URL** die u hebt gekopieerd van de Azure Portal.

    ![Eenmalige aanmelding configureren](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Geef op of de werk nemer hand matig kan kiezen tussen het aanmelden met de gebruikers-ID en het wacht woord of SSO door de selectie van de **hand matige ID-provider**te selecteren.

10. Geef in de sectie **SSO-URL** de URL op die moet worden gebruikt door de werk nemer om te aanmelding aan het systeem.
    In de vervolg keuzelijst URL verzonden naar werk nemer kunt u kiezen uit de volgende opties:

    **Niet-SSO-URL**

    Alleen de normale systeem-URL wordt naar de werknemer verstuurd. De werk nemer kan geen aanmelding met SSO en moet in plaats daarvan het wacht woord of certificaat gebruiken.

    **SSO-URL**

    Alleen de SSO-URL wordt naar de werknemer verzonden. De werk nemer kan aanmelding via SSO. Een verificatieaanvraag wordt omgeleid via de IdP.

    **Automatische selectie**

    Als eenmalige aanmelding niet actief is, wordt alleen de normale systeem-URL naar de werknemer verstuurd. Als eenmalige aanmelding actief is, controleert het systeem of de werknemer een wachtwoord heeft. Als er een wachtwoord beschikbaar is, worden zowel de SSO-URL als de niet-SSO-URL naar de werknemer verzonden. Als de werknemer geen wachtwoord heeft, wordt echter alleen de URL voor eenmalige aanmelding naar de werknemer verzonden.

11. Sla uw wijzigingen op.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ `brittasimon@yourcompanydomain.extension`in het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SAP Business ByDesign.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **SAP Business ByDesign**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SAP Business ByDesign**in de lijst toepassingen.

    ![De koppeling SAP Business ByDesign in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sap-business-bydesign-test-user"></a>Een SAP Business ByDesign-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in SAP Business ByDesign. Neem contact op met het [team voor client ondersteuning van SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) om de gebruikers toe te voegen in het SAP Business ByDesign-platform. 

> [!NOTE]
> Controleer of de NameID-waarde moet overeenkomen met het veld username in het SAP Business ByDesign-platform.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SAP Business ByDesign klikt in het toegangs venster, moet u automatisch worden aangemeld bij de SAP Business ByDesign waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
