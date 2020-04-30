---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met F5 | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9c5fb47a-1c5d-437a-b4c1-dbf739eaf5e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 984fd0c7946a50922315269c87e08b1c35b74348
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74074762"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met F5

In deze zelf studie leert u hoe u F5 kunt integreren met Azure Active Directory (Azure AD). Wanneer u F5 integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot F5.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij F5 met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* F5-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* F5 ondersteunt door **SP en IDP** GEÏNITIEERDe SSO
* F5 SSO kan op drie verschillende manieren worden geconfigureerd.

- [Eenmalige aanmelding voor F5 configureren voor de geavanceerde Kerberos-toepassing](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Eenmalige aanmelding voor F5 configureren voor een toepassing op basis van koptekst](headerf5-tutorial.md)

- [Eenmalige aanmelding voor F5 configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>F5 toevoegen vanuit de galerie

Als u de integratie van F5 wilt configureren in azure AD, moet u F5 toevoegen vanuit de galerie naar uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **F5** in het zoekvak.
1. Selecteer **F5** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Eenmalige aanmelding voor Azure AD voor F5 configureren en testen

Azure AD SSO met F5 configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker op F5.

Als u Azure AD SSO met F5 wilt configureren en testen, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[F5-SSO configureren](#configure-f5-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een gebruiker van F5 test](#create-f5-test-user)** : als u een tegen hanger wilt hebben van B. Simon in F5 dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina voor de integratie van de **F5** -toepassing, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://<YourCustomFQDN>.f5.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YourCustomFQDN>.f5.com/`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) van de client om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer op de sectie **F5 instellen** de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan F5.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **F5**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-f5-sso"></a>F5 SSO configureren

- [Eenmalige aanmelding voor F5 configureren voor een toepassing op basis van koptekst](headerf5-tutorial.md)

- [Eenmalige aanmelding voor F5 configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Eenmalige aanmelding voor F5 configureren voor de geavanceerde Kerberos-toepassing

1. Open een nieuw webbrowser venster en meld u aan bij uw bedrijfs site van F5 (Geavanceerd Kerberos) als beheerder en voer de volgende stappen uit:

1. U moet het meta gegevens certificaat importeren in F5 (Advanced Kerberos), dat later wordt gebruikt in het installatie proces. Ga naar **System > Certificate management > Traffic Certificate management >> lijst met SSL-certificaten**. Klik op **importeren** van de rechter bovenhoek.

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Als u de SAML-IDP wilt instellen, gaat u naar **Access > Federation > SAML-Service Provider > > maken op basis van meta gegevens**.

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure02.png)

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure04.png)

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Geef het certificaat op dat uit taak 3 is geüpload

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure06.png)

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure07.png)

 1. Als u de SAML-SP wilt instellen, gaat u naar **Access > federation > SAML service federation > Local SP Services > maken**.

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Klik op **OK**.

1. Selecteer de SP-configuratie en klik op **BIND/BIND IDP connectes**.

     ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Klik op **nieuwe rij toevoegen** en selecteer de **externe IDP-connector** die u in de vorige stap hebt gemaakt.

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Voor het configureren van Kerberos SSO, **toegang > > Kerberos voor eenmalige aanmelding**

    >[!Note]
    >U moet het account voor Kerberos-delegering maken en opgeven. Zie de sectie KCD (Zie de bijlage voor variabelen verwijzingen)

    • Bron van gebruikers naam`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • Bron van gebruikers-realm`session.logon.last.domain`

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure11.png)

1. Voor het configureren van toegangs profielen, **toegang > profiel/beleids regels > toegangs Profiel (per sessie beleid)**.

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure12.png)

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure13.png)

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure14.png)

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure15.png)

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure16.png)
 
    * Session. Logon. Last. usernameUPN expr {[mcget {sessie. SAML. Last. Identity}]}

    * Session. ad. lastactualdomain tekst-superdemo. Live

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName =% {Session. Logon. Last. usernameUPN})

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure18.png)

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure19.png)

    * sessie. Logon. Last. gebruikers naam expr {"[mcget {Session. ad. Last. attr. sAMAccountName}]"}

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {Session. Logon. Last. username}
    * mcget {Session. Logon. Last. password}

1. Voor het toevoegen van een nieuw knoop punt gaat u naar **lokaal verkeer > knooppunten > knooppunt lijst > +**.

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Als u een nieuwe groep wilt maken, gaat u naar **lokaal verkeer > pools > pool lijst > maken**.

     ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure22.png)

 1. Als u een nieuwe virtuele server wilt maken, gaat u naar **lokaal verkeer > Virtuele Servers > virtuele-server lijst > +**.

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure23.png)

1. Geef het toegangs profiel op dat u in de vorige stap hebt gemaakt.

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Kerberos-delegatie instellen 

>[!Note]
>Zie [hier](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf) voor meer informatie

* **Stap 1: een delegerings account maken**

    * Voorbeeld
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **Stap 2: SPN instellen (op het APM delegerings account)**

    *  Voorbeeld
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **Stap 3: SPN-overdracht (voor het App Service-account)**

    * Stel de juiste overdracht voor het account voor de F5-delegering in.
    * In het onderstaande voor beeld wordt APM-delegering account geconfigureerd voor KCD voor FRP-App1. superdemo. live.

        ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure25.png)

1. Geef de [Details op zoals](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html) vermeld in het bovenstaande referentie document

1. Bijlage-overzicht van de volgende IP-adressen van SAML – F5 voor BIGx:

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure26.png)

    ![F5-configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure27.png) 

1. Hieronder ziet u de volledige lijst met standaard-SAML-kenmerken. De OpgegevenNaam wordt weer gegeven met de volgende teken reeks.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| | |
| -- | -- |
| eb46b6b6. Session. SAML. Last. assertionID | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. assertionIssueInstant  | `<ID>` |
| eb46b6b6. Session. SAML. Last. assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6. Session. SAML. Last. attr. name. http:\//schemas.Microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6. Session. SAML. Last. attr. name. http:\//schemas.Microsoft.com/Identity/claims/DisplayName | user0 |
| eb46b6b6. Session. SAML. Last. attr. name. http:\//schemas.Microsoft.com/Identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6. Session. SAML. Last. attr. name. http:\//schemas.Microsoft.com/Identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. attr. name. http:\//schemas.Microsoft.com/Identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. attr. name. http:\//schemas.xmlsoap.org/WS/2005/05/Identity/claims/EmailAddress | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. attr. name. http:\//schemas.xmlsoap.org/WS/2005/05/Identity/claims/givenName | user0 |
| eb46b6b6. Session. SAML. Last. attr. name. http:\//schemas.xmlsoap.org/WS/2005/05/Identity/claims/name | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. attr. name. http:\//schemas.xmlsoap.org/WS/2005/05/Identity/claims/surname | 0 |
| eb46b6b6. Session. SAML. Last. doelgroep | `https://kerbapp.superdemo.live` |
| eb46b6b6. Session. SAML. Last. authNContextClassRef | urn: Oasis: names: TC: SAML: 2.0: AC: classes: password |
| eb46b6b6. Session. SAML. Last. authNInstant | `<ID>` |
| eb46b6b6. Session. SAML. Last. Identity | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. inResponseTo | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. nameIDValue | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. nameIdFormat | urn: Oasis: names: TC: SAML: 1.1: NameID-indeling: emailAddress |
| eb46b6b6. Session. SAML. Last. responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6. Session. SAML. Last. responseId | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. responseIssueInstant | `<ID>` |
| eb46b6b6. Session. SAML. Last. responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6. Session. SAML. Last. Result | 1 |
| eb46b6b6. Session. SAML. Last. samlVersion | 2.0 |
| eb46b6b6. Session. SAML. Last. sessionIndex | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. statusValue | urn: Oasis: names: TC: SAML: 2.0: status: geslaagd |
| eb46b6b6. Session. SAML. Last. subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6. Session. SAML. Last. subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6. Session. SAML. Last. subjectConfirmMethod | urn: Oasis: names: TC: SAML: 2,0: cm: Bearer |
| eb46b6b6. Session. SAML. Last. validityNotBefore | `<ID>` |
| eb46b6b6. Session. SAML. Last. validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>Een F5 test gebruiker maken

In deze sectie maakt u een gebruiker met de naam B. Simon in F5. Werk samen met het [ondersteunings team voor F5-clients](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) om de gebruikers toe te voegen op het F5-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de F5-tegel in het toegangs venster klikt, moet u automatisch worden aangemeld bij de F5 waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer F5 met Azure AD](https://aad.portal.azure.com/)

- [Eenmalige aanmelding voor F5 configureren voor een toepassing op basis van koptekst](headerf5-tutorial.md)

- [Eenmalige aanmelding voor F5 configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

