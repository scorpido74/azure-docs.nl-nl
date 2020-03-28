---
title: 'Zelfstudie: Azure Active Directory-integratie met ITRP | Microsoft Documenten'
description: In deze zelfstudie leert u hoe u eenmalige aanmelding tussen Azure Active Directory en ITRP configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: d44391624e29d2bdd182bb07452e0e8def2d1407
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67656697"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Zelfstudie: Azure Active Directory-integratie met ITRP

In deze zelfstudie leert u hoe u ITRP integreert met Azure Active Directory (Azure AD).
Deze integratie biedt deze voordelen:

* U Azure AD gebruiken om te bepalen wie toegang heeft tot ITRP.
* U uw gebruikers automatisch laten inloggen op ITRP (single sign-on) met hun Azure AD-accounts.
* U uw accounts beheren op één centrale locatie: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met ITRP wilt configureren, moet u het:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/)krijgen.
* Een ITRP-abonnement met één aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD single sign-on in een testomgeving.

* ITRP ondersteunt door SP geïnitieerde SSO.

## <a name="add-itrp-from-the-gallery"></a>ITRP toevoegen vanuit de galerie

Als u de integratie van ITRP in Azure AD wilt instellen, moet u ITRP uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Selecteer azure **active directory**in de [Azure-portal](https://portal.azure.com)in het linkerdeelvenster:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen** > **Alle toepassingen:**

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Nieuwe toepassing selecteren](common/add-new-app.png)

4. Voer in het zoekvak **ITRP**in . Selecteer **ITRP** in de zoekresultaten en selecteer **Toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on with ITRP met behulp van een testgebruiker genaamd Britta Simon.
Als u eenmalige aanmelding wilt inschakelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de desbetreffende gebruiker in ITRP.

Als u Azure AD Single Sign-on with ITRP wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Configureer de single sign-on van Azure AD](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Configureren VAN ITRP single sign-on](#configure-itrp-single-sign-on)** aan de toepassingszijde.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om azure AD-enkele aanmelding te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om azure AD-enkele aanmelding voor de gebruiker in te schakelen.
5. **[Maak een ITRP-testgebruiker](#create-an-itrp-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u Azure AD single sign-on in de Azure-portal in.

Ga als volgt te werk om azure AD single sign-on met ITRP te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina ITRP-toepassingsintegratie de optie **Eén aanmelding:**

    ![Eén aanmelding selecteren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Een enkele aanmeldingsmethode** selecteren de **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen:

    ![Eén aanmeldingsmethode selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **Eén aanmelding instellen met SAML** het pictogram **Bewerken** om het dialoogvenster **BasisSAML-configuratie** te openen:

    ![Bewerkpictogram](common/edit-urls.png)

4. Neem in het dialoogvenster **BasisSAML-configuratie** de volgende stappen.

    ![Dialoogvenster BasisSAML-configuratie](common/sp-identifier.png)

    1. Voer in het vak **Aanmelding smaken van URL** een URL in dit patroon in:
    
       `https://<tenant-name>.itrp.com`

    1. Voer in het vak **Id (Entity ID)** een URL in dit patroon in:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Deze waarden zijn tijdelijke aanduidingen. U moet de werkelijke aanmeldings-URL en id gebruiken. Neem contact op met het [ITRP-ondersteuningsteam](https://www.itrp.com/support) om de waarden te krijgen. U ook verwijzen naar de patronen die worden weergegeven in het dialoogvenster **BasisSAML-configuratie** in de Azure-portal.

5. Selecteer in de sectie **SAML-ondertekeningscertificaat** het pictogram **Bewerken** om het dialoogvenster **SAML-ondertekeningscertificaat** te openen:

    ![Bewerkpictogram](common/edit-certificate.png)

6. Kopieer in het dialoogvenster **SAML-ondertekeningscertificaat** de waarde **Duimafdruk** en sla deze op:

    ![De waarde Duimafdruk kopiëren](common/copy-thumbprint.png)

7. Kopieer in de sectie **ITRP instellen** de juiste URL's op basis van uw vereisten:

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD-id**.

    1. **Url van afmelden**.

### <a name="configure-itrp-single-sign-on"></a>ITRP-aanmelding configureren

1. Meld u in een nieuw browservenster aan bij uw ITRP-bedrijfssite als beheerder.

1. Selecteer boven aan het venster het pictogram **Instellingen:**

    ![Het pictogram Instellingen](./media/itrp-tutorial/ic775570.png "Het pictogram Instellingen")

1. Selecteer in het linkerdeelvenster De optie **Eén aanmelding:**

    ![Eén aanmelding selecteren](./media/itrp-tutorial/ic775571.png "Eén aanmelding selecteren")

1. Neem in de sectie **Voormeldingsconfiguratie** de volgende stappen.

    ![Sectie Eenmalig aanmelden](./media/itrp-tutorial/ic775572.png "Sectie Eenmalig aanmelden")

    ![Sectie Eenmalig aanmelden](./media/itrp-tutorial/ic775573.png "Sectie Eenmalig aanmelden")

    1. Selecteer **Ingeschakeld**.

    1. Plak in het **vak URL voor afmelden op afstand** de **URL-waarde van afmelden** die u hebt gekopieerd uit de Azure-portal.

    1. Plak in het vak **SAML SSO URL** de **URL-waarde aanmelding** die u hebt gekopieerd uit de Azure-portal.

    1. Plak in het vingerafdrukvak **certificaat** de waarde **Duimafdruk** van het certificaat, die u hebt gekopieerd van de Azure-portal.

    1. Selecteer **Opslaan**.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door haar toegang te verlenen tot ITRP.

1. Selecteer in de Azure-portal **Enterprise-toepassingen,** selecteer **Alle toepassingen**en selecteer **VERVOLGENS ITRP**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **ITRP**in de lijst met toepassingen .

    ![Lijst van aanvragen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **gebruikers en groepen:**

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen de optie **Britta Simon** in de gebruikerslijst en klik op de knop **Selecteren** onder aan het venster.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik op de knop **Selecteren** onder aan het venster.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-an-itrp-test-user"></a>Een ITRP-testgebruiker maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij ITRP, moet u deze toevoegen aan ITRP. Je moet ze handmatig toevoegen.

Als u een gebruikersaccount wilt maken, neemt u de volgende stappen:

1. Meld u aan bij uw ITRP-tenant.

1. Selecteer boven aan het venster het pictogram **Records:**

    ![Pictogram Records](./media/itrp-tutorial/ic775575.png "Pictogram Records")

1. Selecteer **Personen**in het menu:

    ![Personen selecteren](./media/itrp-tutorial/ic775587.png "Personen selecteren")

1. Selecteer het plusteken (**+**) om een nieuwe persoon toe te voegen:

    ![Het plusteken selecteren](./media/itrp-tutorial/ic775576.png "Het plusteken selecteren")

1. Neem **in** het dialoogvenster Nieuwe persoon toevoegen de volgende stappen uit.

    ![Dialoogvenster Nieuwe persoon toevoegen](./media/itrp-tutorial/ic775577.png "Dialoogvenster Nieuwe persoon toevoegen")

    1. Voer de naam en het e-mailadres in van een geldig Azure AD-account dat u wilt toevoegen.

    1. Selecteer **Opslaan**.

> [!NOTE]
> U elk hulpprogramma voor het maken van gebruikersaccounts of API's van ITRP gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u uw Azure AD-configuratie met eenmalige aanmelding testen met behulp van het Access-paneel.

Wanneer u de ITRP-tegel selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij het ITRP-exemplaar waarvoor u SSO hebt ingesteld. Zie [Apps openen en gebruiken op de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over het toegangspaneel.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
