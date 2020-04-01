---
title: 'Zelfstudie: Azure Active Directory-integratie met Herkennen | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Herkennen.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943342"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Zelfstudie: Azure Active Directory-integratie met Herkennen

In deze zelfstudie leert u hoe u Herkennen integreert met Azure Active Directory (Azure AD).
Het integreren van Herkennen met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Herkennen.
* U uw gebruikers automatisch laten aanmelden om te herkennen (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Herkennen wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Eenmalige aanmeldingsabonnement herkennen

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Ondersteuning voor herkennen **SP** geïnitieerde SSO

## <a name="adding-recognize-from-the-gallery"></a>Herkennen toevoegen vanuit de galerie

Als u de integratie van Herkennen in Azure AD wilt configureren, moet u Herkennen vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Herkennen toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Herkennen** **uit** het resultaatpaneel herkennen in het zoekvak en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Herkennen in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Herkennen op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding om te werken, moet een koppelingsrelatie tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Herkennen worden vastgesteld.

Als u Azure AD-single sign-on met Herkennen wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eén aanmelding herkennen configureren](#configure-recognize-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker herkennen maken](#create-recognize-test-user)** - om een tegenhanger van Britta Simon in Herkennen te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren als u de enkele aanmelding van Azure AD met Herkennen wilt configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina Toepassingsintegratie **herkennen** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    >[!NOTE]
    >U krijgt het **metagegevensbestand van** de serviceprovider uit het gedeelte **Eén aanmelding configureren** configureren van de zelfstudie.

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het metagegevensbestand is geüpload, wordt de **id-waarde** automatisch ingevuld in de sectie BasisSAML-configuratie.

    ![Domein- en URL's-informatie met één aanmelding herkennen](common/sp-identifier.png)

     Typ in het tekstvak **Aantekenen op URL** een URL met het volgende patroon:`https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Als de **waarde van** de id niet automatisch wordt ingevuld, krijgt u de **id-waarde** door de URL met metagegevens van serviceprovider te openen in de sectie SSO-instellingen die later wordt uitgelegd in het gedeelte Eén aanmelding configureren van de zelfstudie. De waarde voor de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [het ondersteuningsteam van De Klant](mailto:support@recognizeapp.com) herkennen om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Herkennen instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-recognize-single-sign-on"></a>Herkennen eenmalig aanmelden configureren

1. Meld u in een ander browservenster aan bij uw tenant Herkennen als beheerder.

2. Klik in de rechterbovenhoek op **Menu**. Ga naar **Bedrijfsbeheerder**.
   
    ![Eenmalige aanmelding in de app configureren](./media/recognize-tutorial/tutorial_recognize_000.png)

3. Klik in het linkernavigatiedeelvenster op **Instellingen**.
   
    ![Eenmalige aanmelding in de app configureren](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Voer de volgende stappen uit in de sectie **SSO-instellingen.**
   
    ![Eenmalige aanmelding in de app configureren](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Selecteer ALS **SSO inschakelen**, selecteer **AAN**.

    b. Plak in het tekstvak **IDP-entiteit-id** de waarde van **azure AD-id** die u hebt gekopieerd uit azure-portal.
    
    c. Plak in het tekstvak **url-url van Sso** de waarde van **de aanmeldings-URL** die u hebt gekopieerd van azure-portal.
    
    d. Plak in het tekstvak **Url van Slo-doel** de waarde van **de URL van Afmelden** die u hebt gekopieerd van azure-portal. 
    
    e. Open het gedownloade **certificaatbestand (Base64)** in het notitieblok, kopieer de inhoud ervan in het klembord en plak het vervolgens in het tekstvak **Certificaat.**
    
    f. Klik op de knop **Instellingen opslaan.** 

5. Kopieer naast de sectie **SSO-instellingen** de URL onder **de url metagegevens van serviceprovider**.
   
    ![Eenmalige aanmelding in de app configureren](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Open de **URL-koppeling met metagegevens** onder een lege browser om het metagegevensdocument te downloaden. Kopieer vervolgens de entiteitswaarde EntityDescriptor(entityID) uit het bestand en plak deze in het tekstvak **Identifier** in **BasisSAML-configuratie** op Azure-portal.
    
    ![Eenmalige aanmelding in de app configureren](./media/recognize-tutorial/tutorial_recognize_004.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In **User name** het veld brittasimon@yourcompanydomain.extensiontype gebruikersnaam . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Herkennen.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Herkennen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Herkennen**in de lijst met toepassingen .

    ![De koppeling Herkennen in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-recognize-test-user"></a>Testgebruiker herkennen maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Herkennen, moeten ze worden ingericht in Herkennen. In het geval van Herkennen is inrichten een handmatige taak.

Deze app biedt geen ondersteuning voor SCIM-inrichting, maar heeft een alternatieve gebruikerssynchronisatie die gebruikers voorziet. 

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan op uw bedrijfssite Herkennen als beheerder.

2. Klik in de rechterbovenhoek op **Menu**. Ga naar **Bedrijfsbeheerder**.

3. Klik in het linkernavigatiedeelvenster op **Instellingen**.

4. Voer de volgende stappen uit in de sectie **Gebruikerssynchronisatie.**
   
    ![Nieuwe gebruiker](./media/recognize-tutorial/tutorial_recognize_005.png "Nieuwe gebruiker")
   
    a. Selecteer **Sync Enabled** **AAN**.
   
    b. Selecteer Microsoft / **Office 365**als **synchronisatieprovider kiezen.**
   
    c. Klik **op Gebruikerssynchronisatie uitvoeren**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Herkennen in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de optie Herkennen waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

