---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Nintex Promapp | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Nintex Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 581c850801c153996031378cbf470457264cad3d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76984423"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Nintex Promapp

In deze zelf studie leert u hoe u Nintex Promapp integreert met Azure Active Directory (Azure AD). Wanneer u Nintex Promapp integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Nintex Promapp.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Nintex Promapp met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Nintex Promapp-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Nintex Promapp ondersteunt **SP en IDP** GEÏNITIEERDe SSO
* Nintex Promapp ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-nintex-promapp-from-the-gallery"></a>Nintex Promapp toevoegen vanuit de galerie

Als u de integratie van Nintex Promapp wilt configureren in azure AD, moet u Nintex Promapp van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Nintex Promapp** in het zoekvak.
1. Selecteer **Nintex Promapp** in het resultaten paneel en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Nintex Promapp

Azure AD SSO configureren en testen met Nintex Promapp met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Nintex Promapp.

Als u Azure AD SSO wilt configureren en testen met Nintex Promapp, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Nintex PROMAPP SSO configureren](#configure-nintex-promapp-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak Nintex Promapp test User](#create-nintex-promapp-test-user)** -als u een tegen hanger van B. Simon in Nintex Promapp wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Nintex Promapp** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    1. Voer in het vak **id** een URL in dit patroon in:

        | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > Azure AD-integratie met Nintex Promapp is momenteel alleen geconfigureerd voor door de service geïnitieerde authenticatie. (Dat wil zeggen: naar een Nintex Promapp-URL wordt het verificatie proces gestart.) Maar het veld **antwoord-URL** is een verplicht veld.

    1. Voer in het vak **antwoord-URL** een URL in dit patroon in:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Voer in het vak **URL voor aanmelden** een URL in dit patroon in:`https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE]
    > Deze waarden zijn tijdelijke aanduidingen. U moet de daad werkelijke id, de antwoord-URL en de aanmeldings-URL gebruiken. Neem contact op met het [ondersteunings team van Nintex Promapp](https://www.promapp.com/about-us/contact-us/) om de waarden op te halen. U kunt ook verwijzen naar de patronen die worden weer gegeven in het dialoog venster **basis configuratie van SAML** in de Azure Portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de **Nintex Promapp instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Nintex Promapp.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Nintex Promapp**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-nintex-promapp-sso"></a>Nintex Promapp SSO configureren

1. Meld u aan bij uw Nintex Promapp-bedrijfs site als beheerder.

2. Selecteer in het menu boven aan het venster de optie **beheerder**:

    ![Beheerder selecteren][12]

3. Selecteer **configureren**:

    ![Selecteer configureren][13]

4. In het dialoog venster **beveiliging** voert u de volgende stappen uit.

    ![Het dialoog venster beveiliging][14]

    1. Plak de **aanmeldings-URL** die u hebt gekopieerd van de Azure Portal naar het vak **SSO-aanmeld-URL** .

    1. Selecteer **optioneel**in de **modus SSO-** eenmalige aanmelding. Selecteer **Opslaan**.

       > [!NOTE]
       > De optionele modus is alleen voor test doeleinden. Wanneer u tevreden bent met de configuratie, selecteert u **vereist** in de lijst **SSO-eenmalige aanmelding** om ervoor te zorgen dat alle gebruikers worden geverifieerd bij Azure AD.

    1. Open in Klad blok het certificaat dat u in de vorige sectie hebt gedownload. Kopieer de inhoud van het certificaat zonder de eerste regel (**-----begin certificaat-----**) of de laatste regel (**-----End Certificate-----**). Plak de certificaat inhoud in het vak **SSO-x. 509-certificaat** en selecteer vervolgens **Opslaan**.

### <a name="create-nintex-promapp-test-user"></a>Nintex Promapp-test gebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Nintex Promapp. Nintex Promapp ondersteunt just-in-time-gebruikers inrichting, dat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker zich nog niet in Nintex Promapp bevindt, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Nintex Promapp in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Nintex Promapp waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Nintex Promapp met Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png