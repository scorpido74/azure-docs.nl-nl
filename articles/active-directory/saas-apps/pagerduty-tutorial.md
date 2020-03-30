---
title: 'Zelfstudie: Azure Active Directory-integratie met PagerDuty | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: b7a522aaf35303bbd87e7aafe65b1302f1b98bc0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095335"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Zelfstudie: Azure Active Directory-integratie met PagerDuty

In deze zelfstudie leert u hoe u PagerDuty integreren met Azure Active Directory (Azure AD).
De integratie van PagerDuty met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot PagerDuty.
* U uw gebruikers automatisch laten aanmelden bij PagerDuty (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met PagerDuty wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement met eenmalige aanmelding van PagerDuty

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* PagerDuty ondersteunt **door SP** geïnitieerde SSO

## <a name="adding-pagerduty-from-the-gallery"></a>PagerDuty toevoegen vanuit de galerie

Als u de integratie van PagerDuty in Azure AD wilt configureren, moet u PagerDuty vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u PagerDuty vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **PagerDuty**in het zoekvak , selecteer **PagerDuty** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![PagerDuty in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met PagerDuty op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in PagerDuty.

Als u Azure AD-single sign-on met PagerDuty wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer PagerDuty Single Sign-On](#configure-pagerduty-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[PagerDuty-testgebruiker maken](#create-pagerduty-test-user)** - om een tegenhanger van Britta Simon in PagerDuty te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met PagerDuty te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **PagerDuty-toepassingsintegratie** de optie **Eén aanmelding .**

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over pagerduty-domeinen en URL's met eenmalige aanmelding](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenant-name>.pagerduty.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [het ondersteuningsteam van PagerDuty Client](https://www.pagerduty.com/support/) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **PagerDuty instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-pagerduty-single-sign-on"></a>PagerDuty-aanmelding configureren

1. Log in een ander browservenster in op uw Pagerduty-bedrijfssite als beheerder.

2. Klik in het menu bovenaan op **Accountinstellingen**.

    ![Accountinstellingen](./media/pagerduty-tutorial/ic778535.png "Accountinstellingen")

3. Klik **op Eén aanmelding**.

    ![Eenmalige aanmelding](./media/pagerduty-tutorial/ic778536.png "Eenmalige aanmelding")

4. Voer op de pagina **SSO inschakelen** de volgende stappen uit:

    ![Eén aanmelding inschakelen](./media/pagerduty-tutorial/ic778537.png "Eén aanmelding inschakelen")

    a. Open uw basis-64-gecodeerd certificaat dat is gedownload van de Azure-portal in kladblok, kopieer de inhoud ervan in uw klembord en plak het vervolgens op het tekstvak **x.509-certificaat**
  
    b. Plak in het tekstvak **Aanmeldings-URL** de **aanmeldings-URL** die u in de Azure-portal hebt gekopieerd.
  
    c. Plak in het tekstvak **Afmeldings-URL** de **afmeldings-URL** die u in de Azure-portal hebt gekopieerd.

    d. Selecteer **Gebruikersnaam/wachtwoord aanmelding toestaan**.

    e. Schakel selectievakje Schakel het selectievakje **SCHAKEL NAUWKEURIGE verificatiecontextvergelijking** vereisen in.

    f. Klik **op Wijzigingen opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension**.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot PagerDuty.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **PagerDuty**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **PagerDuty**in de lijst met toepassingen .

    ![De koppeling PagerDuty in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-pagerduty-test-user"></a>PagerDuty-testgebruiker maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij PagerDuty, moeten ze zijn ingericht in PagerDuty.  
In het geval van PagerDuty is inrichten een handmatige taak.

>[!NOTE]
>U alle andere hulpprogramma's voor het maken van gebruikersaccounts voor gebruikersaccounts van Pagerduty gebruiken die door Pagerduty worden verstrekt om Azure Active Directory-gebruikersaccounts in te richten.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw **Pagerduty-tenant.**

2. Klik in het menu bovenaan op **Gebruikers**.

3. Klik **op Gebruikers toevoegen**.
   
    ![Gebruikers toevoegen](./media/pagerduty-tutorial/ic778539.png "Gebruikers toevoegen")

4.  Voer **in het** dialoogvenster Uw team uitnodigen de volgende stappen uit:
   
    ![Nodig je team uit](./media/pagerduty-tutorial/ic778540.png "Nodig je team uit")

    a. Typ de **voor- en achternaam** van de gebruiker zoals **Britta Simon**. 
   
    b. Voer **e-mailadres** van de gebruiker als **\@brittasimon contoso.com**.
   
    c. Klik **op Toevoegen**en klik vervolgens op **Uitnodigingen verzenden**.
   
    >[!NOTE]
    >Alle toegevoegde gebruikers ontvangen een uitnodiging om een PagerDuty-account aan te maken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel PagerDuty in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de PagerDuty waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

