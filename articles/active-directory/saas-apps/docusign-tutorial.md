---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met DocuSign | Microsoft Docs'
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
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983628"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met DocuSign

In deze zelf studie leert u hoe u DocuSign integreert met Microsoft Azure Active Directory (Azure AD). Wanneer u DocuSign integreert met Azure AD, kunt u het volgende doen:

* Gebruik Azure AD om te bepalen wie toegang heeft tot DocuSign.
* Schakel automatisch aanmelden in voor DocuSign voor uw gebruikers via hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [eenmalige aanmelding bij toepassingen in azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van SaaS-apps (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een DocuSign-abonnement dat eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving om te controleren of:

* DocuSign ondersteunt SSO die door de service provider (SP) is gestart.

* DocuSign biedt ondersteuning *voor Just-in-time* -gebruikers inrichting.

* DocuSign ondersteunt [automatische gebruikers inrichting](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial).
* Nadat u DocuSign hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-docusign-from-the-gallery"></a>DocuSign toevoegen uit de galerie

Als u de integratie van DocuSign in azure AD wilt configureren, moet u DocuSign uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u met een werk-of school account aan bij de [Azure Portal](https://portal.azure.com) of gebruik een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **DocuSign** in het zoekvak.
1. Selecteer **DocuSign** in het deel venster resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor DocuSign

Azure AD SSO met DocuSign configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in DocuSign.

Als u Azure AD SSO wilt configureren en testen met DocuSign, voltooit u de volgende bouw stenen:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user) eenmalige aanmelding van Azure ad te testen met B. Simon.
    1. [Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user) toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. [Configureer DOCUSIGN SSO](#configure-docusign-sso) voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. [Maak een DocuSign-test gebruiker](#create-docusign-test-user) voor het genereren van een tegen partij B. Simon in DocuSign dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. [Test SSO](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Voer de volgende stappen uit om Azure AD SSO in te scha kelen in de Azure Portal:

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **DocuSign** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Op de pagina **eenmalige aanmelding met SAML instellen** selecteert u het pictogram voor de pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer de volgende stappen uit in de sectie **basis configuratie van SAML** :

    a. Voer in het vak **AANMELDINGS URL** een URL in met behulp van het volgende patroon: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Voer in het vak **id (Entiteits-ID)** een URL in met behulp van het volgende patroon: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Deze waarden tussen haakjes zijn tijdelijke aanduidingen. Vervang deze door de waarden in de URL voor de werkelijke aanmelding en de id. Deze details worden beschreven in de sectie ' SAML 2,0-eind punten weer geven ' verderop in deze zelf studie.

1. Ga op de pagina **eenmalige aanmelding met SAML instellen** naar **certificaat (base64)** in het gedeelte **SAML-handtekening certificaat** . Selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **DocuSign instellen** de juiste URL (of url's) op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker met de naam B. Simon in de Azure Portal.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer aan de bovenkant van het scherm de optie **nieuwe gebruiker**.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **naam** **B. Simon**in.  
   1. Voer `<username>@<companydomain>.<extension>`in het veld **gebruikers naam** in. Bijvoorbeeld: `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie verleent u B. Simon toegang tot DocuSign, zodat deze gebruiker de eenmalige aanmelding van Azure kan gebruiken.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **DocuSign**.
1. Zoek op de pagina overzicht van de app de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst met **gebruikers** en druk op de knop **selecteren** aan de onderkant van het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Selecteer de knop **toewijzen** in het dialoog venster **toewijzing toevoegen** .

## <a name="configure-docusign-sso"></a>DocuSign SSO configureren

1. Als u de configuratie in DocuSign wilt automatiseren, moet u de uitbrei ding mijn apps Secure Sign-in browser installeren door **de extensie installeren**te selecteren.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de uitbrei ding aan de browser hebt toegevoegd, selecteert u **Setup DocuSign**. U wordt omgeleid naar de DocuSign-toepassing. Geef de beheerders referenties op om u aan te melden bij DocuSign. Met de browser extensie wordt de toepassing automatisch geconfigureerd en wordt stap 3 t/m 5 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u DocuSign hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw DocuSign-bedrijfs site als beheerder.

4. Selecteer in de rechter bovenhoek van de pagina het profiel logo en selecteer vervolgens **Go to admin**.
  
    ![Ga naar de beheerder onder profiel][51]

5. Selecteer op de pagina domein oplossingen de optie **domeinen**.

    ![Domein oplossingen/domeinen][50]

6. Selecteer in de sectie **domeinen** de optie **claim domein**.

    ![Claim domein optie][52]

7. Typ in het dialoog venster **een domein claimen** in het vak **domein naam** het bedrijfs domein en selecteer vervolgens **claim**. Zorg ervoor dat u het domein verifieert en of de status actief is.

    ![Dialoog venster domein/domein naam claimen][53]

8. Selecteer op de pagina domein oplossingen de optie **id-providers**.
  
    ![Optie id-providers][54]

9. Selecteer in de sectie **id-providers** de optie **ID-provider toevoegen**.

    ![Optie ID-provider toevoegen][55]

10. Voer de volgende stappen uit op de pagina instellingen van de **identiteits provider** :

    ![Velden met instellingen voor de identiteits provider][56]

    a. Typ in het vak **naam** een unieke naam voor uw configuratie. Gebruik geen spaties.

    b. Plak in het **vak Uitgever van de identiteits provider**de waarde van de **Azure ad-id** , die u hebt gekopieerd uit de Azure Portal.

    c. Plak in het vak aanmeldings-URL van de **identiteits provider** de waarde voor de **aanmeldings-URL** , die u hebt gekopieerd uit Azure Portal.

    d. Plak in het vak **Afmeldings-URL van de identiteits provider** de waarde van de **afmeldings-URL**, die u van Azure Portal hebt gekopieerd.

    e. Selecteer **authn-aanvraag ondertekenen**.

    f. Selecteer voor **verificatie aanvraag verzenden via** **post**.

    g. Selecteer **ophalen**voor **afmeldings aanvraag door**.

    h. Selecteer in de sectie **toewijzing van aangepast kenmerk** de optie **nieuwe toewijzing toevoegen**.

       ![Gebruikers interface voor aangepaste kenmerk toewijzing][62]

    i. Kies het veld dat u wilt toewijzen aan de Azure AD-claim. In dit voor beeld wordt de claim **EmailAddress** toegewezen aan de waarde van `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`. Dit is de standaard naam van de claim van Azure AD voor de e-mail claim. Selecteer **Opslaan**.

       ![Toewijzings velden voor aangepaste kenmerken][57]

       > [!NOTE]
       > Gebruik de juiste **gebruikers-id** om de gebruiker toe te wijzen vanuit Azure AD aan DocuSign-gebruikers toewijzing. Selecteer het juiste veld en voer de juiste waarde in op basis van de instellingen van uw organisatie.

    j. Selecteer **certificaat toevoegen**in de sectie **identiteits provider certificaten** , upload het certificaat dat u hebt gedownload van de Azure AD-Portal en selecteer **Opslaan**.

       ![Identiteits provider certificaten/certificaat toevoegen][58]

    k. Selecteer in de sectie **id-providers** de optie **acties**en selecteer vervolgens **eind punten**.

       ![Id-providers/-eind punten][59]

    l. Voer de volgende stappen uit in de sectie **SAML 2,0-eind punten weer geven** van de beheer portal van DocuSign:
       1. Kopieer de URL van de uitgever van de **service provider**en plak deze in het vak **id** in de sectie **basis configuratie van SAML** in de Azure Portal.

       1. Kopieer de **aanmeldings-URL van de service provider**en plak deze in het vak **Sign on URL** in het gedeelte **basis configuratie van SAML** in de Azure Portal.

       1. Selecteer **Sluiten**.

       ![SAML 2,0-eind punten weer geven][60]

### <a name="create-docusign-test-user"></a>DocuSign-test gebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in DocuSign. DocuSign biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in DocuSign, wordt er een nieuwe gemaakt na verificatie.

>[!Note]
>Als u hand matig een gebruiker moet maken, neemt u contact op met het [ondersteunings team van DocuSign](https://support.docusign.com/).

## <a name="test-sso"></a>SSO testen 

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel DocuSign in het toegangs venster selecteert, wordt u automatisch aangemeld bij het DocuSign-exemplaar waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Zelf studies over het integreren van SaaS-apps met Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding in azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer DocuSign met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

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