---
title: 'Zelf studie: integratie Azure Active Directory met E Sales Manager Remix | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en E Sales Manager Remix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 167d682bb5ce1266ba6b677f6223c38dd34af9bb
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82202393"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Azure Active Directory integreren met E Sales Manager Remix

In deze zelf studie leert u hoe u Azure Active Directory (Azure AD) integreert met E Sales Manager Remix.

Door Azure AD te integreren met E Sales Manager Remix, profiteert u van de volgende voor delen:

- U kunt beheren in azure AD die toegang heeft tot E Sales Manager Remix.
- U kunt uw gebruikers in staat stellen om automatisch te worden aangemeld bij E Sales Manager Remix (eenmalige aanmelding of SSO) met hun Azure AD-accounts.
- U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met E Sales Manager Remix wilt configureren, hebt u de volgende items nodig:

- Een Azure AD-abonnement
- Een E-mail abonnement voor Remix SSO van de Sales Manager

> [!NOTE]
> Wanneer u de stappen in deze zelf studie test, wordt u aangeraden *geen* productie omgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u niet beschikt over een evaluatieomgeving in Azure AD, kunt u [een gratis proefversie van één maand krijgen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeschrijving
In deze zelf studie test u eenmalige aanmelding voor Azure AD in een test omgeving. 

Het scenario dat in deze zelf studie wordt beschreven, bestaat uit twee belang rijke bouw stenen:

* E Sales Manager Remix toevoegen vanuit de galerie
* Eenmalige aanmelding voor Azure AD configureren en testen

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>E Sales Manager Remix toevoegen vanuit de galerie
Als u de integratie van Azure AD wilt configureren met E Sales Manager Remix, voegt u E Sales Manager Remix van de galerie toe aan uw lijst met beheerde SaaS-apps door het volgende te doen:

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**. 

    ![De knop Azure Active Directory][1]

1. Selecteer**alle toepassingen**in **bedrijfs toepassingen** > .

    ![Het venster bedrijfs toepassingen][2]
    
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **nieuwe toepassing** boven aan het venster.

    ![De knop Nieuwe toepassing][3]

1. Typ in het zoekvak **e Sales Manager Remix**, selecteer **e Sales Manager Remix** in de lijst met resultaten en selecteer vervolgens **toevoegen**.

    ![E Sales Manager Remix in de lijst met resultaten](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met E Sales Manager Remix, op basis van een test gebruiker met de naam ' Julia Simon '.

Voor gebruik van eenmalige aanmelding moet Azure AD de Remix-gebruiker van de verkoop Manager en de bijbehorende tegen hanger identificeren in azure AD. Met andere woorden, een koppelings relatie tussen een Azure AD-gebruiker en dezelfde gebruiker in E Sales Manager Remix moet tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met E Sales Manager Remix, voltooit u de bouw stenen in de volgende vijf secties:

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

Schakel eenmalige aanmelding voor Azure AD in het Azure Portal en configureer eenmalige aanmelding in uw E Sales Manager Remix-toepassing door het volgende te doen:

1. Selecteer in de Azure Portal op de pagina **E-Remix van verkoop Manager** de optie **eenmalige aanmelding**.

    ![De koppeling eenmalige aanmelding][4]

1. Selecteer op **SAML gebaseerde aanmelding**in **het venster eenmalige aanmelding in** het vak **modus voor eenmalige** aanmelding.
 
    ![Het venster eenmalige aanmelding](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. Ga als volgt te werk onder **E Remix domein en url's van verkoop Manager**:

    ![E-Remix-domein en Url's voor de eenmalige aanmelding van de verkoop Manager](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Typ in het vak **AANMELD URL** een URL in de volgende indeling: *https://\<Server-based-URL>/\<subdomein>/esales-PC*.

    b. Typ in het vak **id** een URL in de volgende indeling: *\<https://Server-based-URL>/\<subdomein>/*.

    c. Noteer de **id** -waarde voor later gebruik in deze zelf studie.
    
    > [!NOTE] 
    > De bovenstaande waarden zijn niet echt. Werk deze bij met de werkelijke aanmeldings-URL en id. Neem contact op met [E Sales Manager Remix client ondersteunings team](mailto:esupport@softbrain.co.jp)om de waarden te verkrijgen.

1. Onder **SAML-handtekening certificaat**selecteert u **certificaat (base64)** en vervolgens slaat u het certificaat bestand op uw computer op.

    ![De download koppeling voor het certificaat (base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Schakel het selectie vakje **alle andere gebruikers kenmerken weer geven en bewerken** in en selecteer vervolgens het kenmerk **EmailAddress** .
    
    ![Het venster gebruikers kenmerken](./media/esalesmanagerremix-tutorial/configure1.png)

    Het venster **kenmerk bewerken** wordt geopend.

1. Kopieer de waarden van de **naam ruimte** en de **naam** . Genereer de waarde in de patroon * \<naam ruimte\<>/naam>* en sla deze op voor later gebruik in deze zelf studie.

    ![Het venster kenmerk bewerken](./media/esalesmanagerremix-tutorial/configure2.png)

1. Selecteer onder **e configuratie van de Sales Manager-Remix**de optie **E Sales Manager Remix configureren**.

    ![E Remix-configuratie voor verkoop Manager](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Het venster **aanmelden configureren** wordt geopend.

1. Kopieer in het gedeelte **snelle verwijzing** de afmeldings-URL en de URL voor de SSO-service voor eenmalige aanmelding.

1. Selecteer **Opslaan**.

    ![De knop Opslaan](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Meld u aan bij de Remix-toepassing van uw E-Sales Manager als beheerder.

1. Selecteer rechtsboven **in het menu Administrator**.

    ![De opdracht ' aan beheerder menu '](./media/esalesmanagerremix-tutorial/configure4.png)

1. Selecteer in het linkerdeel venster de optie **systeem instellingen** > **samen werking met het externe systeem**.

    ![De koppelingen systeem instellingen en samen werking met extern systeem](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. Selecteer in het venster **samen werking met extern systeem** de optie **SAML**.

    ![Het venster ' samen werking met extern systeem '](./media/esalesmanagerremix-tutorial/configure6.png)

1. Onder **SAML-verificatie-instelling**gaat u als volgt te werk:

    ![De sectie SAML-verificatie-instelling](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Schakel het selectie vakje **PC-versie** in.
    
    b. Selecteer in de vervolg keuzelijst van het onderdeel **samen werking** de optie **e-mail**.

    c. Plak in het vak **samenwerkings item** de claim waarde die u eerder hebt gekopieerd uit de Azure Portal (dat wil zeggen **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`**).

    d. Plak in het vak **verlener (Entiteits-ID)** de id-waarde die u eerder hebt gekopieerd uit de sectie **E-Remix domein en Url's** van de e-mail van de Azure Portal.

    e. Selecteer voor het uploaden van uw gedownloade certificaat van de Azure Portal **bestands selectie**.

    f. Plak in het vak **ID-provider aanmeldings-URL** de service-URL voor eenmalige SAML-aanmelding die u eerder hebt gekopieerd in de Azure Portal.

    g. Plak in het vak **Afmeldings-URL van de identiteits provider** de waarde voor de afmeldings-URL die u eerder hebt gekopieerd in de Azure Portal.

    h. Selecteer **instelling voltooid**.

> [!TIP]
> Wanneer u de app instelt, kunt u een beknopte versie van de voor gaande instructies in de [Azure Portal](https://portal.azure.com)lezen. Nadat u de app hebt toegevoegd in de sectie **Active Directory** > **Enter prise-toepassingen** , selecteert u het tabblad **eenmalige aanmelding** en opent u de Inge sloten documentatie in de sectie **configuratie** onderaan. Zie voor meer informatie over de Inge sloten documentatie functie [Azure AD embedded-documentatie]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker Julia Simon in de Azure Portal door het volgende te doen:

![Een Azure AD-testgebruiker maken][100]

1. Selecteer **Azure Active Directory**in het linkerdeel venster van de Azure Portal.

    ![De Azure Active Directory koppeling](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Als u een lijst met huidige gebruikers wilt weer geven, selecteert u **gebruikers en groepen** > **alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Selecteer aan de bovenkant van het venster **alle gebruikers** de optie **toevoegen**.

    ![De knop toevoegen](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Het venster **gebruiker** wordt geopend.

1. Ga als volgt te werk in het venster **gebruiker** :

    ![Het venster gebruiker](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Typ **BrittaSimon**in het vak **naam** .

    b. Typ in het vak **gebruikers naam** het e-mail adres van de gebruiker Julia Simon.

    c. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .

    d. Selecteer **Maken**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Een E-Remix test gebruiker voor de verkoop Manager maken

1. Meld u aan bij de Remix-toepassing van uw E-Sales Manager als beheerder.

1. Selecteer in het menu aan de rechter kant **naar het menu beheerder** .

    ![E Remix-configuratie voor verkoop Manager](./media/esalesmanagerremix-tutorial/configure4.png)

1. Selecteer **de instellingen** > van het bedrijf**onderhoud van afdelingen en werk nemers**en selecteer vervolgens **werk nemers geregistreerd**.

    ![Het tabblad "geregistreerde mede werkers"](./media/esalesmanagerremix-tutorial/user1.png)

1. Voer de volgende handelingen uit in de sectie **nieuwe registratie van werk nemers** :
    
    ![De sectie ' nieuwe werknemers registratie '](./media/esalesmanagerremix-tutorial/user2.png)

    a. Typ in het vak **naam van werk nemer** de naam van de gebruiker (bijvoorbeeld **Julia**).

    b. Vul de resterende vereiste velden in.
    
    c. Als u SAML inschakelt, kan de beheerder zich niet aanmelden vanaf de aanmeldings pagina. Ken beheerders machtigingen voor de gebruiker toe door het selectie vakje **beheerder aanmelden** in te scha kelen.

    d. Selecteer **registratie**.

1. Als u zich in de toekomst wilt aanmelden als beheerder, meldt u zich aan als de gebruiker met beheerders machtigingen en selecteert u in de rechter bovenhoek het **menu beheerder**.

    ![De opdracht ' aan beheerder menu '](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u gebruiker Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot E Sales Manager Remix. Ga hiervoor als volgt te werk: 

![De gebruikersrol toewijzen][200] 

1. Open in de Azure Portal de weer gave **toepassingen** , ga **naar de mapweergave** en selecteer vervolgens**alle toepassingen**in **bedrijfs toepassingen** > .

    ![De koppelingen bedrijfs toepassingen en alle toepassingen][201] 

1. Selecteer in de lijst **toepassingen** de optie **E Sales Manager Remix**.

    ![De E-Remix-koppeling voor de verkoop Manager](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. Selecteer in het linkerdeel venster **gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen][202]

1. Selecteer **toevoegen** en selecteer vervolgens in het deel venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![Het deelvenster Toewijzing toevoegen][203]

1. Selecteer in het venster **gebruikers en groepen** in de lijst **gebruikers** de optie **Julia Simon**.

1. Selecteer de knop **selecteren** .

1. Selecteer in het venster **toewijzing toevoegen** de optie **toewijzen**.
    
### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel E Sales Manager Remix selecteert in het toegangs venster, moet u zich automatisch aanmelden bij uw E-Remix-toepassing voor verkoop Manager.

Zie [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster. 

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelf studies over het integreren van SaaS-apps met Azure Active Directory](tutorial-list.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png

