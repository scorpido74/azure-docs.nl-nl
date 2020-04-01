---
title: 'Zelfstudie: Azure Active Directory-integratie met IBM Kenexa Survey Enterprise | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en IBM Kenexa Survey Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: c649b966b3e210f6b026b06a9654761e0f97aea1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099060"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Zelfstudie: Azure Active Directory-integratie met IBM Kenexa Survey Enterprise

In deze zelfstudie leert u hoe u IBM Kenexa Survey Enterprise integreert met Azure Active Directory (Azure AD).
De integratie van IBM Kenexa Survey Enterprise met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot IBM Kenexa Survey Enterprise.
* U uw gebruikers automatisch laten aanmelden bij IBM Kenexa Survey Enterprise (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u de AD-integratie van Azure wilt configureren met IBM Kenexa Survey Enterprise, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* IBM Kenexa Survey Enterprise single sign-on enabled subscription IBM Kenexa Survey Enterprise

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* IBM Kenexa Survey Enterprise ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>IBM Kenexa Survey Enterprise toevoegen vanuit de galerij

Als u de integratie van IBM Kenexa Survey Enterprise in Azure AD wilt configureren, moet u IBM Kenexa Survey Enterprise vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om IBM Kenexa Survey Enterprise vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **IBM Kenexa**Survey Enterprise in het zoekvak, selecteer **IBM Kenexa Survey Enterprise** in het resultatenpaneel en klik op Knop **Toevoegen** om de toepassing toe te voegen.

     ![IBM Kenexa Survey Enterprise in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met IBM Kenexa Survey Enterprise op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppeling slinken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in IBM Kenexa Survey Enterprise.

Als u Azure AD Single Sign-on met IBM Kenexa Survey Enterprise wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer IBM Kenexa Survey Enterprise Single Sign-On](#configure-ibm-kenexa-survey-enterprise-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak IBM Kenexa Survey Enterprise-testgebruiker](#create-ibm-kenexa-survey-enterprise-test-user)** - om een tegenhanger van Britta Simon te hebben in IBM Kenexa Survey Enterprise die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om azure AD single sign-on te configureren met IBM Kenexa Survey Enterprise:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **IBM Kenexa Survey** Enterprise-toepassingsintegratie de optie **Enkele aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![IBM Kenexa Survey Enterprise Domain en URL's single sign-on informatie](common/idp-intiated.png)

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://surveys.kenexa.com/<companycode>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met [het ondersteuningsteam van IBM Kenexa Survey Enterprise Client](https://www.ibm.com/support/home/?lnk=fcw) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. De IBM Kenexa Survey Enterprise-toepassing verwacht de SAML-beweringen (Security Assertions Markup Language) in een specifieke indeling te ontvangen, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan de configuratie van uw SAML-tokenkenmerken. De waarde van de claim voor gebruikers-id's in het antwoord moet overeenkomen met de SSO-id die is geconfigureerd in het Kenexa-systeem. Werk samen met het ondersteuningsteam van [IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw)om de juiste gebruikers-id in uw organisatie als SSO Internet Datagram Protocol (IDP) in kaart te brengen.

    Azure AD stelt standaard de gebruikers-id in als de UPN-waarde (User Principal Name). U deze waarde wijzigen op het tabblad **Gebruikerskenmerken,** zoals in de volgende schermafbeelding wordt weergegeven. De integratie werkt pas nadat u de toewijzing correct hebt voltooid.

    ![installatiekopie](common/edit-attribute.png)

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer in de sectie **IBM Kenexa Survey Enterprise instellen** de juiste URL(s) volgens uw eis.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>IBM Kenexa Survey Enterprise Single Sign-On configureren

Als u eenmalige aanmelding wilt configureren aan de **IBM Kenexa Survey** Enterprise-kant, moet u het gedownloade **certificaat (Base64)** en de juiste gekopieerde URL's van Azure-portal naar het ondersteuningsteam van [IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot IBM Kenexa Survey Enterprise.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer vervolgens **IBM Kenexa Survey Enterprise**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **IBM Kenexa Survey Enterprise**in de lijst met toepassingen.

    ![De IBM Kenexa Survey Enterprise-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>IBM Kenexa Survey Enterprise-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in IBM Kenexa Survey Enterprise.

Als u gebruikers wilt maken in het IBM Kenexa Survey Enterprise-systeem en de SSO ID voor hen in kaart wilt brengen, u samenwerken met het [ondersteuningsteam van IBM Kenexa Survey Enterprise.](https://www.ibm.com/support/home/?lnk=fcw) Deze SSO-id-waarde moet ook worden toegewezen aan de waarde van de gebruikers-id vanuit Azure AD. U deze standaardinstelling wijzigen op het tabblad **Kenmerk.**

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel IBM Kenexa Survey Enterprise in het Access Panel klikt, moet u automatisch worden aangemeld bij de IBM Kenexa Survey Enterprise waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

