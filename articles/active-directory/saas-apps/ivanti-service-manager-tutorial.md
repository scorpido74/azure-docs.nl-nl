---
title: 'Zelf studie: integratie Azure Active Directory met Ivanti Service Manager (ISM) | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Ivanti Service Manager (ISM).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 14297c74-0d57-4146-97fa-7a055fb73057
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e45903bfc54f2c685abea0a8e2924274a21e07b3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73157967"
---
# <a name="tutorial-azure-active-directory-integration-with-ivanti-service-manager-ism"></a>Zelf studie: integratie Azure Active Directory met Ivanti Service Manager (ISM)

In deze zelf studie leert u hoe u Ivanti Service Manager (ISM) integreert met Azure Active Directory (Azure AD).
Het integreren van Ivanti Service Manager (ISM) met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Ivanti-Service Manager (ISM).
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld voor Ivanti Service Manager (ISM) (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Ivanti Service Manager (ISM) wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement op eenmalige aanmelding voor Ivanti Service Manager (ISM) ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Ivanti Service Manager (ISM) ondersteunt door **SP en IDP** GEÏNITIEERDe SSO
* Ivanti Service Manager (ISM) ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-ivanti-service-manager-ism-from-the-gallery"></a>Ivanti Service Manager (ISM) toevoegen vanuit de galerie

Als u de integratie van Ivanti-Service Manager (ISM) wilt configureren in azure AD, moet u Ivanti Service Manager (ISM) uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Ivanti Service Manager (ISM) wilt toevoegen uit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Ivanti Service Manager (ISM)** in het zoekvak, selecteer **IVANTI Service Manager (ISM)** in het resultaten paneel en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![Ivanti Service Manager (ISM) in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Ivanti Service Manager (ISM) op basis van een test gebruiker met de naam **Julia Simon**.
Als u eenmalige aanmelding wilt gebruiken, moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Ivanti-Service Manager (ISM) tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Ivanti Service Manager (ISM), moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Ivanti Service Manager (ISM) configureren: eenmalige aanmelding](#configure-ivanti-service-manager-ism-single-sign-on)** voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een Ivanti Service Manager (ISM) test gebruiker](#create-ivanti-service-manager-ism-test-user)** : als u een equivalent van Julia Simon in Ivanti Service Manager (ISM) wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Ivanti Service Manager (ISM):

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **IVANTI Service Manager (ISM)** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    ![Ivanti Service Manager (ISM) domein en Url's gegevens voor eenmalige aanmelding](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: 
    
    | |
    |--|
    | `https://<customer>.saasit.com/` |
    | `https://<customer>.saasiteu.com/` |
    | `https://<customer>.saasitau.com/` |

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<customer>/handlers/sso/SamlAssertionConsumerHandler.ashx`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Ivanti Service Manager (ISM) domein en Url's gegevens voor eenmalige aanmelding](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<customer>.saasit.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Raw)** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

7. Kopieer op de sectie **Ivanti-Service Manager (ISM) instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-ivanti-service-manager-ism-single-sign-on"></a>Eenmalige aanmelding van Ivanti Service Manager (ISM) configureren

Als u eenmalige aanmelding wilt configureren op **Ivanti Service Manager (ISM)** , moet u het gedownloade **certificaat (RAW)** en de juiste gekopieerde url's verzenden van Azure Portal naar het [ondersteunings team Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ in het veld **gebruikers naam** **brittasimon\@yourcompanydomain. extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen tot Ivanti Service Manager (ISM).

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Ivanti Service Manager (ISM)**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Ivanti Service Manager (ISM)**.

    ![De koppeling Ivanti Service Manager (ISM) in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-ivanti-service-manager-ism-test-user"></a>Test gebruiker voor Ivanti Service Manager (ISM) maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in Ivanti Service Manager (ISM). Ivanti Service Manager (ISM) biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker niet al aanwezig is in de Ivanti-Service Manager (ISM), wordt er een nieuwe gemaakt na verificatie.

> [!Note]
> Als u hand matig een gebruiker moet maken, neemt u contact op met het [ondersteunings team van Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Ivanti Service Manager (ISM) in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Ivanti Service Manager (ISM) waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

