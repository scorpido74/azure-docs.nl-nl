---
title: 'Zelfstudie: Azure Active Directory-integratie met Silverback | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Silverback.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 3c4eab02ed0c7c09fe9b5893bbaaf7cbe1c8028f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090916"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Zelfstudie: Azure Active Directory-integratie met Silverback

In deze zelfstudie leert u hoe u Silverback integreert met Azure Active Directory (Azure AD).
De integratie van Silverback met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Silverback.
* U uw gebruikers automatisch laten aanmelden bij Silverback (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met Silverback wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement met één aanmelding in Silverback

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Silverback steunt **SP** geïnitieerde SSO

## <a name="adding-silverback-from-the-gallery"></a>Silverback toevoegen vanuit de galerie

Als u de integratie van Silverback in Azure AD wilt configureren, moet u Silverback uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Silverback uit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Silverback**in het zoekvak , selecteer **Silverback** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Silverback in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Silverback op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Silverback.

Als u Azure AD-singlesign-aan met Silverback wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Silverback Single Sign-On](#configure-silverback-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Silverback-testgebruiker](#create-silverback-test-user)** - om een tegenhanger van Britta Simon in Silverback te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Silverback te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Silverback-toepassingsintegratie** de optie **Eén aanmelding .**

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over silverback-domeinen en URL's met eenmalige aanmelding](common/sp-identifier-reply.png)

    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<YOURSILVERBACKURL>.com/ssp`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `<YOURSILVERBACKURL>.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [silverback client support team](mailto:helpdesk@matrix42.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-silverback-single-sign-on"></a>Silverback-aanmelding configureren

1. Log in een andere webbrowser in bij uw Silverback Server als beheerder.

2. Navigeer naar de > **beheerderverificatieprovider**. **Admin**

3. Voer op de pagina **Instellingen van verificatieprovider** de volgende stappen uit:

    ![De beheerder](./media/silverback-tutorial/tutorial_silverback_admin.png)

    a.  Klik op **Importeren van URL**.

    b.  Plak de url van gekopieerde metagegevens en klik op **OK**.

    c.  Bevestig met **OK** en de waarden worden automatisch ingevuld.

    d.  **Weergeven inschakelen op aanmeldingspagina**.

    e.  **Dynamische gebruikers maken** inschakelen als u automatisch geautoriseerde gebruikers van Azure AD wilt toevoegen (optioneel).

    f.  Maak een **titel** voor de knop op de Self Service Portal.

    g.  Upload een **pictogram** door op **Bestand kiezen**te klikken .

    h.  Selecteer de **achtergrondkleur** voor de knop.

    i.  Klik op **Opslaan**.

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

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Silverback.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Silverback**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Silverback**in de lijst met toepassingen .

    ![De Silverback-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-silverback-test-user"></a>Silverback-testgebruiker maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij Silverback, moeten ze zijn ingericht in Silverback. In Silverback is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Log in bij uw Silverback Server als beheerder.

2. Navigeer naar **gebruikers** en **voeg een nieuwe apparaatgebruiker toe.**

3. Voer **op de** pagina Basis de volgende stappen uit:

    ![Gebruiker](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. Voer **in het** tekstvak Gebruikersnaam de naam van de gebruiker in, zoals **Britta.**

    b. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **Britta**.

    c. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals **Simon**.

    d. Voer in het tekstvak **E-mailadres** de **Brittasimon@contoso.com**e-mail van de gebruiker in, zoals .

    e. Voer in het tekstvak **Wachtwoord** uw wachtwoord in.

    f. Voer in het tekstvak **Wachtwoord bevestigen** opnieuw uw wachtwoord in en bevestig deze.

    g. Klik op **Opslaan**.

> [!NOTE]
> Als u niet elke gebruiker handmatig wilt maken Schakel het selectievakje **Dynamische gebruikerscreatie** in onder **Admin** > **Authentication Provider**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de silverback-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Silverback waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

