---
title: 'Zelf studie: integratie met SAP Cloud platform identiteits verificatie Azure Active Directory | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Cloud Platform-identiteitsverificatie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 196de1cacb977305c778e16182b21c8dcfafe13e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161175"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Zelf studie: integratie met SAP Cloud platform identiteits verificatie Azure Active Directory

In deze zelfstudie leert u hoe u SAP Cloud Platform-identiteitsverificatie met Azure Active Directory (Azure AD) kunt integreren.
De integratie van SAP Cloud Platform-identiteitsverificatie met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot SAP Cloud Platform-identiteitsverificatie.
* U kunt uw gebruikers zich automatisch laten aanmelden bij SAP Cloud Platform-identiteitsverificatie (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van de Azure AD-integratie met SAP Cloud Platform-identiteitsverificatie hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op SAP Cloud Platform-identiteitsverificatie waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP Cloud Platform-identiteitsverificatie biedt ondersteuning voor door **SP** en **IDP** gestarte eenmalige aanmelding

Voordat u de technische details induikt, is van het belang dat u de concepten begrijpt die u gaat bekijken. Met de SAP Cloud Platform-identiteitsverificatie en Active Directory Federation Services kunt u eenmalige aanmelding uitvoeren op toepassingen of services die worden beveiligd door Azure AD (als een IdP) met SAP-toepassingen en -services die worden beveiligd door SAP-Cloud Platform-identiteitsverificatie.

Momenteel fungeert SAP Cloud Platform-identiteitsverificatie als een proxy-id-provider voor SAP-toepassingen. Azure Active Directory fungeert op zijn beurt als de voornaamste id-provider in deze installatie. 

Het volgende diagram illustreert deze relatie:

![Een Azure AD-testgebruiker maken](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Met deze instelling wordt uw tenant voor SAP Cloud Platform-identiteitsverificatie geconfigureerd als een vertrouwde toepassing in Azure Active Directory.

Alle SAP-toepassingen en -services die u op deze manier wilt beveiligen worden vervolgens geconfigureerd in de beheerconsole voor SAP Cloud Platform-identiteitsverificatie.

Daarom dient de autorisatie voor het verlenen van toegang tot de SAP-toepassingen en -services plaats te vinden in SAP Cloud Platform-identiteitsverificatie (in plaats van Azure Active Directory).

Door SAP Cloud Platform-identiteitsverificatie te configureren als een toepassing via de Azure Active Directory Marketplace, hoeft u geen afzonderlijke claims of SAML-asserties te configureren.

> [!NOTE]
> Momenteel is alleen eenmalige aanmelding bij het web door beide partijen getest. De stromen die nodig voor app-naar-API- of API-naar-API-communicatie zouden moeten werken, maar zijn nog niet getest. Ze worden tijdens latere activiteiten getest.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>SAP Cloud Platform-identiteitsverificatie uit de galerie toevoegen

Voor het configureren van de integratie van SAP Cloud Platform-identiteitsverificatie met Azure AD moet u SAP Cloud Platform-identiteitsverificatie uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u SAP Cloud Platform-identiteitsverificatie uit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **SAP Cloud Platform-identiteitsverificatie**, selecteer **SAP Cloud Platform-identiteitsverificatie** in het deelvenster met resultaten en klik vervolgens op **Toevoegen** om de toepassing toe te voegen.

     ![SAP Cloud Platform-identiteitsverificatie in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met [toepassingsnaam] configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerd gebruiker in [toepassingsnaam] tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met [toepassingsnaam] wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor SAP Cloud Platform-identiteitsverificatie configureren](#configure-sap-cloud-platform-identity-authentication-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor SAP Cloud Platform-identiteitsverificatie maken](#create-sap-cloud-platform-identity-authentication-test-user)** : als u een equivalent van Britta Simon in SAP Cloud Platform-identiteitsverificatie wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met [toepassingsnaam], moet u de volgende stappen uitvoeren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de pagina voor integratie van de toepassing **SAP Cloud Platform-identiteitsverificatie** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u in de door **IDP** geïnitieerde modus wilt configureren:

    ![Domein- en URL-informatie voor eenmalige aanmelding bij SAP Cloud Platform Identity-identiteitsverificatie](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `<IAS-tenant-id>.accounts.ondemand.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem voor deze waarden contact op met het [clientondersteuningsteam van SAP Cloud Platform-identiteitsverificatie](https://cloudplatform.sap.com/capabilities/security/trustcenter.html). Als u de id-waarde niet begrijpt, leest u de documentatie van SAP Cloud Platform-identiteitsverificatie over [Tenant SAML 2.0-configuratie](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Domein- en URL-informatie voor eenmalige aanmelding bij SAP Cloud Platform Identity-identiteitsverificatie](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Gebruik uw specifieke aanmeldings-URL voor zakelijke toepassingen. Neem contact op met het [clientondersteuningsteam van SAP Cloud Platform-identiteitsverificatie](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) als u twijfelt.

6. Voor de SAP Cloud Platform-identiteitsverificatie wordt verwacht dat de SAML-asserties een specifieke indeling hebben. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![image](common/edit-attribute.png)

7. Als uw SAP-toepassing een kenmerk zoals **firstName** verwacht, voegt u het kenmerk **firstName** aan het gedeelte **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** toe en configureert u het kenmerk van het SAML-token zoals wordt weergegeven in de bovenstaande afbeelding. Hierna voert u de volgende stappen uit:

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de kenmerknaam firstName.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Selecteer uit de lijst **Bronkenmerken** de kenmerkwaarde user.givenname.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

8. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om de **metagegevens-XML** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

9. In het gedeelte **SAP Cloud Platform-identiteitsverificatie instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>Eenmalige aanmelding voor SAP Cloud Platform-identiteitsverificatie configureren

1. Om een eenmalige aanmelding te configureren voor uw toepassing, gaat u naar de beheerconsole van SAP Cloud Platform-identiteitsverificatie. De URL heeft het volgende patroon: `https://<tenant-id>.accounts.ondemand.com/admin`. Lees de documentatie over SAP Cloud Platform-identiteitsverificatie in [Integration with Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html) (Integratie met Microsoft Azure AD).

2. Selecteer in de Azure-portal de knop **Opslaan**.

3. Ga alleen door met het volgende als u eenmalige aanmelding voor nog een SAP-toepassing wilt toevoegen en inschakelen. Herhaal de stappen uit het gedeelte **SAP Cloud Platform-identiteitsverificatie uit de galerie toevoegen**.

4. Selecteer in de Azure-portal op de pagina voor integratie van de toepassing **SAP Cloud Platform-identiteitsverificatie** de optie **Gekoppelde aanmelding**.

    ![Gekoppelde aanmelding configureren](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Sla de configuratie op.

> [!NOTE]
> De nieuwe toepassing maakt gebruik van de individuele aanmeldingsconfiguratie van de vorige SAP-toepassing. Zorg ervoor dat u de dezelfde zakelijke id-providers gebruikt in de beheerconsole voor SAP Cloud Platform-identiteitsverificatie.

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

In deze sectie zorgt u ervoor dat Britta Simon van eenmalige aanmelding met Azure gebruik kan maken door haar toegang te verlenen tot SAP Cloud Platform-identiteitsverificatie.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **SAP Cloud Platform-identiteitsverificatie**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SAP Cloud Platform-identiteitsverificatie** in de lijst met toepassingen.

    ![De koppeling SAP Cloud Platform-identiteitsverificatie in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Testgebruiker maken voor SAP Cloud Platform-identiteitsverificatie

U hoeft geen gebruiker te maken in SAP Cloud Platform-identiteitsverificatie. Gebruikers die zich in het gebruikersarchief van Azure AD bevinden, kunnen de functionaliteit voor eenmalige aanmelding gebruiken.

SAP Cloud Platform-identiteitsverificatie ondersteunt de optie Identiteitsfederatie. Met deze optie kan de toepassing controleren of gebruikers die worden geverifieerd door de zakelijke id-provider bestaan in het gebruikersarchief van de SAP Cloud Platform-identiteitsverificatie.

De optie Identiteitsfederatie is standaard uitgeschakeld. Als Identiteitsfederatie is ingeschakeld, hebben alleen de gebruikers die zijn geïmporteerd in de SAP Cloud Platform-identiteitsverificatie, toegang tot de toepassing.

Zie voor meer informatie over het in- of uitschakelen van Identiteitsfederatie met SAP Cloud Platform-identiteitsverificatie 'Enable Identity Federation with SAP Cloud Platform Identity Authentication' (Identiteitsfederatie inschakelen met SAP Cloud Platform-identiteitsverificatie) in [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html) (Identiteitsfederatie configureren met het gebruikersarchief van SAP Cloud Platform-identiteitsverificatie).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SAP Cloud Platform-identiteitsverificatie klikt in het toegangsvenster, wordt u automatisch aangemeld bij de instantie van SAP Cloud Platform-identiteitsverificatie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
