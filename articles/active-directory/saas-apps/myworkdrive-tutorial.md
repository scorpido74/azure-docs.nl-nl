---
title: 'Zelf studie: integratie Azure Active Directory met MyWorkDrive | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.openlocfilehash: c733873a5545bb01f03abd8178ed2f371e2dd3aa
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074073"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Zelf studie: MyWorkDrive integreren met Azure Active Directory

In deze zelf studie leert u hoe u MyWorkDrive integreert met Azure Active Directory (Azure AD). Wanneer u MyWorkDrive integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot MyWorkDrive.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij MyWorkDrive met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proef versie van één maand ontvangen.
* MyWorkDrive-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. MyWorkDrive ondersteunt SSO die door **SP** en **IDP** is geïnitieerd

## <a name="adding-myworkdrive-from-the-gallery"></a>MyWorkDrive toevoegen uit de galerie

Als u de integratie van MyWorkDrive in azure AD wilt configureren, moet u MyWorkDrive uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **MyWorkDrive** in het zoekvak.
1. Selecteer **MyWorkDrive** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test Azure AD SSO met MyWorkDrive met behulp van een test gebruiker met de naam **Julia Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in MyWorkDrive.

Als u Azure AD SSO wilt configureren en testen met MyWorkDrive, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[MYWORKDRIVE SSO configureren](#configure-myworkdrive-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een MyWorkDrive-test gebruiker](#create-myworkdrive-test-user)** -om een equivalent van Julia Simon in MyWorkDrive te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **MyWorkDrive** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u op de pagina **basis configuratie van SAML** de waarden voor het volgende veld in:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Voer de hostnaam van de MyWorkDrive-server van uw bedrijf in: bijvoorbeeld
    > 
    > Antwoord-URL: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Aanmeldings-URL:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Neem contact op met het [ondersteunings team van MyWorkDrive](mailto:support@myworkdrive.com) als u niet zeker weet hoe u uw eigen HOSTNAAM en SSL-certificaat voor deze waarden kunt instellen.

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op de knop kopiëren om de URL voor de **federatieve meta gegevens** van de app naar het klem bord te kopiëren.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>MyWorkDrive SSO configureren

1. Als u de configuratie wilt automatiseren in MyWorkDrive, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup MyWorkDrive** gaat u naar de MyWorkDrive-toepassing. Geef de beheerders referenties op om u aan te melden bij MyWorkDrive. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-4 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

1. Als u MyWorkDrive hand matig wilt instellen in een ander browser venster, meldt u zich aan bij MyWorkDrive als een beveiligings beheerder.

1. Op de MyWorkDrive-server in het deel venster beheer klikt u op **Enter prise** en voert u de volgende stappen uit:

    ![De Beheerder](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Schakel **SAML/ADFS SSO**in.

    b. **SAML-Azure AD** selecteren

    c. Plak in het tekstvak **Azure-app URL voor federatieve meta gegevens** de waarde van de URL voor de **app federatieve meta gegevens** die u hebt gekopieerd uit de Azure Portal.

    d. Klik op **Opslaan**.

    > [!NOTE]
    > Raadpleeg het [artikel MyWorkDrive Azure AD-ondersteuning](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/)voor meer informatie.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam Julia Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `Britta Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan MyWorkDrive.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **MyWorkDrive**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-myworkdrive-test-user"></a>MyWorkDrive-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in MyWorkDrive. Werk samen met [MyWorkDrive-ondersteunings team](mailto:support@myworkdrive.com) om de gebruikers toe te voegen in het MyWorkDrive-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel MyWorkDrive in het toegangs venster selecteert, wordt u automatisch aangemeld bij de MyWorkDrive waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)