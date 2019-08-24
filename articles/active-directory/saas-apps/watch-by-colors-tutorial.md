---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met bekijken op kleuren | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en het bekijken van kleuren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 586a029c-fb8d-4233-b280-103b9ba7102d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce0882be0419cbbbc7d94cb8d517e27bdb06a780
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014137"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Zelfstudie: Azure Active Directory SSO-integratie (single sign-on) met bekijken op kleuren

In deze zelf studie leert u hoe u de controle op kleuren integreert met Azure Active Directory (Azure AD). Wanneer u de controle op kleuren integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de kleuren.
* Stel uw gebruikers in staat om automatisch te worden aangemeld om te kijken op kleuren met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Bekijken op kleuren eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Door de kleuren bekijken ondersteunt SSO met **SP en IDP**

## <a name="adding-watch-by-colors-from-the-gallery"></a>Bewakings kleuren toevoegen vanuit de galerie

Als u de integratie van bewakings kleuren wilt configureren in azure AD, moet u in de galerie aan uw lijst met beheerde SaaS-apps controle toevoegen op basis van kleuren.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **toevoegen vanuit de galerie** de tekst **volgen op kleuren** in het zoekvak.
1. Selecteer **bekijken op kleuren** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor controle op kleuren

Azure AD SSO configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in bekijken op kleuren.

Als u Azure AD SSO wilt configureren en testen met bekijken op kleuren, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configureren van controle op kleuren SSO](#configure-watch-by-colors-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. Een **[controle maken op basis van kleuren test gebruiker](#create-watch-by-colors-test-user)** : als u een equivalent van B. Simon wilt bekijken dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **bekijken op kleuren** van de toepassing de sectie **beheren** en selecteer eenmalige **aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **basis configuratie** van SAML is de toepassing vooraf geconfigureerd in de gestarte modus **IDP** en de benodigde url's zijn al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door te klikken op de knop **Opslaan** .

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://app.colorscorporation.com/login`

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op de knop kopiëren om de URL van de **app Federation-meta gegevens** te kopiëren en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** aan de bovenkant van het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de hand van kleuren.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **controleren op kleuren**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-watch-by-colors-sso"></a>Eenmalige aanmelding via de kleuren configureren

1. Open een nieuw webbrowser venster en meld u aan bij de site controle op kleuren bedrijf als beheerder en voer de volgende stappen uit:

1. Klik in de rechter bovenhoek van de pagina op **profiel** > **account instellingen** > **SSO (eenmalige aanmelding)** .

    ![Configuratie bekijken op kleuren](./media/watch-by-colors-tutorial/config01.png)

1. Voer de volgende stappen uit op de pagina **SSO (eenmalige aanmelding)** :

    ![Configuratie bekijken op kleuren](./media/watch-by-colors-tutorial/config02.png)

    a. **Schakel SAML inschakelen** in **op aan**.

    b. Plak in het tekstvak **URL** de URL voor **federatieve meta gegevens**die u van Azure Portal hebt gekopieerd.

    c. Klik op **importeren**. de volgende velden worden automatisch ingevuld op de pagina.

    d. Klik op **Opslaan**.

### <a name="create-watch-by-colors-test-user"></a>Controle maken op basis van kleuren gebruiker

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden om te bekijken op kleuren, moeten ze worden ingericht in een horloge op kleuren. In bekijken op kleuren is inrichting een hand matige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan om te bekijken op kleuren als beveiligings beheerder.

1. Klik in de rechter bovenhoek van de pagina op **profiel** > **gebruikers** > **gebruiker toevoegen**.

    ![Configuratie bekijken op kleuren](./media/watch-by-colors-tutorial/config03.png)

1. Voer op de pagina **gebruikers Details** de volgende stappen uit:

    ![Configuratie bekijken op kleuren](./media/watch-by-colors-tutorial/config04.png)

    a. Voer in het tekstvak **voor naam** de voor naam van de gebruiker in zoals **B**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

    c. Voer in het tekstvak **e-mail** het e-mail adres van `B.Simon@contoso.com`de gebruiker in.

    d. Voer het wacht woord in het tekstvak **wacht** woord in.

    e. Selecteer **account machtigingen** volgens uw organisatie.

    f. Klik op **Opslaan**.

## <a name="test-sso"></a>SSO testen 

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u op de tegel bekijken op kleuren in het toegangs venster klikt, wordt u automatisch aangemeld bij het horloge door de kleuren waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer vertraging met Azure AD](https://aad.portal.azure.com/)

