---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met F5 | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74074762"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met F5

In deze zelfstudie leert u hoe u F5 integreert met Azure Active Directory (Azure AD). Wanneer u F5 integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot F5.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij F5 met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* F5 single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* F5 ondersteunt **SP en IDP** gestart met SSO
* F5 SSO kan op drie verschillende manieren worden geconfigureerd.

- [F5-aanmelding configureren voor geavanceerde Kerberos-toepassing](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [F5-aanmelding configureren voor toepassing op basis van kopteksten](headerf5-tutorial.md)

- [F5-aanmelding configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>F5 toevoegen vanuit de galerie

Als u de integratie van F5 in Azure AD wilt configureren, moet u F5 uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **F5** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **F5** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Azure AD-aanmelding voor F5 configureren en testen

Azure AD SSO configureren en testen met F5 met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in F5.

Als u Azure AD SSO met F5 wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer F5-SSO](#configure-f5-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[F5-testgebruiker maken](#create-f5-test-user)** - om een tegenhanger van B.Simon in F5 te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **F5-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<YourCustomFQDN>.f5.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YourCustomFQDN>.f5.com/`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [f5 client support team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **F5 instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot F5.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **F5**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-f5-sso"></a>F5 SSO configureren

- [F5-aanmelding configureren voor toepassing op basis van kopteksten](headerf5-tutorial.md)

- [F5-aanmelding configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>F5-aanmelding configureren voor geavanceerde Kerberos-toepassing

1. Open een nieuw webbrowservenster en meld u aan bij uw F5-bedrijfssite (Advanced Kerberos) als beheerder en voer de volgende stappen uit:

1. U moet het metagegevenscertificaat importeren in de F5 (Advanced Kerberos) die later in het installatieproces worden gebruikt. Ga naar **System > Certificate Management > Traffic Certificate Management >> SSL-certificaatlijst.** Klik op **Importeren** in de rechterhoek.

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Als u de SAML IDP wilt instellen, gaat u naar **Access > Federation > SAML-serviceprovider > > maken met metagegevens.**

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure02.png)

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure04.png)

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Het certificaat opgeven dat is geüpload vanuit taak 3

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure06.png)

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure07.png)

 1. Als u de SAML SP wilt instellen, gaat u naar **Access > Federation > SAML-servicefederatie > lokale SP-services > maken**.

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Klik op **OK**.

1. Selecteer de SP-configuratie en klik op **IdP-connectors binden/binden**.

     ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Klik op **Nieuwe rij toevoegen** en selecteer de externe **IdP-connector** die in de vorige stap is gemaakt.

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Voor het configureren van Kerberos SSO, **Toegang > single sign-on > Kerberos**

    >[!Note]
    >Je hebt het Kerberos-delegatieaccount nodig om te worden gemaakt en opgegeven. Verwijs KCD-sectie ( Verwijs bijlage voor variabele verwijzingen)

    • Gebruikersnaam Bron`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • User Realm Bron`session.logon.last.domain`

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure11.png)

1. Voor het configureren van toegangsprofiel, **toegang > profiel/beleid > Toegangsprofiel (per sessiebeleid)**.

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure12.png)

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure13.png)

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure14.png)

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure15.png)

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logon.last.usernameUPN expr {[mcget {session.saml.last.identity}]}

    * session.ad.lastactualdomain TEXT superdemo.live

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logon.last.usernameUPN})

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure18.png)

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logon.last.username expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.logon.last.username}
    * mcget {session.logon.last.password}

1. Ga voor het toevoegen van een nieuw knooppunt naar > > naar **Lokale > knooppunten > knooppuntlijst +**.

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Als u een nieuwe groep wilt maken, gaat u naar **Lokaal verkeer > groepen > groeplijst > maken**.

     ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure22.png)

 1. Als u een nieuwe virtuele server wilt maken, gaat u **naar Lokaal verkeer > virtuele servers > virtuele serverlijst > +**.

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure23.png)

1. Geef het toegangsprofiel op dat in vorige stap is gemaakt.

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Kerberos-delegatie instellen 

>[!Note]
>Voor meer informatie verwijzen wij [hier](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

* **Stap 1: Een delegatieaccount maken**

    * Voorbeeld
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **Stap 2: SPN instellen (op het APM-delegatieaccount)**

    *  Voorbeeld
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **Stap 3: SPN-delegatie (voor het App-serviceaccount)**

    * Stel de juiste delegatie in voor het F5-delegatieaccount.
    * In het onderstaande voorbeeld wordt het APM-delegatieaccount geconfigureerd voor KCD voor FRP-App1.superdemo.live-app.

        ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure25.png)

1. Geef de details zoals vermeld in het bovenstaande referentiedocument onder [deze](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

1. Aanhangsel- SAML – F5 BIG-IP Variabele toewijzingen hieronder weergegeven:

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure26.png)

    ![F5 -configuratie (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure27.png) 

1. Hieronder vindt u de hele lijst met standaard SAML-kenmerken. GivenName wordt weergegeven met de volgende tekenreeks.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| | |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | `<TENANT ID>` |
| eb46b6b6.session.saml.last.assertionIssueInstant eb46b6b6.session.saml.last.assertionIssueInstant eb46b6b6.session.saml.last.assertionIssueInstant eb4  | `<ID>` |
| eb46b6b6.session.saml.last.assertionIssuer eb46b6b6.session.saml.last.assertionIssuer eb46b6b6.session.saml.last.assertionIssuer eb4 | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/displayname | gebruiker0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | gebruiker0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oase:namen:tc:SAML:2.0:ac:klassen:Wachtwoord |
| eb46b6b6.session.saml.last.authNInstant | `<ID>` |
| eb46b6b6.session.saml.last.identity | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.inResponseTo | `<TENANT ID>` |
| eb46b6b6.session.saml.last.nameIDValue | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.nameIdFormat | urn:oase:namen:tc:SAML:1.1:nameid-formaat:emailAddress |
| eb46b6b6.session.saml.last.responseBestemming | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId | `<TENANT ID>` |
| eb46b6b6.session.saml.last.responseIssueInstant eb46b6b6.session.saml.last.responseIssueInstant eb46b6b6.session.saml.last.responseIssueInstant eb4 | `<ID>` |
| eb46b6b6.session.saml.last.responseIssuer eb46b6b6.session.saml.last.responseIssuer eb46b6b6.session.saml.last.responseIssuer eb4 | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.result | 1 |
| eb46b6b6.session.saml.last.samlVersie | 2.0 |
| eb46b6b6.session.saml.last.sessionIndex | `<TENANT ID>` |
| eb46b6b6.session.saml.last.statusWaarde | urn:oase:namen:tc:SAML:2.0:status:Succes |
| eb46b6b6.session.saml.last.subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6.session.saml.last.subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectConfirmMethod | urn:oase:namen:tc:SAML:2.0:cm:drager |
| eb46b6b6.session.saml.last.validityNotBefore | `<ID>` |
| eb46b6b6.session.saml.last.validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>F5-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd B.Simon in F5. Werk samen met [f5-ondersteuningsteam](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) om de gebruikers toe te voegen aan het F5-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de F5-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de F5 waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer F5 met Azure AD](https://aad.portal.azure.com/)

- [F5-aanmelding configureren voor toepassing op basis van kopteksten](headerf5-tutorial.md)

- [F5-aanmelding configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

