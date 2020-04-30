---
title: 'Zelf studie: integratie Azure Active Directory met iQualify LMS | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en iQualify LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: a1db4784eb63df14b7e7971d0273512ba657df96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68944996"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Zelf studie: integratie Azure Active Directory met iQualify LMS

In deze zelf studie leert u hoe u iQualify LMS integreert met Azure Active Directory (Azure AD).
Het integreren van iQualify-LMS met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot iQualify LMS.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij iQualify LMS (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met iQualify LMS wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* abonnement voor eenmalige aanmelding van iQualify LMS

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* iQualify LMS ondersteunt SSO die door **SP en IDP** is geïnitieerd
* iQualify LMS ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-iqualify-lms-from-the-gallery"></a>IQualify LMS toevoegen uit de galerie

Als u de integratie van iQualify LMS wilt configureren in azure AD, moet u iQualify LMS vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om iQualify-LMS toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **IQUALIFY LMS**, selecteer **iQualify LMS** in resultaat paneel en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![iQualify-LMS in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met iQualify LMS op basis van een test gebruiker met de naam **Julia Simon**.
Als u eenmalige aanmelding wilt gebruiken, moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in iQualify-LMS tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met iQualify LMS, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van IQUALIFY LMS configureren](#configure-iqualify-lms-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een iQualify-test gebruiker](#create-iqualify-lms-test-user)** voor het maken van een equivalent van Julia Simon in iQualify LMS dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met iQualify LMS:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **iQualify LMS** -toepassings integratie de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    ![informatie over eenmalige aanmelding voor iQualify LMS en Url's](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: 
    | |
    |--|--|
    | Productie omgeving:`https://<yourorg>.iqualify.com/`|
    | Test omgeving:`https://<yourorg>.iqualify.io`|

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: 
    | |
    |--|--|
    | Productie omgeving:`https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Test omgeving:`https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![informatie over eenmalige aanmelding voor iQualify LMS en Url's](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: 
    | |
    |--|--|
    | Productie omgeving:`https://<yourorg>.iqualify.com/login` |
    | Test omgeving:`https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van IQUALIFY LMS](https://www.iqualify.com/) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Uw iQualify-LMS verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op pictogram **bewerken** om het dialoog venster **gebruikers kenmerken** te openen.

    ![installatiekopie](common/edit-attribute.png)

7. Bewerk in het gedeelte **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** de claims met het **pictogram Bewerken** of voeg de claims toe door met **Nieuwe claim toevoegen** het kenmerk van het SAML-token te configureren, zoals wordt weergegeven in de bovenstaande afbeelding. Hierna voert u de volgende stappen uit:

    | Naam | Bronkenmerk|
    | --- | --- |
    | e-mail | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "uw kenmerk" |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![installatiekopie](common/new-save-attribute.png)

    ![installatiekopie](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

    > [!Note]
    > Het **person_id** kenmerk is **optioneel**

8. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

9. Kopieer op de sectie **IQUALIFY LMS instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-iqualify-lms-single-sign-on"></a>Eenmalige aanmelding voor iQualify LMS configureren

1. Open een nieuw browser venster en meld u vervolgens aan bij uw iQualify-omgeving als beheerder.

1. Zodra u bent aangemeld, klikt u rechtsboven op uw avatar en klikt u vervolgens op **account instellingen**

    ![Accountinstellingen](./media/iqualify-tutorial/setting1.png)

1. Klik in het gebied account instellingen op het lint menu aan de linkerkant en klik op **integraties**

    ![INTEGRATIES](./media/iqualify-tutorial/setting2.png)

1. Klik onder INTEGRATIEs op het **SAML** -pictogram.

    ![SAML-pictogram](./media/iqualify-tutorial/setting3.png)

1. Voer in het dialoog venster **SAML-verificatie-instellingen** de volgende stappen uit:

    ![SAML-verificatie-instellingen](./media/iqualify-tutorial/setting4.png)

    a. Plak in het vak **URL voor eenmalige** aanmelding met SSO de **aanmeldings-URL** die u hebt gekopieerd in het configuratie venster van de Azure AD-toepassing.

    b. Plak in het vak **SAML-AFmeldings-URL** de waarde voor de **afmeldings-URL** die u hebt gekopieerd in het venster Azure AD-toepassings configuratie.

    c. Open het gedownloade certificaat bestand in Klad blok, kopieer de inhoud en plak het in het vak **openbaar certificaat** .

    d. Voer in het **Label AANMELDINGS knop** de naam in voor de knop die op de aanmeldings pagina moet worden weer gegeven.

    e. Klik op **Opslaan**.

    f. Klik op **bijwerken**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension**.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen aan iQualify LMS.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **iQualify LMS**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **IQUALIFY LMS**.

    ![De iQualify-LMS-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-iqualify-lms-test-user"></a>Een iQualify-test gebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in iQualify LMS. iQualify LMS ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in iQualify LMS, wordt er na verificatie een nieuwe gemaakt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel iQualify LMS in het toegangs venster klikt, moet u de aanmeldings pagina van uw iQualify-LMS-toepassing ophalen. 

   ![aanmeldings pagina](./media/iqualify-tutorial/login.png) 

Klik op **Aanmelden met de Azure AD** -knop om automatisch aan te melden bij uw iQualify LMS-toepassing.

Zie [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster. 

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)