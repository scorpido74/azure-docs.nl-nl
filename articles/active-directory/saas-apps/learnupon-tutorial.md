---
title: 'Zelfstudie: Azure Active Directory-integratie met LearnUpon | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdcd8c6024b3cacb422b556718bbbdbb5d601c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098242"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Zelfstudie: Azure Active Directory-integratie met LearnUpon

In deze zelfstudie leert u hoe LearnUpon worden geïntegreerd met Azure Active Directory (Azure AD).
Het integreren van LearnUpon met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot LearnUpon.
* U uw gebruikers automatisch laten aanmelden bij LearnUpon (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met LearnUpon wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* LearnUpon enkel aanmelden ingeschakeld abonnement

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.


* LearnUpon ondersteunt **IDP** geïnitieerde SSO

* LearnUpon ondersteunt **Just In Time** gebruikersinrichting


## <a name="adding-learnupon-from-the-gallery"></a>LearnUpon toevoegen vanuit de galerij

Als u de integratie van LearnUpon in Azure AD wilt configureren, moet u LearnUpon vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om LearnUpon uit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **LearnUpon** uit **LearnUpon**het resultaatpaneel in het zoekvak en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![LearnUpon in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met LearnUpon op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in LearnUpon.

Als u Azure AD-singlesign-aan met LearnUpon wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[LearnUpon Single Sign-On configureren](#configure-learnupon-single-sign-on)** om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[LearnUpon-testgebruiker maken](#create-learnupon-test-user)** - om een tegenhanger van Britta Simon te hebben in LearnUpon die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren als u de enkele aanmelding van Azure AD met LearnUpon wilt configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **LearnUpon-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over learnUpon-domein en URL's met eenmalige aanmelding](common/idp-reply.png)

    Typ in het tekstvak **URL beantwoorden** een URL met het volgende patroon:`https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke antwoord-URL. Neem contact op met [learnUpon Client support team](https://www.learnupon.com/features/support/) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Zoek op de pagina **Single Sign-On with SAML instellen met SAML** de **THUMBPRINT** - Deze wordt toegevoegd aan uw LearnUpon SAML-instellingen.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

6. Kopieer in de sectie **LearnUpon instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-learnupon-single-sign-on"></a>Learns configureren bij eenmalige aanmelding

1. Open een andere browserinstantie en meld u aan bij LearnUpon met een beheerdersaccount.

1. Klik op het tabblad **Instellingen.**

    ![Eenmalige aanmelding configureren](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Klik **op Eén aanmelding - SAML**en klik vervolgens op **Algemene instellingen** om SAML-instellingen te configureren.
   
    ![Eenmalige aanmelding configureren](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. Voer in de sectie **Algemene instellingen** de volgende stappen uit:
   
    ![Eenmalige aanmelding configureren](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Selecteer **Ingeschakeld**.

    b. Selecteer **Versie** als **2.0**.

    c. Selecteer **Voorwaarden overslaan** als **Nee**.

    d. Typ in het tekstvak **SAML Token Post-param-naam** de naam van de parameter request post naar de hierboven aangegeven URL van de SAML-consument die de SAML-bewering bevat die moet worden geverifieerd en geverifieerd , bijvoorbeeld **SAMLResponse**.

    e. Typ in het tekstvak **Naam-id-indeling** de waarde die aangeeft waar in uw `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`SAML-bewering de gebruikers-id (E-mailadres) zich bevindt, bijvoorbeeld .
  
    f. Typ in het tekstvak **Locatie identificeren** de waarde die aangeeft waar de gebruikers naartoe worden verzonden als ze op het geüploade pictogram klikken vanaf het aanmeldingsscherm van uw Azure-portal.
  
    g. Plak in het tekstvak **AFmelden URL** de **URL-waarde van afmelden,** die u hebt gekopieerd van de Azure-portal.

    h. Klik **op Vingerafdrukken beheren**en upload vervolgens de vingerafdruk van het gedownloade certificaat.

1. Klik op **Gebruikersinstellingen**en voer de volgende stappen uit:

     ![Eenmalige aanmelding configureren](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. Typ in het tekstvak **Voornaam-id-indeling** de waarde die ons aangeeft waar in `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`uw SAML-bewering de voornaam van de gebruikers zich bevindt , bijvoorbeeld: .
  
    b. Typ in het tekstvak **Voornaam-id-indeling** de waarde die ons aangeeft waar in `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`uw SAML-bewering de achternaam van de gebruikers zich bevindt , bijvoorbeeld: .

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In **User name** het veld `brittasimon@yourcompanydomain.extension`type gebruikersnaam . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot LearnUpon.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **LearnUpon**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **LearnUpon**in de lijst met toepassingen .

    ![De koppeling LearnUpon in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-learnupon-test-user"></a>LearnUpon-testgebruiker maken

In deze sectie wordt een gebruiker genaamd Britta Simon gemaakt in LearnUpon. LearnUpon ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in LearnUpon, wordt er een nieuwe gemaakt na verificatie. Als u handmatig een gebruiker wilt maken, moet u contact opnemen met [het ondersteuningsteam van LearnUpon.](https://www.learnupon.com/features/support/)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel LearnUpon in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de LearnUpon waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)