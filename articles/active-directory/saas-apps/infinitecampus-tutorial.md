---
title: 'Zelf studie: integratie Azure Active Directory met oneindige campus | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en oneindige campus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d54769c1f3265e2cee619520044313fca46855a5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67100359"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Zelf studie: integratie Azure Active Directory met oneindige campus

In deze zelf studie leert u hoe u een oneindige campus kunt integreren met Azure Active Directory (Azure AD).
Het integreren van een oneindige campus met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot oneindige campus.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij oneindige campus (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met oneindige campus wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Oneindig universiteits abonnement voor eenmalige aanmelding
* U moet mini maal een Azure Active Directory beheerder zijn en beschikken over de rol van student Information System (SIS) om de configuratie te volt ooien.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Oneindige campus ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-infinite-campus-from-the-gallery"></a>Een oneindige campus toevoegen vanuit de galerie

Als u de integratie van een oneindige campus wilt configureren in azure AD, moet u een oneindige campus uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit als u een oneindige campus wilt toevoegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, klikt u op de knop **nieuwe toepassing** boven aan het dialoog venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **oneindig campus**, selecteer **oneindige campus** in het deel venster resultaat en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Oneindige campus in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met een oneindige campus op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in oneindige campus tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met een oneindige campus, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. Een **[oneindige campus eenmalige aanmelding configureren](#configure-infinite-campus-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een oneindige campus-test gebruiker](#create-infinite-campus-test-user)** -een tegen hanger van Julia Simon in oneindige campus dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met een oneindige Campus:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **onbeperkte campus** -toepassings integratie de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer de volgende stappen uit in de sectie basis-SAML-configuratie (Houd er rekening mee dat het domein afhankelijk is van het hosting model, maar dat de **volledig gekwalificeerde domein** waarde moet overeenkomen met uw oneindige universiteits installatie):

    a. Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Typ in het tekstvak **id** een URL met het volgende patroon:`https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Oneindig universiteits domein en Url's eenmalige aanmelding](common/sp-identifier-reply.png)

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Eenmalige aanmelding voor een oneindige campus configureren

1. Meld u in een ander browser venster aan bij oneindige campus als beveiligings beheerder.

2. Klik aan de linkerkant van het menu op **systeem beheer**.

    ![De Beheerder](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Navigeer naar de configuratie van de**SSO-service provider**voor **gebruikers beveiliging** > **SAML Management** > .

    ![De SAML](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Voer de volgende stappen uit op de pagina **configuratie van SSO-service provider** :

    ![De SSO](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Selecteer **eenmalige SAML-aanmelding inschakelen**.

    b. Bewerk de **naam van het optionele kenmerk** dat de **naam** bevat

    c. Selecteer in de sectie **Selecteer een optie voor het ophalen van de server gegevens van de identiteits provider (IDP)** de **meta gegevens-URL**en plak de URL-waarde van de **app Federation-meta gegevens** die u hebt gekopieerd uit de Azure Portal in het vak en klik vervolgens op **synchroniseren**.

    d. Nadat u op **synchroniseren** hebt geklikt, worden de waarden automatisch ingevuld in de configuratie pagina van de **SSO-service provider** . Deze waarden kunnen worden gecontroleerd zodat ze overeenkomen met de waarden die in stap 4 hierboven worden weer gegeven.

    e. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ `brittasimon@yourcompanydomain.extension`in het veld **gebruikers naam** . Bijvoorbeeld BrittaSimon@contoso.com.

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

> [!NOTE]
> Als u wilt dat al uw Azure-gebruikers toegang hebben tot eenmalige toestemming voor een oneindige campus en gebruikmaken van het systeem voor interne campus-machtigingen om de toegang te beheren, kunt u de eigenschap **gebruikers toewijzing vereist** van de toepassing instellen op Nee en de volgende stappen overs Laan.

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan oneindige campus.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer **oneindige campus**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **oneindige campus**.

    ![De oneindige campus-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-infinite-campus-test-user"></a>Een oneindige campus-test gebruiker maken

Oneindige campus heeft een gecentreerde architectuur met demografische gegevens. Neem contact op met het [oneindig universiteits ondersteunings team](mailto:sales@infinitecampus.com) om de gebruikers toe te voegen aan het oneindige universiteits platform.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel oneindige campus in het toegangs venster klikt, moet u automatisch worden aangemeld bij de oneindige campus waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
