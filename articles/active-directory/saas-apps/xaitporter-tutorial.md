---
title: 'Zelfstudie: Azure Active Directory-integratie met XaitPorter | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en XaitPorter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 8652073eb3d7d154958566b68fb6e27c35d8da30
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086520"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Zelfstudie: Azure Active Directory-integratie met XaitPorter

In deze zelfstudie leert u hoe u XaitPorter integreert met Azure Active Directory (Azure AD).
De integratie van XaitPorter met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot XaitPorter.
* U uw gebruikers automatisch laten aanmelden bij XaitPorter (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met XaitPorter wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/)krijgen.
* XaitPorter eenmalig aanmeldingsabonnement

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* XaitPorter ondersteunt **DOOR SP** geïnitieerde SSO

## <a name="adding-xaitporter-from-the-gallery"></a>XaitPorter toevoegen vanuit de galerie

Als u de integratie van XaitPorter in Azure AD wilt configureren, moet u XaitPorter uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om XaitPorter uit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **XaitPorter**in het zoekvak , selecteer **XaitPorter** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![XaitPorter in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met XaitPorter op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in XaitPorter.

Als u Azure AD-singlesign-aan met XaitPorter wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer XaitPorter Single Sign-On](#configure-xaitporter-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak XaitPorter-testgebruiker](#create-xaitporter-test-user)** - om een tegenhanger van Britta Simon in XaitPorter te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met XaitPorter te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **XaitPorter-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![XaitPorter-domein- en URL's met eenmalige aanmeldingsgegevens](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.xaitporter.com/saml/login`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<subdomain>.xaitporter.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [het ondersteuningsteam van XaitPorter Client](https://www.xait.com/support/) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

6. Geef het **IP-adres** of de Url van de **App Federation Metadata** op aan het [Ondersteuningsteam van SmartRecruiters,](https://www.smartrecruiters.com/about-us/contact-us/)zodat XaitPorter ervoor kan zorgen dat het IP-adres bereikbaar is via uw XaitPorter-instantie die whitelist aan hun zijde configureert. 

### <a name="configure-xaitporter-single-sign-on"></a>XaitPorter-aanmelding configureren

1. Als u de configuratie binnen XaitPorter wilt automatiseren, moet u **de beveiligingsextensie Mijn apps Secure installeren** door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup XaitPorter** en leidt u naar de XaitPorter-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij XaitPorter. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-6.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u XaitPorter handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij uw XaitPorter-bedrijfssite en voert u de volgende stappen uit:

4. Klik op **Beheerder**.

    ![Eenmalige aanmelding configureren](./media/xaitporter-tutorial/user1.png)

5. Selecteer **Eén aanmelding beheren** in de vervolgkeuzelijst **Systeeminstellingen.**

    ![Eenmalige aanmelding configureren](./media/xaitporter-tutorial/user2.png)

6. Voer in de sectie **EÉN AANMELDING BEHEREN** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/xaitporter-tutorial/user3.png)

    a. Selecteer **Verificatie voor eenmalig aanmelden inschakelen**.

    b. Plak in het tekstvak **Instellingen voor identiteitsprovider** de **url van de appfederatie-metagegevens** die u hebt gekopieerd uit de Azure-portal en klik op **Ophalen**.

    c. Selecteer **Autocreatie van gebruikers inschakelen**.

    d. Klik op **OK**.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot XaitPorter.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens XaitPorter**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **XaitPorter**in de lijst met toepassingen .

    ![De XaitPorter-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-xaitporter-test-user"></a>XaitPorter-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in XaitPorter. Werk samen met [het XaitPorter Client support team](https://www.xait.com/support/) om de gebruikers toe te voegen in het XaitPorter-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de XaitPorter-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de XaitPorter waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

