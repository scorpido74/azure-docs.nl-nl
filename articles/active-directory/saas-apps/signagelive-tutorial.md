---
title: 'Zelf studie: integratie Azure Active Directory met Signagelive | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Signagelive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d923f0e7-ad31-4d59-a6fd-f0e895e1a32d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dab2fd0ee2f25e835b4bd07a3534475d3d93b5e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160924"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Zelf studie: integratie Azure Active Directory met Signagelive

In deze zelfstudie leert u hoe u Signagelive kunt integreren met Azure AD (Azure Active Directory).
Integratie van Signagelive met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Signagelive.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Signagelive (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts op één centrale locatie beheren: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD. Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om Azure AD-integratie met Signagelive te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [proef versie van één maand](https://azure.microsoft.com/pricing/free-trial/)ontvangen.
* Een Signagelive abonnement met eenmalige aanmelding.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Signagelive ondersteunt door SP geïnitieerde SSO.

## <a name="add-signagelive-from-the-gallery"></a>Signagelive toevoegen vanuit de galerie

Als u de integratie van Signagelive in azure AD wilt configureren, voegt u eerst Signagelive uit de galerie toe aan uw lijst met beheerde SaaS-apps.

Voer de volgende stappen uit om Signagelive toe te voegen uit de galerie:

1. Selecteer in de [Azure Portal](https://portal.azure.com)in het linkerdeel venster het **Azure Active Directory** pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer vervolgens de optie **alle toepassingen** .

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het dialoog venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Signagelive**in het zoekvak. 

     ![Signagelive in de resultatenlijst](common/search-new-app.png)

5. Selecteer **Signagelive** in het resultaten venster en selecteer vervolgens de knop **toevoegen** om de toepassing toe te voegen.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met Signagelive op basis van een testgebruiker met de naam **Britta Simon**.
Als u eenmalige aanmelding wilt gebruiken, moet u een koppeling tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Signagelive.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Signagelive, voltooit u eerst de volgende bouw stenen:

1. [Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers deze functie kunnen gebruiken.
2. [Signagelive eenmalige aanmelding configureren](#configure-signagelive-single-sign-on) om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
3. [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) voor het testen van eenmalige aanmelding van Azure AD met Britta Simon.
4. [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding van Azure AD.
5. [Maak een Signagelive-test gebruiker](#create-a-signagelive-test-user) die een equivalent van Julia Simon in Signagelive heeft dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Signagelive:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de pagina voor integratie van toepassingen met **Signagelive** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer in het dialoog venster **eenmalige aanmelding selecteren** de optie **SAML** om eenmalige aanmelding in te scha kelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** de optie **bewerken** om het dialoog venster **eenvoudige SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer de volgende stappen uit in de sectie **basis configuratie van SAML** :

    ![Informatie over eenmalige aanmelding bij Signagelive-domeinen en -URL's](common/sp-signonurl.png)

    Voer in het vak **aanmeld-URL** een URL in die gebruikmaakt van het volgende patroon: `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team](mailto:support@signagelive.com) van de Signagelive-client om de waarde op te halen. U kunt ook verwijzen naar de patronen die worden weer gegeven in de sectie **basis configuratie van SAML** in de Azure Portal.

5. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** de optie **downloaden** om het **certificaat (RAW)** te downloaden uit de opgegeven opties volgens uw vereiste. Sla het vervolgens op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

6. Kopieer de URL ('s) die u nodig hebt in de sectie **Signagelive instellen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-signagelive-single-sign-on"></a>Eenmalige aanmelding voor Signagelive configureren

Als u eenmalige aanmelding wilt configureren aan de Signagelive zijde, verzendt u het gedownloade **certificaat (RAW)** en de gekopieerde url's van de Azure Portal naar het [ondersteunings team van Signagelive](mailto:support@signagelive.com). Ze zorgen ervoor dat de SAML SSO-verbinding aan beide kanten juist is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![De knop Nieuwe gebruiker](common/new-user.png)

3. Voer in het dialoog venster **gebruiker** de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. Voer in het veld **gebruikers naam de waarde**brittasimon@yourcompanydomain.extensionin. In dit geval kunt u bijvoorbeeld 'BrittaSimon@contoso.com' invoeren.

    c. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak wacht woord.

    d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon toestemming voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Signagelive.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Signagelive**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen de optie **Signagelive**.

    ![De Signagelive-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer de knop **gebruiker toevoegen** . Selecteer vervolgens in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** in de lijst **gebruikers** de optie **Julia Simon**. Klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een rolnaam verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik vervolgens op de knop **selecteren** onder aan het scherm.

7. Selecteer de knop **toewijzen** in het dialoog venster **toewijzing toevoegen** .

### <a name="create-a-signagelive-test-user"></a>Een Signagelive-test gebruiker maken

In deze sectie maakt u een gebruiker in Signagelive met de naam Britta Simon. Werk samen met het [Signagelive-ondersteunings team](mailto:support@signagelive.com) om de gebruikers toe te voegen aan het Signagelive-platform. U moet gebruikers maken en activeren voordat u eenmalige aanmelding gebruikt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie van eenmalige aanmelding voor Azure AD met behulp van de MyApps-Portal.

Wanneer u de tegel **Signagelive** in de MyApps-Portal selecteert, moet u zich automatisch aanmelden. Zie [Wat is de MyApps-Portal?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over de MyApps-Portal.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

