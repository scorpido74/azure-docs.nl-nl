---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Evernote | Microsoft Docs'
description: Lees hoe u eenmalige aanmelding configureert tussen Microsoft Azure Active Directory en Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55216e9eb4bd5cda2635564480751459f05e8309
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121623"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Zelfstudie: Azure Active Directory SSO-integratie (single sign-on) met Evernote

In deze zelf studie leert u hoe u Evernote integreert met Azure Active Directory (Azure AD). Wanneer u Evernote integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Evernote.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Evernote met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Eenmalige eenmalige aanmelding (SSO) met ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Evernote ondersteunt door **SP en IDP** GEÏNITIEERDe SSO

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-evernote-from-the-gallery"></a>Evernote toevoegen vanuit de galerie

Om de configuratie van Evernote in Microsoft Azure Active Directory te configureren, moet u Evernote vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Evernote** in het zoekvak.
1. Selecteer **Evernote** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Eenmalige aanmelding van Azure AD voor Evernote configureren en testen

Azure AD SSO met Evernote configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Evernote.

Als u Azure AD SSO wilt configureren en testen met Evernote, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Evernote SSO configureren](#configure-evernote-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Voeg een Evernote-test gebruiker](#create-evernote-test-user)** toe, zodat deze een soort tegen komt van B. Simon in Evernote dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina **Evernote** -toepassings integratie en selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    In het tekstvak **Id** typt u een URL: `https://www.evernote.com/saml2`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://www.evernote.com/Login.action`

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/certificatebase64.png)

7. Als u de opties voor **ondertekening** wilt wijzigen, klikt u op de knop **bewerken** om het dialoog venster **SAML-handtekening certificaat** te openen.

    ![image](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Selecteer de optie **SAML-respons ondertekenen en** de optie bevestiging voor **ondertekening**.

    b. Klik op **Opslaan**

1. Op de sectie **Evernote instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Evernote.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Evernote** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-evernote-sso"></a>Evernote SSO configureren

1. Als u de configuratie binnen Evernote wilt automatiseren, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie hebt toegevoegd aan de browser, klikt u op het **installatie programma Evernote** stuurt u naar de Evernote-toepassing. Geef de beheerders referenties op om u aan te melden bij Evernote. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-6 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u Evernote hand matig wilt installeren, opent u een nieuw webbrowser venster en meldt u zich aan bij uw Evernote-bedrijfs site als beheerder en voert u de volgende stappen uit:

4. Ga naar **'Admin Console'**

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. Ga vanuit de **'Admin Console'** naar **‘Security’** en selecteer **‘Single Sign-On’**

    ![SSO-instelling](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Configureer de volgende waarden:

    ![Certificaatinstelling](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Enable SSO:** SSO is standaard ingeschakeld (klik op **Disable Single Sign-on** om het SSO vereiste te verwijderen)

    b. Plak de waarde van de **aanmeldings-URL**, die u uit de Microsoft Azure-portal naar het tekstvak **SAML HTTP Request URL** hebt gekopieerd.

    c. Open het gedownloade certificaat van Microsoft Azure Active Directory in Kladblok en kopieer de inhoud, inclusief "BEGIN CERTIFICATE" en "END CERTIFICATE" en plak deze in het tekstvak **X.509 Certificate**. 

    d. Klik op **Save Changes**

### <a name="create-evernote-test-user"></a>Evernote-testgebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij Evernote, moeten ze worden ingericht in Evernote.  
In het geval van Evernote is inrichten een handmatige taak.

**Voer de volgende stappen uit om een gebruikersaccount in te richten:**

1. Meld u als beheerder aan bij uw Evernote-bedrijfs site.

2. Klik op de **'Admin Console'** .

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Ga vanuit de **'Admin Console'** naar **‘Add users’** .

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Voeg teamleden toe** in het tekstvak **Email**, typ het e-mailadres van het gebruikersaccount en klik op **Invite.**

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Nadat de uitnodiging is verzonden, ontvangt de houder van het Microsoft Azure Active Directory-account een e-mail om de uitnodiging te accepteren.

## <a name="test-sso"></a>SSO testen 

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Evernote klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Evernote waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Evernote met Azure AD](https://aad.portal.azure.com/)

