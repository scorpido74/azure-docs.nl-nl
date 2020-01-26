---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met het opnieuw afdrukken van Bureau-artikel Galaxy | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en het opnieuw afdrukken van Bureau-artikel Galaxy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 00fd0b0e-8de1-4e64-8a9c-4b65c0e47fe0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d94fc0bc736d2136d4711ab02ae554605deeb35
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76761264"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-reprints-desk---article-galaxy"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met het opnieuw afdrukken van Bureau-artikel Galaxy

In deze zelf studie leert u hoe u het opnieuw afdrukken van Bureau-artikel Galaxy integreert met Azure Active Directory (Azure AD). Wanneer u het opnieuw afdrukken van Bureau-artikel Galaxy integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot het opnieuw afdrukken van Bureau-artikel Galaxy.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld om Bureau-artikel Galaxy opnieuw af te drukken met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Opnieuw afdrukken van Bureau-artikel Galaxy eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Opnieuw afdrukken Bureau-artikel Galaxy ondersteunt **IDP** GEÏNITIEERDe SSO

* Opnieuw afdrukken Bureau-artikel Galaxy ondersteunt **just-in-time** -gebruikers inrichting

* [Als u het artikel Galaxy opnieuw afdrukken hebt geconfigureerd, kunt u sessie besturings elementen afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie besturings elementen worden uitgebreid vanuit voorwaardelijke toegang. Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-reprints-desk---article-galaxy-from-the-gallery"></a>Opnieuw afdrukken Bureau-artikel Galaxy toevoegen vanuit de galerie

Als u de integratie van Bureau-artikel Galaxy in azure AD wilt configureren, moet u het bureau blad van de galerie opnieuw afdrukken met een lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **Bureau-artikel Galaxy opnieuw afdrukken** in het zoekvak.
1. Selecteer **Bureau opnieuw afdrukken-artikel Galaxy** van het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-reprints-desk---article-galaxy"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor het opnieuw afdrukken van Bureau-artikel Galaxy

Azure AD SSO configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor eenmalige aanmelding moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker bij het opnieuw afdrukken van Bureau-artikel Galaxy.

Voer de volgende bouw stenen uit om Azure AD SSO te configureren en te testen met het opnieuw afdrukken van Bureau-artikel Galaxy:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configureren opnieuw afdrukken Bureau artikel Galaxy SSO](#configure-reprints-desk-article-galaxy-sso)** -voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Opnieuw afdrukken Bureau artikel Galaxy test gebruiker](#create-reprints-desk-article-galaxy-test-user)** : om een tegen hanger te hebben van B. Simon in opnieuw afdrukken Bureau-artikel Galaxy dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in de [Azure Portal](https://portal.azure.com/)op de pagina **opnieuw afdrukken Bureau-artikel Galaxy** Application Integration de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **basis configuratie van SAML** is de toepassing vooraf geconfigureerd en de benodigde url's zijn al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door te klikken op de knop **Opslaan** .


1. Opnieuw afdrukken van Bureau-artikel Galaxy toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. In aanvulling op het bovenstaande wordt het Bureau-artikel Galaxy-toepassing opnieuw afgedrukt, maar nog weinig kenmerken om te worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Name | Bronkenmerk|
    | ------------ | --------- |
    | firstname | user.givenname |
    | lastname | user.surname |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer de gewenste URL ('s) op basis van uw vereiste op de sectie **Stel opnieuw afdrukken Bureau-artikel Galaxy** in.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door het verlenen van toegang tot het opnieuw afdrukken van het Bureau artikel Galaxy.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. In de lijst toepassingen selecteert u **Bureau-artikel Galaxy opnieuw afdrukken**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-reprints-desk-article-galaxy-sso"></a>Opnieuw afdrukken Bureau artikel Galaxy SSO configureren

Als u eenmalige aanmelding wilt configureren voor het opnieuw **afdrukken van Bureau-artikel Galaxy** , moet u het gedownloade **XML-bestand met federatieve meta gegevens** en de juiste gekopieerde url's verzenden van Azure Portal om het [Bureau artikel Galaxy-ondersteunings team opnieuw af te drukken](mailto:customersupport@reprintsdesk.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-reprints-desk-article-galaxy-test-user"></a>Opnieuw afdrukken Bureau artikel Galaxy test gebruiker

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt op het opnieuw afdrukken van Bureau-artikel Galaxy. Opnieuw afdrukken Bureau-artikel Galaxy ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker aanwezig is in het opnieuw afdrukken van Bureau-artikel Galaxy, wordt er na verificatie een nieuw item gemaakt.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Bureau-artikel Galaxy opnieuw afdrukken klikt in het toegangs venster, moet u automatisch worden aangemeld bij het Bureau voor het opnieuw afdrukken van artikelen Galaxy waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Bureau-artikel Galaxy opnieuw af te drukken met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Het beveiligen van bureau blad-artikel Galaxy met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
