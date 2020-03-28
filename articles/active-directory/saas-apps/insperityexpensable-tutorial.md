---
title: 'Zelfstudie: Azure Active Directory-integratie met Insperity ExpensAble | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Insperity ExpensAble.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c579c453-580e-417d-8a5e-9b6b352795c0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 43b24bec557d1cc879be9568bea451f1a8b0b310
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67625323"
---
# <a name="tutorial-azure-active-directory-integration-with-insperity-expensable"></a>Zelfstudie: Azure Active Directory-integratie met Insperity ExpensAble

In deze zelfstudie leert u hoe U Insperity ExpensAble integreren met Azure Active Directory (Azure AD).
Het integreren van Insperity ExpensAble met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Insperity ExpensAble.
* U uw gebruikers automatisch laten inloggen op Insperity ExpensAble (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met Insperity ExpensAble, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Insperity ExpensAble single sign-on enabled subscription Insperity ExpensAble single sign-on enabled subscription Insperity ExpensAble single sign-on enabled subscription Ins

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Insperity ExpensAble ondersteunt **SP** geïnitieerdse SSO

## <a name="adding-insperity-expensable-from-the-gallery"></a>Insperity ExpensAble toevoegen vanuit de galerie

Als u de integratie van Insperity ExpensAble in Azure AD wilt configureren, moet u Insperity ExpensAble vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Insperity ExpensAble toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ In het zoekvak **Insperity ExpensAble**, selecteer **Insperity ExpensAble** in het resultaatpaneel en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Insperity ExpensAble in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Insperity ExpensAble op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Insperity ExpensAble.

Als u Azure AD-eenmaligaanmelding wilt configureren en testen met Insperity ExpensAble, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Insperity Expensable Single Sign-On](#configure-insperity-expensable-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak insperity ExpensAble-testgebruiker](#create-insperity-expensable-test-user)** - om een tegenhanger van Britta Simon in Insperity ExpensAble te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om azure AD single sign-on te configureren met Insperity ExpensAble:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Insperity ExpensAble** application integration de optie **Single sign-on**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Insperity ExpensAble Domain and URLLs single sign-on information Insperity Expensable Domain and URLLs single sign-on information Insperity Expensable Domain and URLLs single sign-on information Ins](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://server.expensable.com/esapp/Authenticate?companyId=<company ID>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [insperity ExpensAble Client support team](https://www.insperity.com/products/expense-management/support/express/) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Insperity ExpensAble** instellen de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-insperity-expensable-single-sign-on"></a>Insperity Expensable Single Sign-On configureren

Als u eenmalige aanmelding wilt configureren aan de **Kant van Insperity ExpensAble,** moet u het gedownloade **certificaat (Base64)** en de juiste gekopieerde URL's van Azure-portal naar [het ondersteuningsteam voor Insperity ExpensAble](https://www.insperity.com/products/expense-management/support/express/)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Insperity ExpensAble.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Insperity ExpensAble**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Insperity ExpensAble in**de lijst met toepassingen .

    ![De koppeling Insperity ExpensAble in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-insperity-expensable-test-user"></a>Insperity ExpensAble testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in Insperity ExpensAble. Werk samen met [insperity ExpensAble support team](https://www.insperity.com/products/expense-management/support/express/) om de gebruikers toe te voegen in het Insperity ExpensAble platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Insperity ExpensAble in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Insperity ExpensAble waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
