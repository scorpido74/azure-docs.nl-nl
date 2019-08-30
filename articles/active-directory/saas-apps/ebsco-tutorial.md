---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met EBSCO | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ebaf3a1d877025cafe8829bc937ef032a3c95d03
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163460"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met EBSCO

In deze zelf studie leert u hoe u EBSCO integreert met Azure Active Directory (Azure AD). Wanneer u EBSCO integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot EBSCO.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij EBSCO met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* EBSCO-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* EBSCO ondersteunt SSO die door **SP en IDP** is geïnitieerd
* EBSCO ondersteunt **just-in-time** -gebruikers inrichting

> [!NOTE]
> Omdat de id van deze toepassing een vaste teken reeks waarde is, kan slechts één exemplaar in één Tenant worden geconfigureerd.

## <a name="adding-ebsco-from-the-gallery"></a>EBSCO toevoegen uit de galerie

Als u de integratie van EBSCO in azure AD wilt configureren, moet u EBSCO uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **EBSCO** in het zoekvak.
1. Selecteer **EBSCO** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor EBSCO

Azure AD SSO met EBSCO configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in EBSCO.

Als u Azure AD SSO wilt configureren en testen met EBSCO, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[EBSCO SSO configureren](#configure-ebsco-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een EBSCO-test gebruiker](#create-ebsco-test-user)** -om een equivalent van B. Simon in EBSCO te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **EBSCO** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    In het tekstvak **Id** typt u een URL: `pingsso.ebscohost.com`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het ondersteunings [team van EBSCO-clients](mailto:support@ebsco.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    o **unieke elementen:**  

    o **CustId** = unieke EBSCO-klant-id invoeren 

    o **profile** = clients kunnen de koppeling aanpassen om gebruikers naar een specifiek profiel te leiden (afhankelijk van wat ze aanschaffen bij EBSCO). Ze kunnen een specifieke profiel-ID invoeren. De belangrijkste Id's zijn uitgavenverdeelstaten (EBSCO Discovery Service) en ehost (EBSOCOhost-data bases). [Hier](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)vindt u instructies.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/metadataxml.png)

1. Op de sectie **EBSCO instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan EBSCO.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **EBSCO**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-ebsco-sso"></a>EBSCO SSO configureren

Als u eenmalige aanmelding wilt configureren op **EBSCO** , moet u het gedownloade **XML-bestand met federatieve meta gegevens** en de juiste gekopieerde url's verzenden van Azure Portal naar [EBSCO ondersteunings team](mailto:support@ebsco.com). Ze stelt u deze optie om de SAML SSO-verbinding instellen goed aan beide zijden.

### <a name="create-ebsco-test-user"></a>EBSCO-test gebruiker maken

In het geval van EBSCO wordt het inrichten van de gebruiker automatisch.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

Azure AD geeft de vereiste gegevens door aan de EBSCO-toepassing. De gebruikers inrichting van EBSCO kan automatisch of een eenmalig formulier vereisen. Dit is afhankelijk van het feit of de client over een groot aantal reeds bestaande EBSCOhost-accounts met persoonlijke instellingen is opgeslagen. Dit kan tijdens de implementatie worden besproken met het EBSCO-ondersteunings [team](mailto:support@ebsco.com) . In beide gevallen hoeft de client geen EBSCOhost-accounts te maken voordat ze worden getest.

   >[!Note]
   >U kunt EBSCOhost gebruikers inrichten/personalisatie automatiseren. Neem contact op met het ondersteunings [team van EBSCO](mailto:support@ebsco.com) over just-in-time-gebruikers inrichting.

## <a name="test-sso"></a>SSO testen

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

1. Wanneer u op de tegel EBSCO in het toegangs venster klikt, wordt u automatisch aangemeld bij uw EBSCO-toepassing.
Zie [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

1. Zodra u zich aanmeldt bij de toepassing, klikt u op de knop **Aanmelden** in de rechter bovenhoek.

    ![De EBSCO-aanmelding in de lijst met toepassingen](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. U ontvangt een eenmalige prompt om de institutionele/SAML-aanmelding te koppelen met een **koppeling naar uw bestaande MyEBSCOhost-account aan uw instellings account** , maar u kunt nu een **Nieuw MyEBSCOhost-account maken en dit koppelen aan uw instellings account**. Het account wordt gebruikt voor personalisatie op de EBSCOhost-toepassing. Selecteer de optie **een nieuw account maken** en u ziet dat het formulier voor personalisatie vooraf is voltooid met de waarden van het SAML-antwoord, zoals wordt weer gegeven in de onderstaande scherm afbeelding. Klik op **door gaan** om deze selectie op te slaan.
    
     ![De EBSCO-gebruiker in de lijst met toepassingen](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Nadat u de bovenstaande installatie hebt voltooid, wist u cookies/cache en meldt u opnieuw. U hoeft zich niet opnieuw hand matig aan te melden en de instellingen voor personalisatie worden onthouden.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer EBSCO met Azure AD](https://aad.portal.azure.com/)

