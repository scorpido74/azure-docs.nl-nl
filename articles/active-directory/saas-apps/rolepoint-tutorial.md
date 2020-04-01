---
title: 'Zelfstudie: Azure Active Directory-integratie met RolePoint | Microsoft Documenten'
description: In deze zelfstudie leert u hoe u eenmalige aanmelding tussen Azure Active Directory en RolePoint configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68d37f40-15da-45f5-a9e1-d53f78e786d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: 0b6fd17d2f8577532778733866260f43e9ac7685
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092738"
---
# <a name="tutorial-azure-active-directory-integration-with-rolepoint"></a>Zelfstudie: Azure Active Directory-integratie met RolePoint

In deze zelfstudie leert u hoe u RolePoint integreert met Azure Active Directory (Azure AD).
Deze integratie biedt deze voordelen:

* U Azure AD gebruiken om te bepalen wie toegang heeft tot RolePoint.
* U uw gebruikers automatisch laten inloggen op RolePoint (enkele aanmelding) met hun Azure AD-accounts.
* U uw accounts beheren op één centrale locatie: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met RolePoint, moet u het als:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/)krijgen.
* Een RolePoint-abonnement met enkele aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD single sign-on in een testomgeving.

* RolePoint ondersteunt door SP geïnitieerde SSO.

## <a name="add-rolepoint-from-the-gallery"></a>RolePoint toevoegen vanuit de galerie

Als u de integratie van RolePoint in Azure AD wilt instellen, moet u RolePoint vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Selecteer azure **active directory**in de [Azure-portal](https://portal.azure.com)in het linkerdeelvenster:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen** > **Alle toepassingen:**

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Nieuwe toepassing selecteren](common/add-new-app.png)

4. Voer in het zoekvak **RolePoint**in . Selecteer **RolePoint** in de zoekresultaten en selecteer **Vervolgens Toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met RolePoint met behulp van een testgebruiker genaamd Britta Simon.
Als u eenmalige aanmelding wilt inschakelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de desbetreffende gebruiker in RolePoint.

Als u Azure AD-single sign-on met RolePoint wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Configureer de single sign-on van Azure AD](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Configureer RolePoint single sign-on](#configure-rolepoint-single-sign-on)** aan de toepassingszijde.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om azure AD-enkele aanmelding te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om azure AD-enkele aanmelding voor de gebruiker in te schakelen.
5. **[Maak een RolePoint-testgebruiker](#create-a-rolepoint-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u Azure AD single sign-on in de Azure-portal in.

Ga als volgt te werk om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met RolePoint te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina RolePoint-toepassingsintegratie de optie **Enkele aanmelding:**

    ![Eén aanmelding selecteren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Een enkele aanmeldingsmethode** selecteren de **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen:

    ![Eén aanmeldingsmethode selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **Eén aanmelding instellen met SAML** het pictogram **Bewerken** om het dialoogvenster **BasisSAML-configuratie** te openen:

    ![Bewerkpictogram](common/edit-urls.png)

4. Neem in het dialoogvenster **BasisSAML-configuratie** de volgende stappen.

    ![Dialoogvenster BasisSAML-configuratie](common/sp-identifier.png)

    1. Voer in het vak **Aanmelding smaken van URL** een URL in dit patroon in:

       `https://<subdomain>.rolepoint.com/login`

    1. Voer in het vak **Id (Entity ID)** een URL in dit patroon in:

       `https://app.rolepoint.com/<instancename>`

    > [!NOTE]
    > Deze waarden zijn tijdelijke aanduidingen. U moet de werkelijke aanmeldings-URL en id gebruiken. We raden u aan een unieke tekenreekswaarde in de id te gebruiken. Neem contact op met het [ondersteuningsteam van RolePoint](mailto:info@rolepoint.com) om deze waarden op te halen. U ook verwijzen naar de patronen die worden weergegeven in het dialoogvenster **BasisSAML-configuratie** in de Azure-portal.

5. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de koppeling **Downloaden** naast **Federation Metadata XML**, volgens uw vereisten, en sla het bestand op uw computer op.

    ![De koppeling om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **RolePoint instellen** de juiste URL's op basis van uw vereisten:

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD-id**.

    1. **Url van afmelden**.


### <a name="configure-rolepoint-single-sign-on"></a>RolePoint-aanmelding configureren

Als u één aanmelding wilt instellen aan de RolePoint-kant, moet u samenwerken met het [Ondersteuningsteam van RolePoint.](mailto:info@rolepoint.com) Stuur dit team het XML-bestand Federatiemetagegevens en de URL's die u van de Azure-portal hebt gekregen. Ze configureren RolePoint om ervoor te zorgen dat de SAML SSO-verbinding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker met de naam Britta Simon in de Azure-portal.

1. Selecteer in de Azure-portal **Azure Active Directory** in het linkerdeelvenster, selecteer **Gebruikers**en selecteer **Alle gebruikers:**

    ![Selecteer Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het venster:

    ![Nieuwe gebruiker selecteren](common/new-user.png)

3. Neem **in** het dialoogvenster Gebruiker de volgende stappen.

    ![Dialoogvenster Gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer in het vak **Gebruikersnaam** **BrittaSimon@\<uw\< bedrijfsdomein in>. uitbreiding>**. (Bijvoorbeeld .) BrittaSimon@contoso.com

    1. Selecteer **Wachtwoord weergeven**en noteer de waarde in het vak **Wachtwoord.**

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door haar toegang te verlenen tot RolePoint.

1. Selecteer in de Azure-portal **Enterprise-toepassingen,** selecteer **Alle toepassingen**en selecteer **Vervolgens RolePoint**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **RolePoint**in de lijst met toepassingen .

    ![Lijst van aanvragen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **gebruikers en groepen:**

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen de optie **Britta Simon** in de gebruikerslijst en klik op de knop **Selecteren** onder aan het venster.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik op de knop **Selecteren** onder aan het venster.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-rolepoint-test-user"></a>Een RolePoint-testgebruiker maken

Vervolgens moet u een gebruiker met de naam Britta Simon maken in RolePoint. Werk samen met het [Ondersteuningsteam Van RolePoint](mailto:info@rolepoint.com) om gebruikers toe te voegen aan RolePoint. Gebruikers moeten worden gemaakt en geactiveerd voordat u eenmalige aanmelding gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u uw Azure AD-configuratie met eenmalige aanmelding testen met behulp van het Access-paneel.

Wanneer u de rolepoint-tegel in het toegangspaneel selecteert, moet u automatisch worden aangemeld bij het RolePoint-exemplaar waarvoor u SSO hebt ingesteld. Zie [Apps openen en gebruiken op de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over het toegangspaneel.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
