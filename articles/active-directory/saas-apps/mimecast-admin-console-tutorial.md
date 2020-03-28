---
title: 'Zelfstudie: Azure Active Directory-integratie met Mimecast-beheerconsole | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en de Mimecast-beheerconsole.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9edadd6462052f82f92c05c1678f845ece856cfb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160663"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Zelfstudie: Azure Active Directory-integratie met Mimecast-beheerconsole

In deze zelfstudie leert u hoe u de Mimecast-beheerconsole kunt integreren met Azure Active Directory (Azure AD).
De integratie van de Mimecast-beheerconsole met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot de Mimecast-beheerconsole.
* U kunt uw gebruikers zich automatisch laten aanmelden bij de Mimecast-beheerconsole (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met de Mimecast-beheerconsole hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op de Mimecast-beheerconsole waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* De Mimecast-beheerconsole biedt ondersteuning van door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>De Mimecast-beheerconsole toevoegen vanuit de galerie

Voor het configureren van de integratie van de Mimecast-beheerconsole in Azure AD moet u de Mimecast-beheerconsole uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om de Mimecast-beheerconsole toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Mimecast-beheerconsole** in het zoekvak, selecteer **Mimecast-beheerconsole** in het resultaatvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![De Mimecast-beheerconsole in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met de Mimecast-beheerconsole configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in de Mimecast-beheerconsole tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met de Mimecast-beheerconsole wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor de Mimecast-beheerconsole configureren](#configure-mimecast-admin-console-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker maken in de Mimecast-beheerconsole](#create-mimecast-admin-console-test-user)**: voor een equivalent van Britta Simon in de Mimecast-beheerconsole dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om Azure AD-eenmalige aanmelding bij de Mimecast-beheerconsole te configureren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Mimecast-beheerconsole**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij de Mimecast-beheerconsole](common/sp-signonurl.png)

    Typ een URL in het tekstvak **Aanmeldings-URL**:
    
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > De aanmeldings-URL is regiospecifiek.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In het gedeelte **Set up Mimecast Admin Console** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-mimecast-admin-console-single-sign-on"></a>Eenmalige aanmelding voor de Mimecast-beheerconsole configureren

1. Meld u in een ander webbrowservenster als beheerder aan bij de Mimecast-beheerconsole.

2. Ga naar **Services \> Application**.

    ![Services](./media/mimecast-admin-console-tutorial/ic794998.png "Services")

3. Klik op **Authentication Profiles**.

    ![Verificatieprofielen](./media/mimecast-admin-console-tutorial/ic794999.png "Verificatieprofielen")
    
4. Klik op **New Authentication Profile**.

    ![Nieuwe verificatieprofielen](./media/mimecast-admin-console-tutorial/ic795000.png "Nieuwe verificatieprofielen")

5. Voer in de sectie **Authentication Profile** de volgende stappen uit:

    ![Verificatieprofiel](./media/mimecast-admin-console-tutorial/ic795015.png "Verificatieprofiel")
    
    a. Typ in het tekstvak **Description** een naam voor de configuratie.
    
    b. Selecteer **Enforce SAML Authentication for Mimecast Admin Console**.
    
    c. Selecteer **Azure Active Directory** als **Provider**.
    
    d. Plak de **Azure AD-id**, die u in de Azure-portal hebt gekopieerd, in het tekstvak **Issuer URL**.
    
    e. Plak de **aanmeldings-URL**, die u in de Azure-portal hebt gekopieerd, in het tekstvak **Login URL**.

    f. Plak de **aanmeldings-URL**, die u in de Azure-portal hebt gekopieerd, in het tekstvak **Logout URL**.
    
    >[!NOTE]
    >Voor de Mimecast-beheerconsole zijn de waarden van de aanmeldings- en afmeldings-URL dezelfde.
    
    g. Open uw basis-64-certificaat dat is gedownload van de Azure-portal in kladblok, verwijder de eerste regel ("*--*") en de laatste regel ("*--*"), kopieer de resterende inhoud ervan in uw klembord en plak het vervolgens in het textbox **(Identity Provider Certificate) (Metadata).**
    
    h. Selecteer **Allow Single Sign On**.
    
    i. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het **veld Type Gebruikersnaam** **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot de Mimecast-beheerconsole.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen**, selecteer **Alle toepassingen**en selecteer vervolgens **Mimecast-beheerconsole**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ **Mimecast-beheerconsole** in de lijst met toepassingen en selecteer het.

    ![De koppeling Mimecast-beheerconsole in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-mimecast-admin-console-test-user"></a>Testgebruiker voor de Mimecast-beheerconsole maken

Om het gebruikers van Azure AD mogelijk te maken zich aan te melden bij de Mimecast-beheerconsole, moeten ze worden ingericht in de Mimecast-beheerconsole. In het geval van de Mimecast-beheerconsole is het inrichten een handmatige taak.

* U moet een domein registreren voordat u gebruikers kunt maken.

**Voer de volgende stappen uit om de inrichting van gebruikers te configureren:**

1. Meld u aan bij uw **Mimecast-beheerconsole** als beheerder.

2. Ga naar **Directories \> Internal**.
   
    ![Mappen](./media/mimecast-admin-console-tutorial/ic795003.png "Mappen")

3. Klik op **Register New Domain**.
   
    ![Nieuw domein registreren](./media/mimecast-admin-console-tutorial/ic795004.png "Nieuw domein registreren")

4. Nadat het nieuwe domein is gemaakt, klikt u op **New Address**.
   
    ![Nieuw adres](./media/mimecast-admin-console-tutorial/ic795005.png "Nieuw adres")

5. Voer in het dialoogvenster met nieuwe adressen de volgende stappen uit:
   
    ![Opslaan](./media/mimecast-admin-console-tutorial/ic795006.png "Opslaan")
   
    a. Typ de kenmerken **E-mailadres**, **Globale naam**, **Wachtwoord** en **Wachtwoord bevestigen** van een geldig Azure Active Directory-account dat u wilt inrichten in de desbetreffende tekstvakken.

    b. Klik op **Opslaan**.

>[!NOTE]
>U kunt ook alle andere hulpprogramma's voor het maken van gebruikersaccounts of API's van de Mimecast-beheerconsole gebruiken om Azure AD-gebruikersaccounts in te richten. 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Mimecast-beheerconsole in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van de Mimecast-beheerconsole waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

