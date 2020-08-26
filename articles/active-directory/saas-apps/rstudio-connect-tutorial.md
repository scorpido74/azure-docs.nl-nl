---
title: 'Zelfstudie: Integratie van Azure Active Directory met RStudio Connect | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en RStudio Connect kunt configureren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: 80d444e9887a3aaa48262ccf59350b0108d7d709
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543632"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect"></a>Zelfstudie: Integratie van Azure Active Directory met RStudio Connect

In deze zelfstudie leert u hoe u RStudio Connect kunt integreren met Azure Active Directory (Azure AD).
Integratie van RStudio Connect met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot RStudio Connect.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij RStudio Connect (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor de configuratie van Azure AD-integratie met RStudio Connect hebt u het volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* RStudio Connect. Er is een [gratis proefperiode van 45 dagen.](https://www.rstudio.com/products/connect/)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* RStudio Connect ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

* RStudio Connect biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-rstudio-connect-from-the-gallery"></a>RStudio Connect toevoegen vanuit de galerie

Voor het configureren van de integratie van RStudio Connect in Azure AD, moet u RStudio Connect vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

**Als u RStudio Connect wilt toevoegen vanuit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **RStudio Connect** in het zoekvak, selecteer **RStudio Connect** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![RStudio Connect in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met RStudio Connect met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in RStudio Connect tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD met RStudio Connect wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[RStudio Connect voor eenmalige aanmelding configureren](#configure-rstudio-connect-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[RStudio Connect-testgebruiker maken](#create-rstudio-connect-test-user)** : als tegenhanger van Britta Simon in RStudio Connect die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding van Azure AD met RStudio Connect wilt configureren:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de pagina voor de integratie van de toepassing **RStudio Connect** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren, waarbij u `<example.com>` vervangt door het adres en de poort van uw RStudio Connect-server:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij RStudio Connect](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<example.com>/__login__/saml`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<example.com>/__login__/saml/acs`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij RStudio Connect](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<example.com>/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Deze worden bepaald op basis van het RStudio Connect-serveradres (`https://example.com` in de bovenstaande voorbeelden). Neem contact op met het [ondersteuningsteam van RStudio Connect](mailto:support@rstudio.com) als u problemen ondervindt. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Uw RStudio Connect-toepassing verwacht de SAML-asserties in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de RStudio Connect-toepassing wordt verwacht dat **nameidentifier** is toegewezen aan **user.mail**. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![image](common/edit-attribute.png)

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-rstudio-connect-single-sign-on"></a>RStudio Connect configureren voor eenmalige aanmelding

Als u eenmalige aanmelding wilt configureren voor **RStudio Connect**, moet u de **App-URL voor federatieve metagegevens** en het **Serveradres** gebruiken die hierboven zijn gebruikt. Dit doet u in het RStudio Connect-configuratiebestand in `/etc/rstudio-connect.rstudio-connect.gcfg`.

Dit is een voorbeeld van een configuratiebestand:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Sla uw **Serveradres** op in de waarde `Server.Address` en de **App-URL voor federatieve metagegevens** in de waarde `SAML.IdPMetaData`. Let op: in deze voorbeeldconfiguratie wordt een niet-versleutelde HTTP-verbinding gebruikt, terwijl Azure AD het gebruik van een versleutelde HTTPS-verbinding vereist. U kunt een [omgekeerde proxy](https://docs.rstudio.com/connect/admin/proxy/) gebruiken vóór RStudio Connect of RStudio Connect configureren om [HTTPS direct te gebruiken](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS). 

Als u problemen ondervindt met de configuratie, kunt u de [beheerdershandleiding voor RStudio Connect](https://docs.rstudio.com/connect/admin/authentication/saml/) lezen of een e-mail sturen aan het [ondersteuningsteam van RStudio](mailto:support@rstudio.com) voor hulp.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot RStudio Connect.

1. Selecteer in Azure Portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **RStudio Connect**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **RStudio Connect** in de lijst met toepassingen.

    ![De RStudio Connect-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-rstudio-connect-test-user"></a>Een testgebruiker voor RStudio Connect maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in RStudio Connect. RStudio Connect biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in RStudio Connect, wordt er een nieuwe gemaakt wanneer u RStudio Connect opent.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel RStudio Connect in het toegangsvenster klikt, wordt u automatisch aangemeld bij het exemplaar van RStudio Connect waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

