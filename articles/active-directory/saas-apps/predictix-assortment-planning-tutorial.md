---
title: 'Zelfstudie: Azure Active Directory-integratie met Predictix-assortimentsplanning | Microsoft Documenten'
description: In deze zelfstudie leert u hoe u eenmalige aanmelding configureren tussen Azure Active Directory en Predictix-assortimentsplanning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 37e686ff-f8e5-40b1-9d7e-f64b076917b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: bc3ea2f6fddc233a69d96c0c885ab310ed1e77c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094154"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>Zelfstudie: Azure Active Directory-integratie met Predictix-assortimentsplanning

In deze zelfstudie leert u hoe u Predictix-assortimentsplanning integreren met Azure Active Directory (Azure AD).
Deze integratie biedt deze voordelen:

* U Azure AD gebruiken om te bepalen wie toegang heeft tot Predictix-assortimentsplanning.
* U uw gebruikers automatisch laten inloggen bij Predictix-assortimentsplanning (enkele aanmelding) met hun Azure AD-accounts.
* U uw accounts beheren op één centrale locatie: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie wilt configureren met Predictix-assortimentsplanning, moet u het volledige nieuwe programma hebben:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/pricing/free-trial/)krijgen.
* Een Predictix-abonnement voor assortimentsplanning dat één aanmelding heeft ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD single sign-on in een testomgeving.

* Predictix Assortment Planning ondersteunt SP-geïnitieerde SSO.

## <a name="add-predictix-assortment-planning-from-the-gallery"></a>Predictix-assortimentsplanning toevoegen vanuit de galerie

Als u de integratie van Predictix-assortimentsplanning wilt instellen in Azure AD, moet u Predictix-assortimentsplanning vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Selecteer azure **active directory**in de [Azure-portal](https://portal.azure.com)in het linkerdeelvenster:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen** > **Alle toepassingen:**

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Nieuwe toepassing selecteren](common/add-new-app.png)

4. Voer in het zoekvak **Predictix-assortimentsplanning in**. Selecteer **Predictix-assortimentsplanning** in de zoekresultaten en selecteer **Vervolgens Toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Predictix-assortimentsplanning met behulp van een testgebruiker genaamd Britta Simon.
Als u eenmalige aanmelding wilt inschakelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de desbetreffende gebruiker in Predictix-assortimentsplanning.

Als u Azure AD-singlesign-aan wilt configureren en testen met Predictix-assortimentsplanning, moet u de volgende stappen uitvoeren:

1. **[Configureer de single sign-on van Azure AD](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Configureer Predictix Assortment Planning single sign-on](#configure-predictix-assortment-planning-single-sign-on)** aan de toepassingszijde.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om azure AD-enkele aanmelding te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om azure AD-enkele aanmelding voor de gebruiker in te schakelen.
5. **[Maak een testgebruiker voor predictix-assortimentsplanning](#create-a-predictix-assortment-planning-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u Azure AD single sign-on in de Azure-portal in.

Ga als volgt te werk om Azure AD single sign-on te configureren met Predictix-assortimentsplanning:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina Voor de integratie van toepassingsintegratie **voor programma's voor het voorspellen van het assortiment** de optie Enkele **aanmelding:**

    ![Eén aanmelding selecteren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Een enkele aanmeldingsmethode** selecteren de **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen:

    ![Eén aanmeldingsmethode selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **Eén aanmelding instellen met SAML** het pictogram **Bewerken** om het dialoogvenster **BasisSAML-configuratie** te openen:

    ![Bewerkpictogram](common/edit-urls.png)

4. Voer in het dialoogvenster **BasisSAML-configuratie** de volgende stappen uit.

    ![Dialoogvenster BasisSAML-configuratie](common/sp-identifier.png)

    1. Voer in het vak **Aanmelding smaken van URL** een URL in dit patroon in:

       | |
        |--|
        | `https://<sub-domain>.ap.predictix.com/sso/request`|
        | `https://<sub-domain>.dev.ap.predictix.com/`|
        | |

    1. Voer in het vak **Id (Entity ID)** een URL in dit patroon in:

        | |
        |--|
        | `https://<sub-domain>.ap.predictix.com`|
        | `https://<sub-domain>.dev.ap.predictix.com`|
        | |

    > [!NOTE]
    > Deze waarden zijn tijdelijke aanduidingen. U moet de werkelijke aanmeldings-URL en id gebruiken. Neem contact op met het [supportteam voor predictix-planning](https://www.infor.com/support) om de waarden te krijgen. U ook verwijzen naar de patronen die worden weergegeven in het dialoogvenster **BasisSAML-configuratie** in de Azure-portal.

5. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de koppeling **Downloaden** naast **Certificaat (Base64)** en sla het certificaat op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Predictix-assortimentsplanning** instellen de juiste URL's op basis van uw vereisten:

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD-id**.

    1. **Url van afmelden**.

### <a name="configure-predictix-assortment-planning-single-sign-on"></a>Eén aanmelding voor Predictix-assortimentsplanning configureren

Als u eenmalige aanmelding wilt configureren aan de kant van de programmaplanning van Predictix, moet u het certificaat dat u hebt gedownload en de URL's die u hebt gekopieerd van de Azure-portal naar het [ondersteuningsteam voor de planning van het assortiment van Predictix](https://www.infor.com/support)verzenden. Dit team zorgt ervoor dat de SAML SSO-verbinding aan beide kanten goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker met de naam Britta Simon in de Azure-portal.

1. Selecteer in de Azure-portal **Azure Active Directory** in het linkerdeelvenster, selecteer **Gebruikers**en selecteer **Alle gebruikers:**

    ![Selecteer Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm:

    ![Nieuwe gebruiker selecteren](common/new-user.png)

3. Neem **in** het dialoogvenster Gebruiker de volgende stappen.

    ![Dialoogvenster Gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer in het vak **Gebruikersnaam** **BrittaSimon@\<uw\< bedrijfsdomein in>. uitbreiding>**. (Bijvoorbeeld .) BrittaSimon@contoso.com

    1. Selecteer **Wachtwoord weergeven**en noteer de waarde in het vak **Wachtwoord.**

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure AD single sign-on te gebruiken door haar toegang te verlenen tot Predictix-assortimentsplanning.

1. Selecteer in de Azure-portal **Enterprise-toepassingen,** selecteer **Alle toepassingen**en selecteer vervolgens **De assortimentsplanning van Predictix**.

    ![Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer In de lijst met toepassingen de optie **Predictix-assortimentsplanning**.

    ![Lijst van aanvragen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **gebruikers en groepen:**

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen de optie **Britta Simon** in de gebruikerslijst en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik op de knop **Selecteren** onder aan het scherm.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-predictix-assortment-planning-test-user"></a>Een testgebruiker voor predictix-assortimentsplanning maken

Vervolgens moet u een gebruiker met de naam Britta Simon maken in Predictix Assortment Planning. Werk samen met het [ondersteuningsteam voor Predictix-assortimentplanning](https://www.infor.com/support) om gebruikers toe te voegen. Gebruikers moeten worden gemaakt en geactiveerd voordat u eenmalige aanmelding gebruikt.

> [!NOTE]
> De houder van het Azure AD-account ontvangt een e-mail en selecteert een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u uw Azure AD-configuratie met eenmalige aanmelding testen met behulp van het Access-paneel.

Wanneer u de tegel Predictix-assortimentsplanning selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij het voorbeeld Voor het opstellen van Het Assortiment van Predictix waarvoor u SSO hebt ingesteld. Zie [Apps openen en gebruiken op de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)