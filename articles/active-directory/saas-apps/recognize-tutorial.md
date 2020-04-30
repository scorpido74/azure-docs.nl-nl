---
title: 'Zelf studie: integratie Azure Active Directory met recognize | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en het herkennen van.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: c0a513ad8fe772839813615d065616f2c911a031
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68943342"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Zelf studie: integratie Azure Active Directory met recognize

In deze zelf studie leert u hoe u kunt integreren met Azure Active Directory (Azure AD).
Integratie herkennen met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft om te herkennen.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld om te herkennen (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met herkennen wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding herkennen

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Recognize ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-recognize-from-the-gallery"></a>Herkenning toevoegen vanuit de galerie

Als u de integratie van recognize in azure AD wilt configureren, moet u herkennen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

**Als u herkenning wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak de tekst **Recognize**, selecteer **erkennen** van resultaten paneel en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![Herkennen in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met herkennen op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in recognize worden ingesteld.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met recognize, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer eenmalige aanmelding herkennen](#configure-recognize-single-sign-on)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak recognize test User](#create-recognize-test-user)** -als u een equivalent van Julia Simon in recognize wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met recognize:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie **herkennen** de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    >[!NOTE]
    >U krijgt het **META gegevensbestand van de service provider** via de sectie **Configure single sign-on** van de zelf studie.

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het meta gegevensbestand is geüpload, wordt de **id** -waarde automatisch ingevuld in de basis configuratie sectie voor SAML.

    ![Informatie over het domein en Url's van eenmalige aanmelding herkennen](common/sp-identifier.png)

     Typ in het tekstvak **URL voor aanmelden** een URL met behulp van het volgende patroon:`https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Als de **id** -waarde niet automatisch wordt ingevuld, krijgt u de id-waarde door de meta gegevens-URL van de service provider te openen in de sectie SSO-instellingen die later wordt beschreven in de sectie **Configure eenmalige aanmelding herkennen** van de zelf studie. De waarde voor de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team](mailto:support@recognizeapp.com) van de client om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer op de sectie **instellen herkennen** de juiste URL ('s) conform uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-recognize-single-sign-on"></a>Configureren van eenmalige aanmelding herkennen

1. Meld u in een ander webbrowser venster als beheerder aan bij uw herkende Tenant.

2. Klik in de rechter bovenhoek op **menu**. Ga naar **bedrijfs beheerder**.
   
    ![Eenmalige aanmelding in de app configureren](./media/recognize-tutorial/tutorial_recognize_000.png)

3. Klik in het linkernavigatiedeelvenster op **Instellingen**.
   
    ![Eenmalige aanmelding in de app configureren](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Voer de volgende stappen uit in de sectie **SSO-instellingen** .
   
    ![Eenmalige aanmelding in de app configureren](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Selecteer aan om SSO **in**te **scha kelen**.

    b. Plak in het tekstvak **IDP entiteit-id** de waarde van de **Azure ad-id** die u van Azure Portal hebt gekopieerd.
    
    c. Plak in het tekstvak **URL voor SSO-doel** de waarde van de AANMELDINGS- **URL** die u hebt gekopieerd uit Azure Portal.
    
    d. Plak in het tekstvak **URL van Slo-doel** de waarde van de **afmeldings-URL** die u van Azure Portal hebt gekopieerd. 
    
    e. Open het gedownloade **certificaat bestand (base64)** in Klad blok, kopieer de inhoud ervan naar het klem bord en plak het in het tekstvak **certificaat** .
    
    f. Klik op de knop **instellingen opslaan** . 

5. Naast de sectie **SSO-instellingen** kopieert u de URL onder de meta gegevens-URL van de **service provider**.
   
    ![Eenmalige aanmelding in de app configureren](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Open de **meta gegevens-URL-koppeling** onder een lege browser om het meta gegevens document te downloaden. Kopieer vervolgens de waarde van EntityDescriptor (entityID) uit het bestand en plak deze in het tekstvak **id** in de **basis-SAML-configuratie** op Azure Portal.
    
    ![Eenmalige aanmelding in de app configureren](./media/recognize-tutorial/tutorial_recognize_004.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ brittasimon@yourcompanydomain.extensionin het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de herkenning.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **herkennen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Recognize**.

    ![De koppeling herkennen in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-recognize-test-user"></a>Test gebruiker maken herkennen

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij recognize, moeten ze worden ingericht in recognize. In het geval van herkennen is inrichting een hand matige taak.

Deze app biedt geen ondersteuning voor SCIM-inrichting, maar heeft een alternatieve gebruikers synchronisatie waarmee gebruikers worden ingericht. 

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u als beheerder aan bij uw website voor het herkennen van uw bedrijf.

2. Klik in de rechter bovenhoek op **menu**. Ga naar **bedrijfs beheerder**.

3. Klik in het linkernavigatiedeelvenster op **Instellingen**.

4. Voer de volgende stappen uit in de sectie **gebruikers synchronisatie** .
   
    ![Nieuwe gebruiker](./media/recognize-tutorial/tutorial_recognize_005.png "Nieuwe gebruiker")
   
    a. Selecteer **aan**als **synchronisatie is ingeschakeld**.
   
    b. Selecteer bij **provider synchronisatie**de optie **micro soft/Office 365**.
   
    c. Klik op **gebruikers synchronisatie uitvoeren**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel herkennen in het toegangs venster klikt, wordt u automatisch aangemeld bij de herkenning waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

