---
title: 'Zelfstudie: Azure Active Directory-integratie met Workspot-besturingselement | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding voor Azure Active Directory en Workspot-besturingselement.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086686"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Zelfstudie: Azure Active Directory-integratie met Workspot-besturingselement

In deze zelfstudie leert u hoe u Workspot Control integreert met Azure Active Directory (Azure AD). Wanneer u Workspot Control integreert met Azure AD, u het als:

* Gebruik Azure AD om te bepalen wie toegang heeft tot Workspot-besturingselement.
* Gebruikers in staat stellen zich automatisch aan te melden bij Workspot Control (single sign-on [SSO]) met behulp van hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Enkele aanmelding](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor toepassingen in Azure AD voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Workspot Control wilt configureren, hebt u de volgende dingen nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/)krijgen.

* Een abonnement met één aanmeldingsbord.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

> [!Note]
> Workspot Control ondersteunt door SP geïnitieerde en IDP-geïnitieerde SSO.


## <a name="adding-workspot-control-from-the-gallery"></a>Werkspotbesturingselement toevoegen vanuit de galerie

Als u de integratie van Workspot-besturingselement in Azure AD wilt configureren, moet u Workspot-besturingselement vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Workspot-besturingselement en werkpuntbeheer toe te voegen vanuit de galerie:**

1. Selecteer Azure Active Directory in het linkerdeelvenster van de [Azure-portal](https://portal.azure.com). **Azure Active Directory**

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen** en selecteer **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

3. Selecteer **Nieuwe toepassing** boven aan het venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Workspot-besturingselement**in, selecteer **Workspot-besturingselement** in het deelvenster Resultaten en selecteer **Vervolgens Toevoegen**.

     ![Venster 'Toevoegen vanuit de galerie'](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Workspot Control voor een testgebruiker, Britta Simon.
Voor eenmalige aanmelding aan het werk moet u een koppeling maken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Workspot Control.

Als u Azure AD-eenmaligaanmelding wilt configureren en testen met Workspot-besturingselement, moet u de volgende taken uitvoeren:

1. [Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers de functie kunnen gebruiken.
2. [Configureer Workspot Control single sign-on](#configure-workspot-control-single-sign-on) om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om azure AD-enkele aanmelding voor Britta Simon te testen.
4. [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding van Azure AD.
5. [Maak een Workspot Control-testgebruiker](#create-a-workspot-control-test-user) om een tegenhanger van Britta Simon in Workspot Control te vestigen die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen te configureren voor het configureren van Azure AD-eenmaligaanmelding met Workspot Control:

1. Selecteer op de pagina **Integratie van de Workspot Control-toepassing** in de [Azure-portal](https://portal.azure.com/)de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer in het venster **Een enkele aanmeldingsmethode** de **SAML-modus** om eenmalige aanmelding in te schakelen.

    ![Eén aanmeldingsvenster selecteren selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **Eén aanmelding instellen met SAML** het pictogram **Bewerken** (potlood) om toegang te krijgen tot **basisSAML-configuratie**.

    ![Pictogram Bewerken gemarkeerd in 'BasisSAML-configuratie'](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de idp-modus wilt configureren:

    ![Workspot Control-domein en URL's met eenmalige aanmeldingsgegevens](common/idp-intiated.png)

    1. Voer in het tekstvak **van de id** een URL in het volgende patroon in:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. Voer in het tekstvak **antwoord-URL** een URL in het volgende patroon in:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/saml/assertion***

5. Als u de toepassing wilt configureren in de door SP geïnitieerde modus, selecteert u **Extra URL's instellen**.

    ![Workspot Control-domein en URL's met eenmalige aanmeldingsgegevens](common/metadata-upload-additional-signon.png)

    Voer in het tekstvak **URL aanmelding** een URL in het volgende patroon in:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > Dit zijn geen echte waarden. Vervang deze waarden door de werkelijke id, de URL van het antwoord en de URL van aanmelding. Neem contact op met het [ondersteuningsteam van Workspot Control Client](mailto:support@workspot.com) om deze waarden te krijgen. U ook verwijzen naar de patronen in de sectie **BasisSAML-configuratie** van de Azure-portal.

6. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Downloaden** om **certificaat (Base64)** te downloaden van de beschikbare opties volgens uw vereisten. Sla het op uw computer op.

    ![De downloadkoppeling Certificate (Base64)](common/certificatebase64.png)

7. Kopieer in de sectie **Workspot control** instellen de juiste URL's volgens uw vereisten:

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    - **Aanmeldings-URL**

    - **Azure AD-id**

    - **Afmeldings-URL**

### <a name="configure-workspot-control-single-sign-on"></a>Enkele aanmelding voor Werkspotbesturingselement configureren

1. Meld u in een ander browservenster aan bij Workspot Control als beveiligingsbeheerder.

2. Selecteer **instellen** en vervolgens **SAML**op de werkbalk boven aan de pagina.

    ![Instelopties](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. Voer in het venster **Taalconfiguratie** van beveiligingsbeweringen de volgende stappen uit:
 
    ![Venster Taalconfiguratie van beveiligingsbewerings](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. Plak in het vak **Entiteits-id** de **Azure-advertentie-id** die u hebt gekopieerd vanuit de Azure-portal.

    1. Plak in het **vak URL van Signon Service** de **aanmeldings-URL** die u hebt gekopieerd vanuit de Azure-portal.

    1. Plak in het vak URL van de **uitlogservice** de **url van de afmelding** die u hebt gekopieerd uit de Azure-portal.

    1. Selecteer **Bestand bijwerken** om het basis-64-gecodeerde certificaat dat u hebt gedownload van de Azure-portal naar het X.509-certificaat te uploaden naar het X.509-certificaat.

    1. Selecteer **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal.

1. Selecteer Azure Active Directory , **Gebruikers**en vervolgens Alle **gebruikers**in het linkerdeelvenster van de **Azure-portal**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het venster.

    ![De knop 'Nieuwe gebruiker'](common/new-user.png)

3. Voer in de eigenschappen voor de gebruiker de volgende stappen uit:

    ![Het venster Gebruikerseigenschappen](common/user-properties.png)

    1. Voer In het veld **Naam** **BrittaSimon**in .
  
    1. Voer in het veld **Gebruikersnaam** **brittasimon@* uwbedrijfsdomein.extensie**** in. Voer bijvoorbeeld ** BrittaSimon@contoso.<i> </i> com**.

    1. Schakel het selectievakje **Wachtwoord weergeven** in. Noteer vervolgens de waarde die wordt weergegeven in het vak **Wachtwoord.**

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon toegang tot Workspot Control om haar in staat te stellen Azure single sign-on te gebruiken.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, **Alle toepassingen**en vervolgens **Workspot-besturingselement**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Workspot-besturingselement**in de lijst met toepassingen .

    ![De koppeling Workspot Control in de lijst Toepassingen](common/all-applications.png)

3. Selecteer **gebruikers en groepen**in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer de **knop Gebruiker toevoegen.** Selecteer **vervolgens Gebruikers en groepen** in het venster **Toewijzing toevoegen.**

    ![Het venster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **Britta Simon** in het venster **Gebruikers en groepen** in de lijst **Gebruikers.** Klik vervolgens op **Selecteren**.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u de juiste rol voor de gebruiker in de lijst in het venster **Rol selecteren.** Klik vervolgens onderaan op **Selecteren.**

7. Selecteer **Toewijzing toewijzen** in het venster Toewijzing **toevoegen**.

### <a name="create-a-workspot-control-test-user"></a>Een testgebruiker van Workspot Control maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij Workspot Control, moeten ze zijn ingericht in Workspot Control. Inrichten is een handmatige taak.

**Voer de volgende stappen uit om een gebruikersaccount in te richten:**

1. Meld u aan bij Workspot Control als beveiligingsbeheerder.

2. Selecteer op de werkbalk boven aan de pagina **Gebruikers** en **voeg vervolgens Gebruiker toe.**

    ![Opties voor gebruikers](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Voer **in** het venster Een nieuwe gebruiker toevoegen de volgende stappen uit:

    ![Venster 'Een nieuwe gebruiker toevoegen'](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. Voer in het vak **Voornaam** de voornaam van een gebruiker in, zoals **Britta**.

    1. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals **Simon**.

    1. Voer in het vak **E-mail** het e-mailadres van de gebruiker in, zoals ** Brittasimon@contoso.<i> </i> com**.

    1. Selecteer de juiste gebruikersrol in de vervolgkeuzelijst **Rol.**

    1. Selecteer de juiste gebruikersgroep in de vervolgkeuzelijst **Groep.**

    1. Selecteer **Gebruiker toevoegen**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie testen we onze Azure AD-configuratie voor eenmalige aanmelding via *access panel*.

Wanneer u op de tegel **Workspotbesturingselement** in het Access-deelvenster klikt, moet u automatisch worden aangemeld bij het Werkspotbesturingselement waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) (Inleiding tot het toegangsvenster) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
