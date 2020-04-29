---
title: 'Zelf studie: integratie Azure Active Directory met RStudio Connect | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en RStudio Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9bc78022-6d38-4476-8f03-e3ca2551e72e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb5dd845b03bd94f0a94db50c01b804cf6f55c2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81407102"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect"></a>Zelf studie: integratie Azure Active Directory met RStudio Connect

In deze zelf studie leert u hoe u RStudio kunt integreren met Azure Active Directory (Azure AD).
Het integreren van RStudio-verbinding met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot RStudio Connect.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld voor RStudio Connect (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met RStudio Connect wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* RStudio verbinding maken. Er is een [gratis evaluatie versie van 45 dagen.](https://www.rstudio.com/products/connect/)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* RStudio Connect ondersteunt door **SP en IDP** GEÏNITIEERDe SSO

* RStudio Connect ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-rstudio-connect-from-the-gallery"></a>RStudio Connect toevoegen vanuit de galerie

Als u de integratie van RStudio Connect wilt configureren in azure AD, moet u RStudio Connect toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om RStudio Connect toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **RStudio Connect**in het zoekvak, selecteer **RStudio verbinding maken** via resultaten paneel en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

    ![RStudio Connect in de resultaten lijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met RStudio Connect op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in RStudio verbinding tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met RStudio Connect, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[RStudio Connect eenmalige aanmelding configureren](#configure-rstudio-connect-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een RStudio-gebruiker](#create-rstudio-connect-test-user)** voor het maken van een verbinding. Als u een equivalent van Julia Simon wilt hebben in RStudio Connect dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met RStudio Connect:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **RStudio Connect** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus in **IDP** wilt configureren, voert u de volgende stappen uit in de sectie `<example.com>` **basis configuratie van SAML** , waarbij u vervangt door het adres en de poort van uw RStudio Connect-server:

    ![Informatie over RStudio verbinden domein en Url's eenmalige aanmelding](common/idp-intiated.png)

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://<example.com>/__login__/saml`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<example.com>/__login__/saml/acs`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Informatie over RStudio verbinden domein en Url's eenmalige aanmelding](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<example.com>/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Ze worden bepaald op basis van het RStudio Connect-`https://example.com` server adres (in de bovenstaande voor beelden). Neem contact op met het [ondersteunings team van RStudio Connect](mailto:support@rstudio.com) als u problemen ondervindt. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Uw RStudio Connect-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. RStudio Connect-toepassing verwacht dat **nameidentifier** moet worden toegewezen aan **User. mail**, dus u moet de kenmerk toewijzing bewerken door op het pictogram **bewerken** te klikken en de kenmerk toewijzing te wijzigen.

    ![installatiekopie](common/edit-attribute.png)

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-rstudio-connect-single-sign-on"></a>Eenmalige aanmelding met RStudio-verbinding configureren

Als u eenmalige aanmelding wilt configureren voor **RStudio Connect**, moet u de URL van de **app-federatieve meta gegevens** en het **server adres** gebruiken dat hierboven wordt gebruikt. Dit doet u in het RStudio Connect-configuratie bestand `/etc/rstudio-connect.rstudio-connect.gcfg`op.

Dit is een voor beeld van een configuratie bestand:

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

Sla uw **server adres** op in `Server.Address` de waarde en de **URL voor de federatieve meta gegevens** van de app in de `SAML.IdPMetaData` waarde. Houd er rekening mee dat deze voorbeeld configuratie een niet-versleutelde HTTP-verbinding gebruikt, terwijl Azure AD het gebruik van een versleutelde HTTPS-verbinding vereist. U kunt een [omgekeerde proxy](https://docs.rstudio.com/connect/admin/proxy/) gebruiken vóór RStudio Connect of Configure RStudio Connect om [https rechtstreeks te gebruiken](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS). 

Als u problemen ondervindt met de configuratie, kunt u de [RStudio Connect-beheer handleiding](https://docs.rstudio.com/connect/admin/authentication/saml/) lezen of het [ondersteunings team van RStudio](mailto:support@rstudio.com) voor hulp e-mailen.

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

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan RStudio Connect.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **RStudio CONNECT**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **RStudio Connect**.

    ![De koppeling RStudio Connect in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-rstudio-connect-test-user"></a>Een test gebruiker voor RStudio-verbinding maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in RStudio Connect. RStudio Connect biedt ondersteuning voor Just-in-time-inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in RStudio Connect, wordt er een nieuwe gemaakt wanneer u probeert verbinding te maken met RStudio Connect.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel RStudio Connect in het toegangs venster klikt, moet u automatisch worden aangemeld bij de RStudio-verbinding waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

