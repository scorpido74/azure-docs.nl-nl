---
title: 'Zelfstudie: Azure Active Directory-integratie met Infinite Campus | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Infinite Campus.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100359"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Zelfstudie: Azure Active Directory-integratie met Infinite Campus

In deze zelfstudie leert u hoe u Infinite Campus integreert met Azure Active Directory (Azure AD).
De integratie van Infinite Campus met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Infinite Campus.
* U uw gebruikers automatisch laten aanmelden bij Infinite Campus (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Infinite Campus wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Abonnement met één aanmelding voor Infinite Campus
* U moet minimaal een Azure Active Directory-beheerder zijn en een campusproductbeveiligingsrol van 'Student Information System (SIS)' hebben om de configuratie te voltooien.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Infinite Campus ondersteunt **SP** geïnitieerde SSO

## <a name="adding-infinite-campus-from-the-gallery"></a>Infinite Campus toevoegen vanuit de galerie

Als u de integratie van Infinite Campus in Azure AD wilt configureren, moet u Infinite Campus vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Infinite Campus vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, klikt u boven aan het dialoogvenster op de knop **Nieuwe toepassing.**

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **Infinite Campus**in het zoekvak en selecteer **Infinite Campus** in het resultaatpaneel en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Infinite Campus in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Infinite Campus op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding om te werken, moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Infinite Campus.

Als u Azure AD single sign-on met Infinite Campus wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Infinite Campus Single Sign-On](#configure-infinite-campus-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak de testgebruiker van Infinite Campus](#create-infinite-campus-test-user)** - om een tegenhanger van Britta Simon in Infinite Campus te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Infinite Campus te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Infinite Campus-toepassingsintegratie** de optie Eén **aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie BasisSAML-configuratie de volgende stappen uit (houd er rekening mee dat het domein zal variëren met hostingmodel, maar de **waarde VOLLEDIG GEKWALIFICEERD-DOMEIN** moet overeenkomen met uw Infinite Campus-installatie):

    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Typ **in** het tekstvak Identifier een URL met het volgende patroon:`https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Infinite Campus Domain en URL's single sign-on informatie](common/sp-identifier-reply.png)

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Infinite Campus Single Sign-On configureren

1. Meld u in een ander browservenster aan bij Infinite Campus als beveiligingsbeheerder.

2. Klik aan de linkerkant van het menu op **Systeembeheer**.

    ![De Beheerder](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Navigeer naar de configuratie van**SAML-beheer** > **sso-serviceproviders** **voor gebruikersbeveiliging.** > 

    ![De saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Voer op de pagina **Configuratie van sso-serviceproviders** de volgende stappen uit:

    ![De sso](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Selecteer **SAML-eenmalig aanmelding inschakelen**.

    b. De **optionele kenmerknaam** bewerken om **de naam** te bevatten

    c. Selecteer in de sectie **IDP-servergegevens (Selecteren selecteren om de servergegevens van de identiteitsprovider op te halen,** selecteer **URL met ametagegevens**, plak de **url-waarde van de app-federatie-metagegevens,** die u hebt gekopieerd uit de Azure-portal in het vak en klik vervolgens op **Synchroniseren**.

    d. Nadat u op **Synchroniseren** hebt geklikt, worden de waarden automatisch ingevuld op de pagina **Configuratie van sso-serviceproviders.** Deze waarden kunnen worden geverifieerd op de waarden die in stap 4 hierboven worden gezien.

    e. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In **User name** het veld `brittasimon@yourcompanydomain.extension`type gebruikersnaam . Bijvoorbeeld BrittaSimon@contoso.com.

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

> [!NOTE]
> Als u wilt dat al uw Azure-gebruikers één aanmeldingstoegang hebben tot Infinite Campus en vertrouwen op het interne machtigingensysteem van Infinite Campus om de toegang te beheren, u de eigenschap **Vereist gebruikerstoewijzing** van de toepassing instellen op Nee en de volgende stappen overslaan.

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Infinite Campus.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Infinite Campus**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Infinite Campus**in de lijst met toepassingen .

    ![De koppeling Infinite Campus in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-infinite-campus-test-user"></a>Infinite Campus-testgebruiker maken

Infinite Campus heeft een demografische gecentreerde architectuur. Neem contact op met [het ondersteuningsteam van Infinite Campus](mailto:sales@infinitecampus.com) om de gebruikers toe te voegen in het Infinite Campus-platform.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Infinite Campus in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Infinite Campus waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
