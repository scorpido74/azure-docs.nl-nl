---
title: 'Zelf studie: integratie Azure Active Directory met thoughtworks Mingle | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en thoughtworks mingle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a12d4dca61734275ef0e56dfe2a800c64dc52540
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233294"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Zelf studie: integratie Azure Active Directory met thoughtworks Mingle

In deze zelf studie leert u hoe u thoughtworks Mingle integreert met Azure Active Directory (Azure AD).
Het integreren van thoughtworks Mingle met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot thoughtworks mingle.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij thoughtworks Mingle (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met thoughtworks Mingle wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor thoughtworks Mingle-eenmalige aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Thoughtworks Mingle ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Thoughtworks Mingle toevoegen vanuit de galerie

Als u de integratie van thoughtworks Mingle wilt configureren in azure AD, moet u thoughtworks Mingle van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om thoughtworks Mingle toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **thoughtworks Mingle**in het zoekvak, selecteer **thoughtworks Mingle** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![Thoughtworks Mingle in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met thoughtworks Mingle op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in thoughtworks Mingle tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met thoughtworks Mingle, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor thoughtworks Mingle configureren](#configure-thoughtworks-mingle-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een thoughtworks Mingle-test gebruiker](#create-thoughtworks-mingle-test-user)** -om een equivalent van Julia Simon in thoughtworks Mingle te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD met thoughtworks Mingle te configureren:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **thoughtworks Mingle** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor thoughtworks Mingle domein en Url's](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van thoughtworks Mingle](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer de gewenste URL ('s) volgens uw vereiste in het gedeelte **thoughtworks Mingle instellen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-thoughtworks-mingle-single-sign-on"></a>Eenmalige aanmelding voor thoughtworks Mingle configureren

1. Meld u aan bij de **thoughtworks Mingle** -bedrijfs site als beheerder.

2. Klik op het tabblad **beheer** en klik vervolgens op **SSO-configuratie**.
   
    ![Tabblad beheer](./media/thoughtworks-mingle-tutorial/ic785157.png "SSO-configuratie")

3. Voer in de sectie **SSO-configuratie** de volgende stappen uit:
   
    ![SSO-configuratie](./media/thoughtworks-mingle-tutorial/ic785158.png "SSO-configuratie")
    
    a. Klik op **bestand kiezen**om het meta gegevensbestand te uploaden. 

    b. Klik op **Wijzigingen opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. Typ brittasimon@yourcompanydomain.extensionin het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot thoughtworks mingle.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **thoughtworks Mingle**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **thoughtworks Mingle**.

    ![De koppeling thoughtworks Mingle in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-thoughtworks-mingle-test-user"></a>Thoughtworks Mingle-test gebruiker maken

Als Azure AD-gebruikers zich kunnen aanmelden, moeten ze worden ingericht voor de thoughtworks Mingle-toepassing met behulp van hun Azure Active Directory gebruikers namen. In het geval van thoughtworks Mingle is inrichting een hand matige taak.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u aan bij de thoughtworks Mingle-bedrijfs site als beheerder.

2. Klik op **profiel**.
   
    ![Uw eerste project](./media/thoughtworks-mingle-tutorial/ic785160.png "Uw eerste project")

3. Klik op het tabblad **beheerder** en klik vervolgens op **gebruikers**.
   
    ![Gebruikers](./media/thoughtworks-mingle-tutorial/ic785161.png "Gebruikers")

4. Klik op **New User**.
   
    ![Nieuwe gebruiker](./media/thoughtworks-mingle-tutorial/ic785162.png "Nieuwe gebruiker")

5. Voer de volgende stappen uit in het dialoogvenster **New User**:
   
    ![Dialoog venster nieuwe gebruiker](./media/thoughtworks-mingle-tutorial/ic785163.png "Nieuwe gebruiker")  
 
    a. Typ de **aanmeldings naam**, **weergave naam**, **Kies wacht woord**, **Bevestig het wacht woord** voor een geldig Azure ad-account dat u wilt inrichten in de Verwante tekst vakken. 

    b. Selecteer **volledige gebruiker**als **gebruikers type**.

    c. Klik op **Dit profiel maken**.

>[!NOTE]
>U kunt elk ander thoughtworks Mingle voor het maken van gebruikers accounts of Api's van thoughtworks Mingle gebruiken om Azure AD-gebruikers accounts in te richten.
> 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel thoughtworks Mingle in het toegangs venster klikt, moet u automatisch worden aangemeld bij de thoughtworks Mingle waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

