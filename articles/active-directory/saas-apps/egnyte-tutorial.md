---
title: 'Zelf studie: integratie Azure Active Directory met Egnyte | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Egnyte configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 2/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7201b7d3f9f7e8333b18a087f58b6211101b1304
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233558"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Zelf studie: integratie Azure Active Directory met Egnyte

In deze zelfstudie leert u hoe u Egnyte kunt integreren met Azure AD (Active Directory).
De integratie van Egnyte met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Egnyte.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Egnyte (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie te configureren met Egnyte:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Egnyte waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Egnyte biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-egnyte-from-the-gallery"></a>Egnyte toevoegen vanuit de galerie

Om de integratie van Egnyte te configureren in Azure AD moet u Egnyte vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Egnyte toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Egnyte** in het zoekvak, selecteer **Egnyte** in het deelvenster met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Egnyte in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Egnyte op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Egnyte tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Egnyte, moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Egnyte-eenmalige aanmelding configureren](#configure-egnyte-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Egnyte-testgebruiker maken](#create-egnyte-test-user)** : als u een tegenhanger van Britta Simon in Egnyte wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met Egnyte:

1. Selecteer in de [Azure-portal](https://portal.azure.com/), op de integratiepagina voor de toepassing **Egnyte**, de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens voor domein en URL's voor eenmalige aanmelding van Egnyte](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.egnyte.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

5. Kopieer in het gedeelte **Egnyte instellen** de juiste URL('s) op basis van wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-egnyte-single-sign-on"></a>Egnyte-eenmalige aanmelding configureren

1. Meld u in een ander browservenster als een beheerder aan bij de bedrijfssite van Egnyte.

2. Klik op **Instellingen**.
   
    ![Instellingen](./media/egnyte-tutorial/ic787819.png "Instellingen")

3. Klik in het menu op **Instellingen**.

    ![Instellingen](./media/egnyte-tutorial/ic787820.png "Instellingen")

4. Klik op het tabblad **Configuratie** en klik vervolgens op **Beveiliging**.

    ![Beveiliging](./media/egnyte-tutorial/ic787821.png "Beveiliging")

5. Voer in de sectie **Verificatie voor eenmalige aanmelding** de volgende stappen uit:

    ![Verificatie voor eenmalige aanmelding](./media/egnyte-tutorial/ic787822.png "Verificatie voor eenmalige aanmelding")   
    
    a. Selecteer **SAML 2.0** als **Verificatie voor eenmalige aanmelding**.
   
    b. Selecteer **AzureAD** als **Id-provider**.
   
    c. Plak de **Aanmeldings-URL** die u in de Azure-portal hebt gekopieerd, in het tekstvak **Aanmeldings-URL voor id-provider**.
   
    d. Plak de **Azure AD-id** die u hebt gekopieerd in de Azure-portal, in het tekstvak **Entiteits-id van id-provider**.
      
    e. Open in Kladblok het met Base64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **Id-providercertificaat**.
   
    f. Selecteer **E-mailadres** als **Standaardgebruikerstoewijzing**.
   
    g. Selecteer **Uitgeschakeld** bij **Waarde voor domeinspecifieke verlener gebruiken**.
   
    h. Klik op **Opslaan**.

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

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte geeft u Britta Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Egnyte.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Egnyte**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Egnyte** in de lijst met toepassingen.

    ![De koppeling naar Egnyte in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-egnyte-test-user"></a>Egnyte-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Egnyte, moeten ze worden ingericht in Egnyte. In het geval van Egnyte is het inrichten een handmatige taak.

**Voer de volgende stappen uit om een gebruikersaccount in te richten:**

1. Meld u als een beheerder aan bij de bedrijfssite van **Egnyte**.

2. Ga naar **Instellingen \> Gebruikers en groepen**.

3. Klik op **Nieuwe gebruiker toevoegen** en selecteer vervolgens het type gebruiker dat u wilt toevoegen.
   
    ![Gebruikers](./media/egnyte-tutorial/ic787824.png "Gebruikers")

4. Voer in de sectie **Nieuwe hoofdgebruiker** de volgende stappen uit:
    
    ![Nieuwe standaard gebruiker](./media/egnyte-tutorial/ic787825.png "Nieuwe standaard gebruiker")   

    a. Voer in het tekstvak **e-mail** het e-mail adres van de gebruiker in, zoals **Brittasimon\@contoso.com**.

    b. Voer in het tekstvak **Gebruikersnaam** de gebruikersnaam van een gebruiker zoals **Brittasimon**.

    c. Selecteer als **Verificatietype** de optie **Eenmalige aanmelding**.
   
    d. Klik op **Opslaan**.
    
    >[!NOTE]
    >De gebruiker van het Azure Active Directory-account ontvangt een e-mailmelding.
    >

>[!NOTE]
>U kunt alle andere hulpprogram ma's voor het maken van Egnyte-gebruikers accounts of Api's die worden geleverd door Egnyte, gebruiken om Azure AD-gebruikers accounts in te richten.
>

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Egnyte klikt, wordt u automatisch aangemeld bij de instantie van Egnyte waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

