---
title: 'Zelfstudie: Azure Active Directory-integratie met Mitel Connect | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Mitel Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 30a4dcbd15d8a47d99b3a61879a7e61938d3b133
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160531"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Zelfstudie: Azure Active Directory-integratie met Mitel MiCloud Connect

In deze zelfstudie leert u hoe u Mitel MiCloud Connect integreren met Azure Active Directory (Azure AD). De integratie van MiCloud Connect met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot MiCloud Connect-apps met hun bedrijfsreferenties.
* U ervoor zorgen dat gebruikers in uw account automatisch worden aangemeld bij MiCloud Connect (Single Sign-On) met hun Azure AD-accounts.


Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u de AD-integratie van Azure wilt configureren met MiCloud Connect, hebt u de volgende items nodig:

* Een Azure AD-abonnement

  Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een Mitel MiCloud Connect-account

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD single sign-on (SSO).

* Mitel Connect ondersteunt **SP** geïnitieerde SSO

## <a name="adding-mitel-connect-from-the-gallery"></a>Mitel Connect toevoegen vanuit de galerie

Als u de integratie van Mitel Connect in Azure AD wilt configureren, moet u Mitel Connect vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps in de Azure-portal.

**Ga als volgt te werk om Mitel Connect vanuit de galerie toe te voegen:**

1. Klik in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster op **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Klik **op Bedrijfstoepassingen** en klik vervolgens op **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Klik **op Nieuwe toepassing**.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Mitel Connect** in het zoekveld, klik op **Mitel Connect** van het deelvenster Resultaten en klik vervolgens op **Toevoegen**.

     ![Mitel Connect in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met MiCloud Connect op basis van een testgebruiker met de naam **Britta Simon.** Voor eenmalige aanmelding om te werken, moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in MiCloud Connect.

Als u Azure AD Single Sign-on wilt configureren en testen met MiCloud Connect, moet u de volgende stappen uitvoeren:

1. **[Configureer MiCloud Connect voor SSO met Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** - zodat uw gebruikers deze functie kunnen gebruiken en de SSO-instellingen aan de toepassingszijde kunnen configureren.
2. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
3. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
4. **[Maak een Mitel MiCloud Connect-testgebruiker](#create-a-mitel-micloud-connect-test-user)** - om een tegenhanger van Britta Simon op uw MiCloud Connect-account te hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
5. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>MiCloud Connect voor SSO configureren met Azure AD

In deze sectie schakelt u Azure AD single sign-on voor MiCloud Connect in de Azure-portal in en configureert u uw MiCloud Connect-account zodat SSO gebruik kan maken van Azure AD.

Om MiCloud Connect met SSO voor Azure AD te configureren, is het het gemakkelijkst om de Azure-portal en de Mitel-accountportal naast elkaar te openen. U moet bepaalde informatie van de Azure-portal kopiëren naar de Mitel-accountportal en een aantal van de Mitel-accountportal naar de Azure-portal.


1. Ga als volgt te werk om de configuratiepagina in de [Azure-portal](https://portal.azure.com/)te openen:

    a. Klik op de pagina Met toepassing-integratie van **Mitel Connect** op **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

    b. Klik in het dialoogvenster **Een enkele aanmeldingsmethode** selecteren op **SAML**.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)
    
    De op SAML gebaseerde aanmeldingspagina wordt weergegeven.

2. Ga als volgt te werk om het configuratiedialoogvenster in de Mitel-accountportal te openen:

    a. Klik in het menu **Telefoonsysteem** op **Invoegfuncties**.

    b. Klik rechts van **Eenmalige aanmelding**op **Activeren** of **Instellingen**.
    
    Het dialoogvenster Instellingen voor eenmalig aanmelden verbinden wordt weergegeven.
    
3. Schakel het selectievakje **Eén aanmelding inschakelen** in.
    ![afbeelding](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. Klik in de Azure-portal op het pictogram **Bewerken** in de sectie **BasisSAML-configuratie.**
    ![afbeelding](common/edit-urls.png)

    Het dialoogvenster BasisSAML-configuratie wordt weergegeven.

5.  Kopieer de URL vanuit het veld **Mitel Identifier (Entity ID)** in de Mitel-accountportal en plak deze in het veld **Id (Entity ID)** in de Azure-portal.

6. Kopieer de URL vanuit het veld URL van de **antwoord-URL (Bevestigingsconsumentenservice)** in de Mitel-accountportal en plak deze in het veld **Antwoord-URL (Bevestigingsconsumentenservice-URL)** in de Azure-portal.  
   ![afbeelding](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. Typ in het tekstvak **Aantekenen op URL** een van de volgende URL's:

    * **https://portal.shoretelsky.com**- om de Mitel Account portal te gebruiken als uw standaard Mitel applicatie
    * **https://teamwork.shoretel.com**- teamwork gebruiken als uw standaard Mitel applicatie

    **OPMERKING:** De standaard Toepassing Mitel is de toepassing die wordt geopend wanneer een gebruiker op de mitel connect-tegel in het toegangspaneel klikt. Dit is ook de toepassing die wordt geopend bij het maken van een testinstallatie vanuit Azure AD.

8. Klik **op Opslaan** in het dialoogvenster **BasisSAML-configuratie** in de Azure-portal.

9. Klik in de sectie **SAML-ondertekeningscertificaat** op de **op SAML gebaseerde aanmeldingspagina** in de Azure-portal op **Downloaden** naast **Certificaat (Base64)** om het **ondertekeningscertificaat** te downloaden en op te slaan op uw computer.
    ![afbeelding](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Open het bestand Certificaat ondertekenen in een teksteditor, kopieer alle gegevens in het bestand en plak de gegevens in het veld **Certificaat ondertekenen** in de portal voor Mitel-account. 
    ![afbeelding](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. Ga als volgt te werk in de sectie **Setup Mitel Connect** op de **op SAML gebaseerde aanmeldingspagina** van de Azure-portal:

    a. Kopieer de URL vanuit het veld **Aanmeldings-URL** en plak deze in het **veld AanmeldingsURL** in de Portal van Mitel-account.

    b. Kopieer de URL vanuit het veld **Azure AD-id** en plak deze in het veld **Entiteit-id** in de Mitel-accountportal.
    ![afbeelding](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Klik op **Opslaan** in het dialoogvenster **Interne aanmeldingsinstellingen verbinden** in de mitel-accountportal.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

In deze sectie maakt u een testgebruiker met de naam Britta Simon in de Azure-portal.

1. Klik in de Azure-portal in het linkerdeelvenster op **Azure Active Directory**, klik op **Gebruikers**en klik vervolgens op **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Klik boven aan het scherm op **Nieuwe gebruiker.**

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. Ga in het dialoogvenster Gebruikerseigenschappen de volgende stappen uit:

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Typ **BrittaSimon**in het veld **Naam** .
  
    b. Typ **User name** in het veld\<Gebruikersnaam brittasimon@\>uwbedrijfsdomein. \<uitbreiding\>.  
Bijvoorbeeld BrittaSimon@contoso.com.

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Mitel Connect.

1. Klik in de Azure-portal op **Bedrijfstoepassingen**en klik vervolgens op **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Klik in de lijst met toepassingen op **Mitel Connect**.

    ![De Mitel Connect-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Klik in het menu aan de linkerkant op **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik **op Gebruiker toevoegen**en klik vervolgens op Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen De optie **Britta Simon** in de lijst **Gebruikers** en klik op **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u de juiste rol voor de gebruiker in de lijst in het dialoogvenster **Rol selecteren** en klikt u onder aan het scherm op **Selecteren.**

7. Klik in het dialoogvenster **Toewijzing toevoegen** op **Toewijzing toewijzen**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Een Mitel MiCloud Connect-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon op uw MiCloud Connect-account. Gebruikers moeten worden gemaakt en geactiveerd voordat ze eenmalige aanmelding gebruiken.

Zie het artikel [Toevoegen van een gebruiker](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) in de Mitel Knowledge Base voor meer informatie over het toevoegen van gebruikers in de Mitel-accountportal.

Maak een gebruiker op uw MiCloud Connect-account met de volgende gegevens:

  * **Naam:** Britta Simon

* **Zakelijk e-mailadres:**`brittasimon@<yourcompanydomain>.<extension>`   
(Voorbeeld: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Gebruikersnaam:**`brittasimon@<yourcompanydomain>.<extension>`  
(Voorbeeld: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); de gebruikersnaam van de gebruiker is meestal hetzelfde als het zakelijke e-mailadres van de gebruiker)

**LET OP:** De MiCloud Connect-gebruikersnaam van de gebruiker moet identiek zijn aan het e-mailadres van de gebruiker in Azure.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u uw Azure AD-configuratie met eenmalige aanmelding met het Toegangspaneel.

Wanneer u op de tegel Mitel Connect in het access-paneel klikt, moet u automatisch worden doorgestuurd om u aan te melden bij de MiCloud Connect-toepassing die u als standaard hebt geconfigureerd in het veld **Aanmelden op URL.** Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
