---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met netdocumenten | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en netdocumenten.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71e5e13485c4a10664d98363e8e99bfd3b4f4bcf
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035702"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Zelfstudie: Azure Active Directory SSO-integratie (single sign-on) met netdocumenten

In deze zelf studie leert u hoe u netdocumenten integreert met Azure Active Directory (Azure AD). Wanneer u netdocumenten integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot netdocumenten.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij netdocumenten met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Netdocumenten eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Netdocumenten ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-netdocuments-from-the-gallery"></a>Netdocumenten toevoegen vanuit de galerie

Als u de integratie van netdocumenten wilt configureren in azure AD, moet u netdocumenten vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **in de galerie toevoegen** **netdocumenten** in het zoekvak.
1. Selecteer **Netdocumenten** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>Eenmalige aanmelding van Azure AD voor netdocumenten configureren en testen

Configureer en test Azure AD SSO met netdocumenten met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in netdocumenten.

Als u Azure AD SSO met netdocumenten wilt configureren en testen, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configureren van de SSO van Netdocumenten](#configure-netdocuments-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. Een gebruiker voor het **[maken van Netdocumenten testen](#create-netdocuments-test-user)** : als u een equivalent van B. Simon wilt hebben in netdocumenten die zijn gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **netdocumenten** voor toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`
    
    c. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `http://netdocuments.com/VAULT`

    > [!NOTE]
    > Dit zijn geen echte waarden. Deze waarden bijwerken met de werkelijke aanmeldings-URL en antwoord-URL. Opslagplaats-ID is een waarde die begint met **CA-** gevolgd door 8 teken code die is gekoppeld aan de opslag plaats van uw netdocument. U kunt het document voor de [federatieve identiteits ondersteuning van het netdocument](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) controleren voor meer informatie. U kunt ook contact opnemen met het [ondersteunings team van Netdocumenten](https://support.netdocuments.com/hc/) om deze waarden op te halen als u problemen hebt met het configureren van de bovenstaande gegevens. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Netdocuments-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. Netdocuments-toepassing verwacht dat **nameidentifier** worden toegewezen aan de werk **nemers** -of andere claim die van toepassing is op uw organisatie als **nameidentifier**, dus u moet de kenmerk toewijzing bewerken door op het pictogram **bewerken** te klikken en wijzig de kenmerk toewijzing.

    ![image](common/edit-attribute.png)

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/metadataxml.png)

1. Kopieer op de sectie **Netdocumenten instellen** de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** aan de bovenkant van het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extension in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang tot netdocumenten te verlenen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Netdocumenten**in de lijst toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-netdocuments-sso"></a>SSO van netdocumenten configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij de bedrijfs site van uw netdocument.

2. Ga naar de **beheerder**.

3. Klik op **gebruikers en groepen toevoegen en verwijderen**.
   
    ![Opslagplaats](./media/netdocuments-tutorial/ic795047.png "opslagplaats")

4. Klik op **geavanceerde verificatie opties configureren**.
    
    ![Geavanceerde verificatie opties configureren](./media/netdocuments-tutorial/ic795048.png "geavanceerde verificatie opties configureren")

5. Voer de volgende stappen uit in het dialoog venster **federatieve identiteit** :
   
    ![Federatieve identiteit](./media/netdocuments-tutorial/ic795049.png "federatieve identiteit")
   
    a. Selecteer **Active Directory Federation Services**als **federatief identiteits server type**.
   
    b. Klik op **bestand kiezen**om het gedownloade meta gegevensbestand te uploaden dat u hebt gedownload van Azure Portal.
   
    c. Klik op **OK**.

### <a name="create-netdocuments-test-user"></a>Test gebruiker voor netdocumenten maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij netdocumenten, moeten ze worden ingericht in netdocumenten.  
In het geval van netdocumenten is inrichting een hand matige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij de bedrijfs site van uw **netdocument** als beheerder.

2. Klik in het menu bovenaan op **Admin**.
   
    ![Admin](./media/netdocuments-tutorial/ic795051.png "Admin")

3. Klik op **gebruikers en groepen toevoegen en verwijderen**.
   
    ![Opslagplaats](./media/netdocuments-tutorial/ic795047.png "opslagplaats")

4. Typ in het tekstvak **e-mail adres** het e-mail adres van een geldig Azure Active Directory account dat u wilt inrichten en klik vervolgens op **gebruiker toevoegen**.
   
    E-(./media/netdocuments-tutorial/ic795053.png "mail adres e") ![-]mail adres
   
    >[!NOTE]
    >De houder van het Azure Active Directory-account krijgt een e-mail bericht met een koppeling om het account te bevestigen voordat het actief wordt. U kunt andere netdocumenten gebruiken hulpprogram ma's voor het maken van gebruikers accounts of Api's die worden geleverd door netdocumenten om Azure Active Directory gebruikers accounts in te richten.

## <a name="test-sso"></a>SSO testen 

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u op de tegel netdocumenten in het toegangs venster klikt, moet u automatisch worden aangemeld bij de netdocumenten waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Gebruik netdocumenten met Azure AD](https://aad.portal.azure.com/)

