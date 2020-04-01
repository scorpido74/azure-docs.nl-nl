---
title: 'Zelfstudie: Azure Active Directory-integratie met Trisotech Digital Enterprise Server | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Trisotech Digital Enterprise Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 56399f99ede611c4a120603cce3a3eede2728c6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088266"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Zelfstudie: Azure Active Directory-integratie met Trisotech Digital Enterprise Server

In deze zelfstudie leert u hoe u Trisotech Digital Enterprise Server integreert met Azure Active Directory (Azure AD).
De integratie van Trisotech Digital Enterprise Server met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Trisotech Digital Enterprise Server.
* U uw gebruikers automatisch laten aanmelden bij Trisotech Digital Enterprise Server (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie wilt configureren met Trisotech Digital Enterprise Server, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Trisotech Digital Enterprise Server eenmalig aanmeldingsabonnement

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Trisotech Digital Enterprise Server ondersteunt **DOOR SP** geïnitieerde SSO

* Trisotech Digital Enterprise Server ondersteunt **Just In Time** gebruikersinrichting

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Trisotech Digital Enterprise Server toevoegen vanuit de galerij

Als u de integratie van Trisotech Digital Enterprise Server in Azure AD wilt configureren, moet u Trisotech Digital Enterprise Server vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Trisotech Digital Enterprise Server vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **Trisotech Digital Enterprise**Server in het zoekvak, selecteer **Trisotech Digital Enterprise Server** uit het resultaatpaneel en klik op Knop **Toevoegen** om de toepassing toe te voegen.

     ![Trisotech Digital Enterprise Server in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Trisotech Digital Enterprise Server op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding om te werken, moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Trisotech Digital Enterprise Server.

Als u Azure AD Single Sign-on met Trisotech Digital Enterprise Server wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Trisotech Digital Enterprise Server Single Sign-On](#configure-trisotech-digital-enterprise-server-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Trisotech Digital Enterprise Server-testgebruiker](#create-trisotech-digital-enterprise-server-test-user)** - om een tegenhanger van Britta Simon in Trisotech Digital Enterprise Server te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD te configureren met Trisotech Digital Enterprise Server:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Trisotech Digital Enterprise** Server-toepassingsintegratie de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Trisotech Digital Enterprise Server Domain en URL's single sign-on informatie](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.trisotech.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<companyname>.trisotech.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [trisotech Digital Enterprise Server Client support team](mailto:support@trisotech.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>Trisotech Digital Enterprise Server Single Sign-On configureren

1. Meld u in een ander browservenster aan bij uw site voor trisotech Digital Enterprise Server Configuration als beheerder.

2. Klik op het **menupictogram** en selecteer **Vervolgens Beheer**.

    ![Eenmalige aanmelding configureren](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. Selecteer **Gebruikersprovider**.

    ![Eenmalige aanmelding configureren](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. Voer in de sectie **Configuratie van gebruikersprovider** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Selecteer **Beveiligde bevestigingsmarkeringstaal 2 (SAML 2)** in de vervolgkeuzelijst in de **verificatiemethode**.

    b. Plak in het tekstvak **metagegevens URL** de **url-waarde van de appfederatie,** die u hebt gekopieerd, de Azure-portal.

    c. Voer in het tekstvak **Toepassings-id** de `https://<companyname>.trisotech.com`URL in met het volgende patroon: .

    d. Klik **op Opslaan**

    e. Voer de domeinnaam in het **tekstvak Toegestane domeinen (leeg betekent iedereen)** in, hiermee worden automatisch licenties toegeschreven voor gebruikers die overeenkomen met de toegestane domeinen

    f. Klik **op Opslaan**

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Trisotech Digital Enterprise Server.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **vervolgens Trisotech Digital Enterprise Server**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Trisotech Digital Enterprise Server**in de lijst met toepassingen .

    ![De Trisotech Digital Enterprise Server-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>Trisotech Digital Enterprise Server-testgebruiker maken

In deze sectie wordt een gebruiker genaamd Britta Simon gemaakt in Trisotech Digital Enterprise Server. Trisotech Digital Enterprise Server ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Trisotech Digital Enterprise Server, wordt er een nieuwe gemaakt na verificatie.

>[!Note]
>Als u handmatig een gebruiker wilt maken, neemt u contact op met [het ondersteuningsteam van Trisotech Digital Enterprise Server.](mailto:support@trisotech.com)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Trisotech Digital Enterprise Server in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Trisotech Digital Enterprise Server waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

