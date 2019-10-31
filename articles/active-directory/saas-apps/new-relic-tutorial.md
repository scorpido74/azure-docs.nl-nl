---
title: 'Zelf studie: integratie Azure Active Directory met nieuwe Relic | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en nieuwe Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: fd75cc392bb30d15b665190eef8fbea39d692ee3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160336"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Zelf studie: integratie Azure Active Directory met nieuwe Relic

In deze zelf studie leert u hoe u nieuwe Relic integreert met Azure Active Directory (Azure AD).
Het integreren van nieuwe Relic met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot nieuwe Relic.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij nieuwe Relic (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met nieuwe Relic wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Nieuw abonnement voor eenmalige aanmelding voor Relic

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Nieuwe Relic ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-new-relic-from-the-gallery"></a>Nieuwe Relic toevoegen uit de galerie

Als u de integratie van nieuwe Relic wilt configureren in azure AD, moet u nieuwe Relic toevoegen uit de galerie aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om nieuwe Relic toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **nieuwe Relic**, selecteer **Nieuw Relic** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![Nieuwe Relic in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met nieuwe Relic op basis van een test gebruiker met de naam **Julia Simon**.
Als u eenmalige aanmelding wilt gebruiken, moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in New Relic tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met nieuwe Relic, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer een nieuwe Relic](#configure-new-relic-single-sign-on)** -eenmalige aanmelding voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een nieuwe Relic-test gebruiker](#create-new-relic-test-user)** -om een equivalent van Julia Simon in nieuwe Relic te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met een nieuwe Relic:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **nieuwe Relic** -toepassings integratie de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over nieuw Relic-domein en Url's voor eenmalige aanmelding](common/sp-identifier.png)

    a. Typ in het tekstvak **URL voor aanmelden** een URL met behulp van het volgende patroon: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login`-zorg ervoor dat u uw eigen nieuwe Relic-account-id vervangt.

    b. Typ een URL in het vak **Id (Entiteits-id)** : `rpm.newrelic.com`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer op de sectie **nieuwe Relic instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-new-relic-single-sign-on"></a>Nieuwe eenmalige aanmelding voor Relic configureren

1. Meld u in een ander browser venster aan bij uw **nieuwe Relic** -bedrijfs site als beheerder.

2. Klik in het menu aan de bovenkant op **account instellingen**.
   
    ![Account instellingen](./media/new-relic-tutorial/ic797036.png "Account instellingen")

3. Klik op het tabblad **beveiliging en verificatie** en klik vervolgens op het tabblad **eenmalige aanmelding** .
   
    ![Eenmalige aanmelding](./media/new-relic-tutorial/ic797037.png "Eenmalige aanmelding")

4. Voer de volgende stappen uit op de pagina SAML-dialoog vensters:
   
    ![SSO](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Klik op **bestand kiezen** om uw gedownloade Azure Active Directory certificaat te uploaden.

    b. Plak in het tekstvak **URL voor externe aanmelding** de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd.
   
    c. Plak in het tekstvak **Afmeldings-URL afmelden** de waarde van de **afmeldings-URL**, die u van Azure Portal hebt gekopieerd.

    d. Klik op **Mijn wijzigingen opslaan**.

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

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot nieuwe Relic.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **nieuwe Relic**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **New Relic**.

    ![De nieuwe Relic-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-new-relic-test-user"></a>Nieuwe Relic-test gebruiker maken

Om Azure Active Directory gebruikers in staat te stellen zich aan te melden bij een nieuw Relic, moeten ze worden ingericht in nieuwe Relic. In het geval van nieuwe Relic is inrichting een hand matige taak.

**Voer de volgende stappen uit om een gebruikers account in te richten op een nieuwe Relic:**

1. Meld u als beheerder aan bij de **nieuwe Relic** -bedrijfs site.

2. Klik in het menu aan de bovenkant op **account instellingen**.
   
    ![Account instellingen](./media/new-relic-tutorial/ic797040.png "Account instellingen")

3. Klik in het deel venster **account** aan de linkerkant op **samen vatting**en klik vervolgens op **gebruiker toevoegen**.
   
    ![Account instellingen](./media/new-relic-tutorial/ic797041.png "Account instellingen")

4. Voer de volgende stappen uit in het dialoog venster **actieve gebruikers** :
   
    ![Actieve gebruikers](./media/new-relic-tutorial/ic797042.png "Actieve gebruikers")
   
    a. Typ in het tekstvak **e-mail** het e-mail adres van een geldige Azure Active Directory gebruiker die u wilt inrichten.

    b. Als **rol** **gebruiker**selecteren.

    c. Klik op **deze gebruiker toevoegen**.

>[!NOTE]
>U kunt alle andere nieuwe hulpprogram ma's voor het maken van Relic-gebruikers accounts of Api's die worden geleverd door nieuwe Relic gebruiken om AAD-gebruikers accounts in te richten.
> 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel nieuw Relic in het toegangs venster klikt, moet u automatisch worden aangemeld bij de nieuwe Relic waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

