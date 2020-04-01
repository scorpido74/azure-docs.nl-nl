---
title: 'Zelfstudie: Azure Active Directory-integratie met Predictix-volgorde | Microsoft Documenten'
description: In deze zelfstudie leert u hoe u eenmalige aanmelding configureren tussen Azure Active Directory en Predictix Ordering.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2fe2f976-e97f-4368-9695-3e1624409e8b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 457ab3a0d5e816becbd2b32d858d5172951f27ad
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094124"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-ordering"></a>Zelfstudie: Azure Active Directory-integratie met Predictix-volgorde

In deze zelfstudie leert u hoe u Predictix-bestellen integreren met Azure Active Directory (Azure AD).
Deze integratie biedt deze voordelen:

* U Azure AD gebruiken om te bepalen wie toegang heeft tot Predictix Ordering.
* U uw gebruikers automatisch laten inloggen op Predictix Ordering (single sign-on) met hun Azure AD-accounts.
* U uw accounts beheren op één centrale locatie: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie wilt configureren met Predictix Bestellen, moet u het:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/pricing/free-trial/)krijgen.
* Een Predictix-bestelabonnement dat één aanmelding heeft ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD single sign-on in een testomgeving.

* Predictix Ordering ondersteunt sp-geïnitieerde SSO.

## <a name="add-predictix-ordering-from-the-gallery"></a>Predictix Bestellen toevoegen vanuit de galerie

Als u de integratie van Predictix-bestellen in Azure AD wilt instellen, moet u Predictix Bestellen vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Selecteer azure **active directory**in de [Azure-portal](https://portal.azure.com)in het linkerdeelvenster:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen** > **Alle toepassingen:**

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Nieuwe toepassing selecteren](common/add-new-app.png)

4. Voer in het zoekvak **Predictix-volgorde in.** Selecteer **Predictix-volgorde** in de zoekresultaten en selecteer **Vervolgens Toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Predictix Ordering met behulp van een testgebruiker genaamd Britta Simon.
Als u eenmalige aanmelding wilt inschakelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de desbetreffende gebruiker in Predictix Ordering.

Als u Azure AD Single Sign-on met Predictix Ordering wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Configureer de single sign-on van Azure AD](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Predictix Bestellen van eenmalige aanmelding](#configure-predictix-ordering-single-sign-on)** configureren aan de toepassingszijde.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om azure AD-enkele aanmelding te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om azure AD-enkele aanmelding voor de gebruiker in te schakelen.
5. **[Maak een Testgebruiker voor predictix bestellen](#create-a-predictix-ordering-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u Azure AD single sign-on in de Azure-portal in.

Ga als volgt te werk om Azure AD single sign-on te configureren met Predictix Ordering:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina Voor het bestellen van toepassingen op **Predictix** de optie **Enkele aanmelding:**

    ![Eén aanmelding selecteren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Een enkele aanmeldingsmethode** selecteren de **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen:

    ![Eén aanmeldingsmethode selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **Eén aanmelding instellen met SAML** het pictogram **Bewerken** om het dialoogvenster **BasisSAML-configuratie** te openen:

    ![Bewerkpictogram](common/edit-urls.png)

4. Voer in het dialoogvenster **BasisSAML-configuratie** de volgende stappen uit.

    ![Dialoogvenster BasisSAML-configuratie](common/sp-identifier.png)

    1. Voer in het vak **Aanmelding smaken van URL** een URL in dit patroon in:

       `https://<companyname-pricing>.ordering.predictix.com/sso/request`

    1. Voer in het vak **Id (Entity ID)** een URL in dit patroon in:

        | |
        |--|
        | `https://<companyname-pricing>.dev.ordering.predictix.com` |
        | `https://<companyname-pricing>.ordering.predictix.com` |
        | |

    > [!NOTE]
    > Deze waarden zijn tijdelijke aanduidingen. U moet de werkelijke aanmeldings-URL en id gebruiken. Neem contact op met het [ondersteuningsteam van Predictix Ordering](https://www.predix.io/support/) om de waarden te krijgen. U ook verwijzen naar de patronen die worden weergegeven in het dialoogvenster **BasisSAML-configuratie** in de Azure-portal.

5. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de koppeling **Downloaden** naast **Certificaat (Base64)** en sla het certificaat op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Predictix-volgorde instellen** de juiste URL's op basis van uw vereisten:

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    2. **Azure AD-id**.

    3. **Url van afmelden**.

### <a name="configure-predictix-ordering-single-sign-on"></a>Predictix Bestellen van eenmalige aanmelding configureren

Als u eenmalige aanmelding wilt configureren aan de kant van Predictix-volgorde, moet u het certificaat dat u hebt gedownload en de URL's die u hebt gekopieerd van de Azure-portal naar het [ondersteuningsteam voor het bestellen van Predictix](https://www.predix.io/support/)verzenden. Dit team zorgt ervoor dat de SAML SSO-verbinding aan beide kanten goed is ingesteld.

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

In deze sectie u Britta Simon inschakelen om Azure AD single sign-on te gebruiken door haar toegang te verlenen tot Predictix Ordering.

1. Selecteer in de Azure-portal **Enterprise-toepassingen,** selecteer **Alle toepassingen**en selecteer **Vervolgens Predictix-volgorde:**

    ![Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Predictix Bestellen**in de lijst met toepassingen .

    ![Lijst van aanvragen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **gebruikers en groepen:**

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen de optie **Britta Simon** in de gebruikerslijst en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik op de knop **Selecteren** onder aan het scherm.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-predictix-ordering-test-user"></a>Een testgebruiker voor Predictix bestellen maken

Vervolgens moet u een gebruiker met de naam Britta Simon maken in Predictix Ordering. Werk samen met het [ondersteuningsteam voor Predictix Ordering](https://www.predix.io/support/) om gebruikers toe te voegen. Gebruikers moeten worden gemaakt en geactiveerd voordat u eenmalige aanmelding gebruikt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u uw Azure AD-configuratie met eenmalige aanmelding testen met behulp van het Access-paneel.

Wanneer u de tegel Predictix-bestellen selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij het voorbeeld Predictix Bestellen waarvoor u SSO hebt ingesteld. Zie [Apps openen en gebruiken op de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
