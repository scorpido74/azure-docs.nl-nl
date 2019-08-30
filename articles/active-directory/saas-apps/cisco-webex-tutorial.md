---
title: 'Zelfstudie: Azure Active Directory SSO-integratie (single sign-on) met Cisco WebEx-vergaderingen | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Cisco Web-vergaderingen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 141d3062f231e198b3587efcdf4ae6e9a1c97ed6
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162668"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Cisco WebEx-vergaderingen

In deze zelf studie leert u hoe u Cisco Web-vergaderingen integreert met Azure Active Directory (Azure AD). Wanneer u Cisco Web-vergaderingen integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Cisco WebEx-vergaderingen.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Cisco Web-vergaderingen met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Cisco Web-vergaderingen met eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Cisco WebEx-vergaderingen ondersteunen SSO **met SP en IDP**

* Cisco WebEx-vergaderingen ondersteunt **just-in-time** gebruikers inrichting

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Cisco WebEx-vergaderingen toevoegen vanuit de galerie

Als u de integratie van Cisco Web-vergaderingen wilt configureren in azure AD, moet u Cisco WebEx-vergaderingen vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Cisco Web-vergaderingen** in het zoekvak.
1. Selecteer **Cisco Web-vergaderingen** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Eenmalige aanmelding van Azure AD voor Cisco WebEx-vergaderingen configureren en testen

Azure AD SSO met Cisco WebEx-vergaderingen configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Cisco WebEx-vergaderingen.

Als u Azure AD SSO wilt configureren en testen met Cisco WebEx-vergaderingen, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Cisco Web-vergaderingen configureren SSO](#configure-cisco-webex-meetings-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Cisco Web-vergaderingen maken test gebruiker](#create-cisco-webex-meetings-test-user)** : als u een equivalent van B. Simon wilt hebben in Cisco WebEx-vergaderingen die zijn gekoppeld aan de Azure AD-representatie van de gebruiker.
3. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina **Cisco WebEx** -toepassings integratie, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Op de pagina **eenmalige aanmelding met SAML instellen** kunt u de toepassing in de gestarte modus **IDP** configureren door het **META gegevensbestand van de service provider** als volgt te uploaden:

    a. Klik op **metagegevensbestand uploaden**.

    b. Klik op **map logo** voor het selecteren van het bestand met metagegevens en klikt u op **uploaden**.

    c. Nadat de upload van het meta gegevensbestand van de service provider is voltooid, worden de waarden voor de **id** en de **antwoord-URL** automatisch ingevuld in de basis- **SAML-configuratie** sectie.

    >[!Note]
    >U ontvangt het meta gegevensbestand van de service provider in het gedeelte **Cisco WebEx-vergaderingen configureren** . dit wordt verderop in de zelf studie beschreven. 

1. Als u de toepassing in de modus door **SP** gestart wilt configureren, voert u de volgende stappen uit:  

    a. Klik in de sectie **basis configuratie van SAML** op het pictogram bewerken/pen.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)
    
    b. Typ in het tekstvak **aanmeldings-URL** de URL met het volgende patroon:` https://<customername>.my.webex.com`

5. Cisco Web-vergaderingen verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster Gebruikerskenmerken te openen.

    ![image](common/edit-attribute.png)

6. Voor de toepassing Cisco Web-vergaderingen worden behalve hierboven echter nog maar enkele kenmerken door gegeven aan het SAML-antwoord. Voer in de sectie gebruikers claims van het dialoog venster gebruikers kenmerken de volgende stappen uit om het SAML-token kenmerk toe te voegen, zoals wordt weer gegeven in de onderstaande tabel: 

    | Name | Bronkenmerk|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   email       | user.mail |
    |   uid    | user.mail |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Selecteer in de lijst **bron kenmerk** de waarde van het kenmerk dat voor die rij in de vervolg keuzelijst wordt weer gegeven.

    f. Klik op **Opslaan**.

4. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/metadataxml.png)

6. Op de sectie **Cisco WebEx-vergaderingen instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Cisco WebEx-vergaderingen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Cisco WebEx-vergaderingen**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

    ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cisco-webex-meetings-sso"></a>SSO van Cisco Web-vergaderingen configureren

1. Ga naar de URL met uw beheerders referenties. `https://<customername>.webex.com/admin`

2. Ga naar **algemene site-instellingen** en navigeer naar **SSO-configuratie**.
 
    ![Eenmalige aanmelding configureren](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. Voer de volgende stappen uit op de pagina **web-beheer** :

    ![Eenmalige aanmelding configureren](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. Selecteer **SAML 2,0** als **Federatie protocol**.

    b. Klik op **SAML meta data link importeren** om het meta gegevensbestand te uploaden dat u hebt gedownload van Azure Portal.

    c. Klik op de knop **exporteren** om het meta gegevensbestand van de service provider te downloaden en te uploaden in het gedeelte **basis configuratie van SAML** op Azure Portal.

    d. Typ`urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` in het tekstvak **AuthContextClassRef** en als u de MFA wilt inschakelen met behulp van Azure AD, typt u de twee waarden zoals`urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Selecteer **automatisch accounts maken**.

    >[!NOTE]
    >Voor het inschakelen van **just-in-time** -gebruikers inrichting moet u het **automatisch maken**van het account controleren. Naast de SAML-token kenmerken moet worden door gegeven in het SAML-antwoord.

    f. Klik op **Opslaan**.

    >[!NOTE]
    >Deze configuratie is alleen voor de klanten die gebruikmaken van WebEx-gebruikers-id in e-mail indeling.

### <a name="create-cisco-webex-meetings-test-user"></a>Cisco Web-vergaderingen maken test gebruiker

Het doel van deze sectie is het maken van een gebruiker met de naam B. Simon in Cisco WebEx-vergaderingen. Cisco Web-vergaderingen bieden ondersteuning voor **just-in-time** -inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Cisco Web-vergaderingen, wordt er een nieuwe gemaakt wanneer u toegang probeert te krijgen tot Cisco WebEx-vergaderingen.

## <a name="test-sso"></a>SSO testen 

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u klikt op de tegel Cisco WebEx-vergaderingen in het toegangs venster, moet u automatisch worden aangemeld bij de Cisco WebEx-vergaderingen waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer ServiceNow met Azure AD](https://aad.portal.azure.com)
