---
title: 'Zelfstudie: Azure Active Directory-integratie met Rightscale | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Rightscale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 53799b62da043b7680f010e1eaaf0d9243f07dd5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093077"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Zelfstudie: Azure Active Directory-integratie met Rightscale

In deze zelfstudie leert u hoe u Rightscale integreert met Azure Active Directory (Azure AD).
Het integreren van Rightscale met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Rightscale.
* U uw gebruikers automatisch laten aanmelden bij Rightscale (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Rightscale wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Abonnement met één aanmelding op de juiste schaal

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Rightscale ondersteunt **SP en IDP** geïnitieerd sso

## <a name="adding-rightscale-from-the-gallery"></a>Rechtsschaal toevoegen vanuit de galerie

Als u de integratie van Rightscale in Azure AD wilt configureren, moet u Rightscale vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Rightscale vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Rechtsschalen**, selecteer **Rechtsschalen** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Rechtsschalen in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Rightscale op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Rightscale.

Als u Azure AD-single sign-on met Rightscale wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Rightscale Single Sign-On](#configure-rightscale-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak de testgebruiker van Rightscale](#create-rightscale-test-user)** - om een tegenhanger van Britta Simon in Rightscale te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Rightscale te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Integratie van de juiste toepassing** de optie Eén **aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![Informatie over het rechterwaardendomein en URL's met eenmalige aanmelding](common/preintegrated.png)

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Informatie over het rechterwaardendomein en URL's met eenmalige aanmelding](common/metadata-upload-additional-signon.png)

    Typ de URL in het tekstvak **AANmeldings-URL:**`https://login.rightscale.com/`

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer in de sectie **Rechtsschalen instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-rightscale-single-sign-on"></a>Rightscale Single Sign-On configureren

1. Als u SSO wilt configureren voor uw toepassing, moet u zich aanmelden bij uw RightScale-tenant als beheerder.

2. Klik in het menu bovenaan op het tabblad **Instellingen** en selecteer **Eenmalig aanmelden**.

    ![Eenmalige aanmelding configureren](./media/rightscale-tutorial/tutorial_rightscale_001.png)

3. Klik op de **nieuwe** knop om **uw SAML-identiteitsproviders**toe te voegen.

    ![Eenmalige aanmelding configureren](./media/rightscale-tutorial/tutorial_rightscale_002.png)

4. Voer in het tekstvak **van Weergavenaam**uw bedrijfsnaam in.

    ![Eenmalige aanmelding configureren](./media/rightscale-tutorial/tutorial_rightscale_003.png)

5. Selecteer SSO met **rechtsschaal toestaan met behulp van een detectiehint** en voer uw **domeinnaam** in het onderstaande tekstvak in.

    ![Eenmalige aanmelding configureren](./media/rightscale-tutorial/tutorial_rightscale_004.png)

6. Plak de waarde van **de aanmeldings-URL** die u hebt gekopieerd van Azure-portal naar **SAML SSO-eindpunt** in RightScale.

    ![Eenmalige aanmelding configureren](./media/rightscale-tutorial/tutorial_rightscale_006.png)

7. Plak de waarde van **Azure AD-id** die u hebt gekopieerd van Azure-portal in **SAML-entiteitsid** in RightScale.

    ![Eenmalige aanmelding configureren](./media/rightscale-tutorial/tutorial_rightscale_008.png)

8. Klik op de knop **Browser** om het certificaat te uploaden dat u hebt gedownload van de Azure-portal.


    ![Eenmalige aanmelding configureren](./media/rightscale-tutorial/tutorial_rightscale_009.png)

9. Klik op **Opslaan**.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Rightscale.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Rechterschaal**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Rightscale**in de lijst met toepassingen .

    ![De koppeling Rechtsschalen in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-rightscale-test-user"></a>Rechterschaaltestgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in Rightscale. Werk samen met [het ondersteuningsteam](mailto:support@rightscale.com) van Rightscale Client om de gebruikers toe te voegen in het Rightscale-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Rechtsschalen in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de juiste schaal waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
