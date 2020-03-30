---
title: 'Zelfstudie: Azure Active Directory-integratie met Panorama9 | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Panorama9.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: c88088b804f90769512d33a5e96fdedc5ce6fba8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094931"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Zelfstudie: Azure Active Directory-integratie met Panorama9

In deze zelfstudie leert u hoe u Panorama9 integreert met Azure Active Directory (Azure AD).
Het integreren van Panorama9 met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Panorama9.
* U uw gebruikers automatisch laten aanmelden bij Panorama9 (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Panorama9 wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement met één aanmelding met één aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Panorama9 ondersteunt **DOOR SP** geïnitieerde SSO

## <a name="adding-panorama9-from-the-gallery"></a>Panorama9 toevoegen vanuit de galerie

Als u de integratie van Panorama9 in Azure AD wilt configureren, moet u Panorama9 vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Panorama9 vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **Panorama9**in het zoekvak , selecteer **Panorama9** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Panorama9 in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Panorama9 op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding om te werken, moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Panorama9.

Als u Azure AD-eenmaligaanmelding met Panorama9 wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Panorama9-aanmelding configureren](#configure-panorama9-single-sign-on)** - om de instellingen voor eenmalig aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Panorama9-testgebruiker maken](#create-panorama9-test-user)** - om een tegenhanger van Britta Simon in Panorama9 te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Panorama9 te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Panorama9-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Panorama9-domein- en URL's met eenmalige aanmelding](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL: `https://dashboard.panorama9.com/saml/access/3262`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [het ondersteuningsteam van Panorama9](https://support.panorama9.com/) Client om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

6. Kopieer in de sectie **SAML-ondertekeningscertificaat** de **duimafdruk** en sla deze op uw computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

7. Kopieer in de sectie **Panorama9 instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-panorama9-single-sign-on"></a>Panorama9-aanmelding configureren

1. Meld u in een ander browservenster aan bij uw objectsite Panorama9 als beheerder.

2. Klik op de werkbalk bovenaan op **Beheren**en klik vervolgens op **Extensies**.
   
    ![Extensies](./media/panorama9-tutorial/ic790023.png "Extensies")

3. Klik in het dialoogvenster **Extensies** op **Eén aanmelding**.
   
    ![Eén aanmelding](./media/panorama9-tutorial/ic790024.png "Eenmalige aanmelding")

4. Voer **in** de sectie Instellingen de volgende stappen uit:
   
    ![Instellingen](./media/panorama9-tutorial/ic790025.png "Instellingen")
   
    a. Plak in **het tekstvak URL van de identiteitsprovider** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd vanuit azure-portal.
   
    b. Plak **in het** tekstvak Certificaat vingerafdruk de **thumbprint-waarde** van het certificaat, die u hebt gekopieerd van Azure-portal.    
         
5. Klik op **Opslaan**.

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

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Panorama9.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Panorama9**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Panorama9**in de lijst met toepassingen .

    ![De koppeling Panorama9 in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-panorama9-test-user"></a>Panorama9-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Panorama9, moeten ze worden ingericht in Panorama9.  

In het geval van Panorama9 is inrichten een handmatige taak.

**Voer de volgende stappen uit om de inrichting van gebruikers te configureren:**

1. Meld u aan bij uw **panorama9-bedrijfssite** als beheerder.

2. Klik in het menu bovenaan op **Beheren**en klik vervolgens op **Gebruikers**.
   
    ![Gebruikers](./media/panorama9-tutorial/ic790027.png "Gebruikers")

3. Klik in de **+** sectie Gebruikers om nieuwe gebruikers toe te voegen.

    ![Gebruikers](./media/panorama9-tutorial/ic790028.png "Gebruikers")

4. Ga naar de sectie Gebruikersgegevens en typ het e-mailadres van een geldige Azure Active Directory-gebruiker die u wilt inrichten in het tekstvak **E-mail.**

5. Kom naar de sectie Gebruikers, Klik op **Opslaan**.
   
    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Panorama9 in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Panorama9 waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

