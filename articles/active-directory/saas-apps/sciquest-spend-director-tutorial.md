---
title: 'Zelfstudie: Azure Active Directory-integratie met SciQuest Spend Director | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SciQuest Spend Director.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 199f531485a6a6c9ea234b35a7dbb88b66b3067c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091642"
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Zelfstudie: Azure Active Directory-integratie met SciQuest Spend Director

In deze zelfstudie leert u hoe u SciQuest Spend Director integreert met Azure Active Directory (Azure AD).
De integratie van SciQuest Spend Director met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot SciQuest Spend Director.
* U uw gebruikers automatisch laten aanmelden bij SciQuest Spend Director (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie wilt configureren met SciQuest Spend Director, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* SciQuest Spend Director single sign-on enabled subscription SciQuest Spend Director single sign-on enabled subscription SciQuest Spend Director single sign-on enabled subscription SciQuest

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SciQuest Spend Director ondersteunt **SP** geïnitieerde SSO
* SciQuest Spend Director ondersteunt **Just In Time** gebruikersinrichting

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>Het toevoegen van SciQuest Spend Director uit de galerij

Als u de integratie van SciQuest Spend Director in Azure AD wilt configureren, moet u SciQuest Spend Director vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u SciQuest Spend Director vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **SciQuest Spend**Director in het zoekvak , selecteer **SciQuest Spend Director** uit het resultaatpaneel en klik op Knop **Toevoegen** om de toepassing toe te voegen.

     ![SciQuest Spend Director in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met SciQuest Spend Director op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SciQuest Spend Director.

Als u Azure AD single sign-on wilt configureren en testen met SciQuest Spend Director, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer SciQuest Spend Director Single Sign-On](#configure-sciquest-spend-director-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak SciQuest Spend Director-testgebruiker](#create-sciquest-spend-director-test-user)** - om een tegenhanger van Britta Simon in SciQuest Spend Director te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met SciQuest Spend Director te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina Voor de integratie van de **SciQuest Spend** **Director-toepassing de**optie Enkele aanmelding .

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![SciQuest Spend Director Domain en URL's single sign-on informatie](common/sp-identifier-reply.png)

    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<companyname>.sciquest.com/apps/Router/SAMLAuth/<instancename>`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<companyname>.sciquest.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<companyname>.sciquest.com/apps/Router/ExternalAuth/Login/<instancename>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [SciQuest Spend Director Client support team](https://www.jaggaer.com/contact-us/) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **SciQuest Spend Director** de juiste URL(s) naar uw behoefte.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-sciquest-spend-director-single-sign-on"></a>SciQuest Spend Director Single Sign-On configureren

Als u eenmalige aanmelding wilt configureren aan de kant **van SciQuest Spend Director,** moet u de gedownloade **XML met aalmetagegevens** van de Federatie en de juiste gekopieerde URL's van Azure-portal naar [het ondersteuningsteam van SciQuest Spend Director](https://www.jaggaer.com/contact-us/)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot SciQuest Spend Director.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **vervolgens SciQuest Spend Director**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SciQuest Spend Director**in de lijst met toepassingen.

    ![De Link SciQuest Spend Director in de lijst Met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sciquest-spend-director-test-user"></a>SciQuest Spend Director-testgebruiker maken

Het doel van deze sectie is het creëren van een gebruiker genaamd Britta Simon in SciQuest Spend Director.

U moet contact opnemen met uw [SciQuest Spend Director support team](https://www.jaggaer.com/contact-us/) en hen voorzien van de details over uw testaccount om het te laten maken.

U ook just-in-time provisioning gebruiken, een enkele aanmeldingsfunctie die wordt ondersteund door SciQuest Spend Director.  
Als just-in-time provisioning is ingeschakeld, worden gebruikers automatisch gemaakt door SciQuest Spend Director tijdens een enkele aanmeldingspoging als ze niet bestaan. Deze functie elimineert de noodzaak om handmatig eenmalige aanmeldingsgebruikers te maken.

Om just-in-time provisioning ingeschakeld te krijgen, moet u contact opnemen met uw [SciQuest Spend Director support team.](https://www.jaggaer.com/contact-us/)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SciQuest Spend Director in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de SciQuest Spend Director waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)