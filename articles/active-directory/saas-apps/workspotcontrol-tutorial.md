---
title: 'Zelfstudie: Azure Active Directory-integratie met Workspot Control | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding voor Azure Active Directory en Workspot controle.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086686"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Zelfstudie: Azure Active Directory-integratie met Workspot besturingselement

In deze zelfstudie leert u hoe u Workspot besturingselement integreren met Azure Active Directory (Azure AD). Wanneer u Workspot besturingselement met Azure AD integreert, kunt u het volgende doen:

* Azure AD om te bepalen wie toegang tot Workspot besturingselement heeft gebruiken.
* Kunnen gebruikers automatisch om te bepalen (SSO single sign-on []) Workspot aanmelden met hun Azure AD-accounts.
* Uw accounts in één centrale locatie beheren: de Azure-portal.

Zie voor meer informatie over de integratie met Azure AD SaaS [Single sign-on bij toepassingen in Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Workspot besturingselement, moet u de volgende zaken:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, krijgt u een [gratis account](https://azure.microsoft.com/free/).

* Een besturingselement Workspot single sign-on-ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

> [!Note]
> Workspot besturingselement ondersteunt SP geïnitieerde en IDP gestart door SSO.


## <a name="adding-workspot-control-from-the-gallery"></a>Workspot-besturingselement toe te voegen uit de galerie

Voor het configureren van integratie van Workspot besturingselement in Azure AD, moet u Workspot besturingselement uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u wilt toevoegen Workspot besturingselement uit de galerie, de volgende stappen uit:**

1. In het linkerdeelvenster van de [Azure-portal](https://portal.azure.com), selecteer **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfstoepassingen** en selecteer **alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

3. Selecteer **nieuwe toepassing** aan de bovenkant van het venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Workspot besturingselement**, selecteer **Workspot besturingselement** vanuit het deelvenster met resultaten en selecteer vervolgens **toevoegen**.

     ![Venster 'Uit de galerie toevoegen'](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie maakt u configureert en test Azure AD eenmalige aanmelding met Workspot besturingselement voor een testgebruiker, Britta Simon.
Voor eenmalige aanmelding om te werken, moet u een koppeling tussen een Azure AD-gebruiker en de gerelateerde gebruiker maken in Workspot besturingselement.

Als u wilt configureren en testen van Azure AD eenmalige aanmelding met Workspot besturingselement, moet u de volgende taken uitvoeren:

1. [Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers de functie kunnen gebruiken.
2. [Configureren van eenmalige aanmelding Workspot besturingselement](#configure-workspot-control-single-sign-on) de instellingen voor eenmalige aanmelding configureren aan de toepassing.
3. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) voor het testen van Azure AD eenmalige aanmelding voor Britta Simon.
4. [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding van Azure AD.
5. [Maak een testgebruiker Workspot besturingselement](#create-a-workspot-control-test-user) tot stand brengen van een equivalent van Britta Simon in Workspot-besturingselement dat gekoppeld aan de Azure AD-weergave van de gebruiker.
6. [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD eenmalige aanmelding met Workspot beheer, de volgende stappen uit:

1. Op de **Workspot besturingselement** toepassingspagina-integratie in de [Azure-portal](https://portal.azure.com/), selecteer **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In de **selecteert u een methode voor eenmalige aanmelding** venster, selecteer **SAML** modus voor eenmalige aanmelding inschakelen.

    ![Selecteer een eenmalige aanmelding methode select-venster](common/select-saml-option.png)

3. Op de **instellen van eenmalige aanmelding met SAML** weergeeft, schakelt de **bewerken** (potloodpictogram) voor toegang tot **SAML-basisconfiguratie**.

    ![Pictogram gemarkeerd 'Basic SAML-configuratie' bewerken](common/edit-urls.png)

4. In de **SAML-basisconfiguratie** sectie, als u wilt configureren van de toepassing in de modus voor IDP gestart door, als volgt te werk:

    ![Workspot besturingselement domein en URL's eenmalige aanmelding informatie](common/idp-intiated.png)

    1. In de **id** tekst vak, een URL opgeven in het volgende patroon:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. In de **antwoord-URL** tekst vak, een URL opgeven in het volgende patroon:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/assertion***

5. Als u configureren van de toepassing in de modus SP geïnitieerde wilt, selecteert u **extra URL's instellen**.

    ![Workspot besturingselement domein en URL's eenmalige aanmelding informatie](common/metadata-upload-additional-signon.png)

    In de **aanmeldings-URL** tekst vak, een URL opgeven in het volgende patroon:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > Dit zijn geen echte waarden. Deze waarden vervangen door de werkelijke-id, antwoord-URL en aanmeldings-URL. Neem contact op met de [Workspot Beheerclient ondersteuningsteam](mailto:support@workspot.com) om deze waarden te verkrijgen. Of u kunt ook verwijzen naar de patronen in de **SAML-basisconfiguratie** sectie van de Azure portal.

6. Op de **instellen van eenmalige aanmelding met SAML** pagina, in de **SAML-handtekeningcertificaat** sectie, selecteer **downloaden** downloaden **certificaat (Base64)** uit de beschikbare opties overeenkomstig uw vereisten. Sla het op uw computer.

    ![De downloadkoppeling certificaat (Base64)](common/certificatebase64.png)

7. In de **Workspot besturingselement instellen** sectie, kopieert u de juiste URL's overeenkomstig uw vereisten:

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    - **Aanmeldings-URL**

    - **Azure AD Identifier**

    - **Afmeldings-URL**

### <a name="configure-workspot-control-single-sign-on"></a>Configureren van eenmalige aanmelding Workspot besturingselement

1. In een ander browservenster aanmelden bij Workspot besturingselement als een beveiligingsbeheerder.

2. Selecteer in de werkbalk boven aan de pagina **Setup** en vervolgens **SAML**.

    ![Instelopties](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. In de **Security Assertion Markup Language configuratie** venster als volgt te werk:
 
    ![Security Assertion Markup Language configuratie venster](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. In de **entiteit-ID** vak, plak de **Azure Ad-id** die u hebt gekopieerd vanuit Azure portal.

    1. In de **URL van de Service aanmelden** vak, plak de **aanmeldings-URL** die u hebt gekopieerd vanuit Azure portal.

    1. In de **afmeldings-URL van Service** vak, plak de **afmeldings-URL van** die u hebt gekopieerd vanuit Azure portal.

    1. Selecteer **updatebestand** om te uploaden naar het X.509-certificaat van de base-64 gecodeerde certificaat dat u hebt gedownload vanuit Azure portal.

    1. Selecteer **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in Azure portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal, **Azure Active Directory**, **gebruikers**, en vervolgens **alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** aan de bovenkant van het venster.

    ![De 'nieuwe gebruiker"knop](common/new-user.png)

3. In de eigenschappen voor de gebruiker, de volgende stappen uit:

    ![Het venster van de eigenschappen van gebruiker](common/user-properties.png)

    1. In de **naam** veld **BrittaSimon**.
  
    1. In de **gebruikersnaam** veld **brittasimon@* yourcompanydomain.extension* **. Voer bijvoorbeeld  **BrittaSimon@contoso.<i> </i>com**.

    1. Selecteer de **Show wachtwoord** selectievakje. Noteer de waarde die wordt weergegeven in de **wachtwoord** vak.

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie kunt u Britta Simon toegang verlenen aan Workspot besturingselement haar te gebruiken Azure eenmalige aanmelding inschakelen.

1. Selecteer in de Azure portal, **bedrijfstoepassingen**, **alle toepassingen**, en vervolgens **Workspot besturingselement**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen, **Workspot besturingselement**.

    ![De koppeling Workspot besturingselement in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant, **gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer de **gebruiker toevoegen** knop. Selecteer vervolgens **gebruikers en groepen** in de **toewijzing toevoegen** venster.

    ![Het venster 'Toewijzing toevoegen'](common/add-assign-user.png)

5. In de **gebruikers en groepen** venster **Britta Simon** uit de **gebruikers** lijst. Klik vervolgens op **Selecteren**.

6. Als u een waarde voor de rol in het SAML-verklaring verwacht, selecteert u de juiste rol voor de gebruiker in de lijst in de **rol selecteren** venster. Klik vervolgens op **Selecteer** aan de onderkant.

7. In de **toevoegen toewijzing** venster **toewijzen**.

### <a name="create-a-workspot-control-test-user"></a>Maak een testgebruiker Workspot besturingselement

Om in te schakelen in Azure AD-gebruikers zich aanmelden bij Workspot besturingselement, moeten ze worden ingericht in Workspot besturingselement. Inrichten is een handmatige taak.

**Volg deze stappen voor het inrichten van een gebruikersaccount:**

1. Meld u aan om te bepalen Workspot als een beveiligingsbeheerder.

2. Selecteer in de werkbalk boven aan de pagina **gebruikers** en vervolgens **gebruiker toevoegen**.

    ![Opties voor 'Gebruikers'](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. In de **een nieuwe gebruiker toevoegen** venster als volgt te werk:

    ![Venster 'Een nieuwe gebruiker toevoegen'](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. In **voornaam** voert u de voornaam van een gebruiker, zoals **Julia**.

    1. In **achternaam** tekst voert u de achternaam van de gebruiker, zoals **Simon**.

    1. In **e** vak, voer het e-mailadres van de gebruiker, zoals  **Brittasimon@contoso.<i> </i>com**.

    1. Selecteer de juiste gebruikersrol van de **rol** vervolgkeuzelijst.

    1. Selecteer de juiste gebruikersgroep uit de **groep** vervolgkeuzelijst.

    1. Selecteer **Add User**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie testen we onze configuratie Azure AD eenmalige aanmelding via *Toegangsvenster*.

Wanneer u klikt op de **Workspot besturingselement** tegel in het paneel voor Apptoegang, u moet worden automatisch aangemeld bij het Workspot besturingselement waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) (Inleiding tot het toegangsvenster) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
