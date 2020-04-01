---
title: 'Zelfstudie: Azure Active Directory-integratie met Way We Do | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Way We Do.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67310410"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Zelfstudie: Manier van werken integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Way We Do integreert met Azure Active Directory (Azure AD). Wanneer u Way We Do integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Way We Do.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Way We Do met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Als je geen abonnement hebt, kun je [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proefperiode van een maand krijgen.
* Way We Do single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Way We Do ondersteunt **SP** geïnitieerde SSO
* Way We Do ondersteunt **Just In Time** gebruikersinrichting

## <a name="adding-way-we-do-from-the-gallery"></a>Manier toevoegen die wij van de galerij doen

Als u de integratie van Way We Do in Azure AD wilt configureren, moet u Way We Do vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Way We Do** in het zoekvak in de sectie Toevoegen in de sectie Toevoegen in de **galerie.**
1. Selecteer **Manier die we doen** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker op de manier waarop we dat doen.

Als u Azure AD SSO wilt configureren en testen met Way We Do, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Manier configureren We Doen SSO](#configure-way-we-do-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Create Way We Do test gebruiker](#create-way-we-do-test-user)** - om een tegenhanger van Britta Simon in way We Do die is gekoppeld aan de Azure AD vertegenwoordiging van de gebruiker.
6. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Way We Do-toepassingsintegratie** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Contact [Way We Do Client support team](mailto:support@waywedo.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificateraw.png)

1. Kopieer in de sectie **Manier instellen we doen** de juiste URL(s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Manier configureren waarop we SSO doen

1. Als u de configuratie wilt automatiseren binnen Way We Do, moet u **de beveiligingsextensie Mijn apps secure aanmelden** installeren door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

1. Na het toevoegen van extensie aan de browser, klik op **Setup Way We Do** zal u direct naar de Manier Waarop We doen applicatie. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Way We Do. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-6.

    ![Configuratie instellen](common/setup-sso.png)

1. Als u Way We Do handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich aan bij uw Way We Do-bedrijfssite als beheerder en voert u de volgende stappen uit:

1. Klik op het **persoonspictogram** in de rechterbovenhoek van een pagina in Way We Do En klik vervolgens op **Account** in het vervolgkeuzemenu.

    ![Manier waarop we doen account](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Klik op het **menupictogram** om het navigatiemenu van de push te openen en klik op **Eén teken aan**.

    ![Way We Do single](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Voer **op** de installatiepagina Eenmalig aanmelden de volgende stappen uit:

    ![Manier waarop we redden](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Klik **op** de schakelaar Voor eenmalig aanmelden inschakelen op **Ja** om eenmalig aanmelden in te schakelen.

    b. Voer in het tekstvak Voor naam **met één aanmeldingsnaam** uw naam in.

    c. Plak in het tekstvak **Entiteit-id** de waarde van **Azure AD-id**, die u hebt gekopieerd van de Azure-portal.

    d. Plak in het tekstvak **SAML SSO URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    e. Upload het certificaat door op de knop **selecteren** naast **Certificaat**te klikken .

    f. **Optionele instellingen** -
    
    * Wachtwoorden inschakelen - Wanneer deze optie is uitgeschakeld, wordt het reguliere wachtwoord functies voor Way We Do, zodat gebruikers alleen eenmalige aanmelding kunnen gebruiken.

    * Automatisch inrichten inschakelen - Wanneer dit is ingeschakeld, wordt het e-mailadres dat wordt gebruikt om u aan te melden automatisch vergeleken met de lijst met gebruikers in Way We Do. Als het e-mailadres niet overeenkomt met een actieve gebruiker in Way We Do, wordt automatisch een nieuw gebruikersaccount toegevoegd voor de persoon die zich aanmeldt, waarbij ontbrekende informatie wordt opgevraagd.

      > [!NOTE]
      > Gebruikers die via eenmalige aanmelding zijn toegevoegd, worden toegevoegd als algemene gebruikers en krijgen geen rol toegewezen in het systeem. Een beheerder kan zijn beveiligingsrol als editor of beheerder wijzigen en kan ook een of meerdere orgchart-rollen toewijzen.

    g. Klik **op Opslaan** om uw instellingen voort te houden.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Way We Do.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer In de lijst met toepassingen de optie **Manier waarop we doen**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-way-we-do-test-user"></a>Maak manier waarop we doen test gebruiker

In deze sectie, een gebruiker genaamd Britta Simon is gemaakt in Way We Do. Way We Do ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Way We Do, wordt er een nieuwe gemaakt na verificatie.

> [!Note]
> Als u handmatig een gebruiker wilt maken, neemt u contact op met [het ondersteuningsteam van Way We Do Client.](mailto:support@waywedo.com)

### <a name="test-sso"></a>Test SSO

Wanneer u de tegel Way We Do selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de manier waarop u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)