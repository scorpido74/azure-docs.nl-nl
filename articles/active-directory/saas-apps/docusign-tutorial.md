---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met DocuSign | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01f969c3bc6f546025b3bbe5826181efdfa69be0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983628"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met DocuSign

In deze zelfstudie leert u hoe u DocuSign integreert met Microsoft Azure Active Directory (Azure AD). Wanneer u DocuSign integreert met Azure AD, u het als:

* Gebruik Azure AD om te bepalen wie toegang heeft tot DocuSign.
* Automatische aanmelding bij DocuSign inschakelen voor uw gebruikers via hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Single sign-on](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor toepassingen in Azure AD voor meer informatie over de integratie van de App -app voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Een DocuSign-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving om te controleren of:

* DocuSign ondersteunt door de provider (SP)geïnitieerde SSO.

* DocuSign ondersteunt *just-in-time* gebruikersinrichting.

* DocuSign ondersteunt [automatische gebruikersinrichting](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial).
* Zodra u DocuSign hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-docusign-from-the-gallery"></a>DocuSign toevoegen vanuit de galerie

Als u de integratie van DocuSign in Azure AD wilt configureren, moet u DocuSign vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of met een persoonlijk Microsoft-account.
1. Selecteer in het navigatiedeelvenster aan de linkerkant de **Azure Active Directory-service.**
1. Ga naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **DocuSign** in het zoekvak in de sectie **Toevoegen in de galeriesectie.**
1. Selecteer **DocuSign** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Azure AD-aanmelding voor DocuSign configureren en testen

Azure AD SSO configureren en testen met DocuSign met behulp van een testgebruiker met de naam **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de desbetreffende gebruiker in DocuSign.

Als u Azure AD SSO wilt configureren en testen met DocuSign, voert u de volgende bouwstenen in:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om Azure AD-enkele aanmelding met B.Simon te testen.
    1. [Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user) toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. [Configureer DocuSign SSO](#configure-docusign-sso) om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. [Maak een DocuSign-testgebruiker](#create-docusign-test-user) om een tegenhanger van B.Simon te genereren in DocuSign die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. [Test SSO](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Voer de volgende stappen uit om Azure AD SSO in te schakelen in de Azure-portal:

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Metude-toepassingsintegratie** de sectie **Beheren** en selecteer **vervolgens eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Selecteer op de pagina **Eén aanmelding instellen met SAML** het penpictogram voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit:

    a. Voer in het vak **Aanmelding op URL** een URL in met het volgende patroon:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Voer in het vak **Id (Entity ID)** een URL in met het volgende patroon:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Deze waarden tussen haakjes zijn tijdelijke aanduidingen. Vervang ze door de waarden in de werkelijke aanmeldings-URL en id. Deze details worden later in deze zelfstudie uitgelegd in de sectie 'Saml 2.0-eindpunten bekijken'.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** op **Certificaat (Base64)**. Selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **DocuSign instellen** de juiste URL (of URL's) op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker met de naam B.Simon in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory**, selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer Boven aan het scherm De optie **Nieuwe gebruiker**.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam** **B.Simon**in.  
   1. Voer **in** het veld `<username>@<companydomain>.<extension>`Gebruikersnaam . Bijvoorbeeld: `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie verleent u B.Simon toegang tot DocuSign, zodat deze gebruiker azure-enkele aanmelding kan gebruiken.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **DocuSign**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens in het dialoogvenster Toewijzing **toevoegen** de optie Gebruikers **en groepen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst **Gebruikers** en druk op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en drukt u op de knop **Selecteren** onder aan het scherm.
1. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen.**

## <a name="configure-docusign-sso"></a>DocuSign SSO configureren

1. Als u de configuratie in DocuSign wilt automatiseren, moet u de browserextensie Mijn apps Secure Sign-in installeren installeren **installeren.**

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, selecteert u **Setup DocuSign**. U wordt doorverwezen naar de DocuSign-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij DocuSign. De browserextensie configureert automatisch de toepassing en automatiseert stap 3 tot en met 5.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u DocuSign handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij uw DocuSign-bedrijfssite.

4. Selecteer in de rechterbovenhoek van de pagina het profiellogo en selecteer **Ga naar Beheerder**.
  
    ![Ga naar Beheerder onder Profiel][51]

5. Selecteer **domeinen**op de pagina Domeinen .

    ![Domeinoplossingen/domeinen][50]

6. Selecteer IN de sectie **Domeinen** de optie **CLAIM-DOMEIN**.

    ![Claimdomein, optie][52]

7. Typ in het dialoogvenster **Een domein claimen** in het vak **Domeinnaam** uw bedrijfsdomein en selecteer **CLAIM**. Controleer of u het domein controleert en dat de status ervan actief is.

    ![Dialoogvenster Een domein/domeinnaam claimen][53]

8. Selecteer **identiteitsproviders**op de pagina Domeinoplossingen .
  
    ![Optie Identiteitsproviders][54]

9. Selecteer in de sectie **Identiteitsproviders** de optie **IDENTITEITSPROVIDER TOEVOEGEN**.

    ![De optie Identiteitsprovider toevoegen][55]

10. Voer op de pagina **Instellingen voor identiteitsprovider** de volgende stappen uit:

    ![Instellingen voor identiteitsprovider ( velden )][56]

    a. Typ in het vak **Naam** een unieke naam voor uw configuratie. Gebruik geen spaties.

    b. Plak in het **vak Identity Provider Issuer**de azure **AD-id-waarde** die u hebt gekopieerd van de Azure-portal.

    c. Plak in het **vak URL voor het inloggen van de identiteitsprovider** de **URL-waarde aanmelding,** die u hebt gekopieerd vanuit de Azure-portal.

    d. Plak in het **vak URL-logboeken** van de identiteitsprovider de waarde van de URL van **afmelden**, die u hebt gekopieerd vanuit azure-portal.

    e. Selecteer **AuthN-aanvraag ondertekenen**.

    f. Selecteer POST voor **AuthN-verzoek verzenden**door **.**

    g. Selecteer **GET** **voor het verzenden van afmeldingsaanvraag.**

    h. Selecteer **NIEUWE TOEWIJZING TOEVOEGEN**in de sectie Aangepaste toewijzing van **kenmerken** .

       ![Gebruikersinterface voor het toewijzen van aangepaste kenmerken][62]

    i. Kies het veld dat u wilt toewijzen aan de Azure AD-claim. In dit voorbeeld wordt de **e-mailadresclaim** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`toegewezen met de waarde van . Dat is de standaardclaimnaam van Azure AD voor de e-mailclaim. Selecteer **OPSLAAN**.

       ![Aangepaste toewijzingsvelden voor kenmerken][57]

       > [!NOTE]
       > Gebruik de juiste **gebruikers-id** om de gebruiker van Azure AD naar DocuSign-gebruikerstoewijzing toe te geven. Selecteer het juiste veld en voer de juiste waarde in op basis van uw organisatie-instellingen.

    j. Selecteer in de sectie **Certificaten van identiteitsprovider** de optie **CERTIFICAAT TOEVOEGEN,** upload het certificaat dat u hebt gedownload van de Azure AD-portal en selecteer **OPSLAAN**.

       ![Certificaten van identiteitsprovider/certificaat toevoegen][58]

    k. Selecteer **in**de sectie **Identiteitsproviders** acties en selecteer **Vervolgens Eindpunten**.

       ![Identiteitsproviders/eindpunten][59]

    l. Voer in de sectie **SAML 2.0-eindpunten** bekijken van de docusign-beheerportal de volgende stappen uit:
       1. Kopieer de URL van de **serviceprovideruitgever**en plak deze vervolgens in het vak **Identifier** in de sectie **BasisSAML-configuratie** in de Azure-portal.

       1. Kopieer de **URL voor aanmelding van serviceprovider**en plak deze vervolgens in het vak Aanmelding in de sectie **BasisSAML-configuratie** in de Azure-portal. **Sign On URL**

       1. Selecteer **Sluiten**.

       ![SAML 2.0-eindpunten weergeven][60]

### <a name="create-docusign-test-user"></a>DocuSign-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in DocuSign. DocuSign ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in DocuSign, wordt er een nieuwe gemaakt na verificatie.

>[!Note]
>Als u handmatig een gebruiker wilt maken, neemt u contact op met het [ondersteuningsteam van DocuSign.](https://support.docusign.com/)

## <a name="test-sso"></a>Test SSO 

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel DocuSign selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij het DocuSign-exemplaar waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Zelfstudies over het integreren van SaaS-apps met Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding in Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [DocuSign uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png