---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met iProva | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en iProva configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98458f8be162d0903f5ea0d1f7d4651d46f78e8e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80048438"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met iProva

In deze zelf studie leert u hoe u iProva integreert met Azure Active Directory (Azure AD). Wanneer u iProva integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot iProva.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij iProva met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* iProva-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* iProva ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Nadat u iProva hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iprova-from-the-gallery"></a>iProva toevoegen vanuit de galerie

Om de integratie van iProva in Azure AD te configureren, moet u iProva vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **iProva** in het zoekvak.
1. Selecteer **iProva** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor iProva

Azure AD SSO met iProva configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in iProva.

Als u Azure AD SSO wilt configureren en testen met iProva, voltooit u de volgende bouw stenen:

1. **[Configuratiegegevens ophalen uit iProva](#retrieve-configuration-information-from-iprova)**: ter voorbereiding op de volgende stappen.
1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Maak een iProva-test gebruiker](#create-iprova-test-user)** -om een equivalent van B. Simon in iProva te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[IPROVA SSO configureren](#configure-iprova-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="retrieve-configuration-information-from-iprova"></a>Configuratiegegevens ophalen uit iProva

In dit gedeelte gaat u gegevens ophalen uit iProva om eenmalige aanmelding van Azure AD te configureren.

1. Open een webbrowser en ga naar de **SAML2-infopagina** van iProva, met behulp van het volgende URL-patroon:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

    ![De iProva SAML2-infopagina weergeven](media/iprova-tutorial/iprova-saml2-info.png)

1. Laat het tabblad geopend terwijl u de volgende stappen uitvoert op een nieuw browsertabblad.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **IProva** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. Vul in het vak **Aanmeldings-URL** de waarde in die wordt weergegeven achter het label **Sign-on URL** op de **SAML2-infopagina van iProva**. Deze pagina is nog steeds geopend op het andere tabblad van de browser.

    b. Vul in het vak **Id** de waarde in die wordt weergegeven achter het label **EntityID** op de **SAML2-infopagina van iProva**. Deze pagina is nog steeds geopend op het andere tabblad van de browser.

    c. Vul in het vak **Antwoord-URL** de waarde in die wordt weergegeven achter het label **Reply URL** op de **SAML2-infopagina van iProva**. Deze pagina is nog steeds geopend op het andere tabblad van de browser.

1. de iProva-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Daarnaast verwacht iProva toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam | Bronkenmerk| Naamruimte  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op de knop kopiëren om de URL van de **app Federation-meta gegevens** te kopiëren en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

## <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan iProva.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **iProva** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="create-iprova-test-user"></a>Testgebruiker voor iProva maken

1. Meld u aan bij iProva met uw **beheerdersaccount**.

2. Open het menu **Go to**.

3. Selecteer **Application management**.

4. Selecteer **Users** in het deelvenster **Users and user groups**.

5. Selecteer **Toevoegen**.

6. Voer in het vak **gebruikers naam** de gebruikers naam van de `B.Simon@contoso.com`gebruiker in.

7. Voer in het vak **volledige naam** de volledige naam van de gebruiker in zoals **B. Simon**.

8. Selecteer de optie **No password (use single sign-on)**.

9. Voer in het vak **e-mail adres** het e-mail adres `B.Simon@contoso.com`van de gebruiker in.

10. Scrol omlaag naar het einde van de pagina en selecteer **Finish**.

## <a name="configure-iprova-sso"></a>IProva SSO configureren

1. Meld u aan bij iProva met uw **beheerdersaccount**.

2. Open het menu **Go to**.

3. Selecteer **Application management**.

4. Selecteer **General** in het deelvenster **System settings**.

5. Selecteer **bewerken**.

6. Scrol omlaag naar **Access control**.

    ![Instellingen voor toegangsbeheer van iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Zoek de instelling **Users are automatically logged on with their network accounts** en wijzig deze in **Yes, authentication via SAML**. Er worden nu aanvullende opties weergegeven.

8. Selecteer **Set up**.

9. Selecteer **Next**.

10. Er wordt nu gevraagd of u federatiegegevens wilt downloaden via een URL of wilt uploaden uit een bestand. Selecteer de optie **van URL** .

    ![Azure AD-metagegevens downloaden](media/iprova-tutorial/iprova-download-metadata.png)

11. Plak de metagegevens-URL die u hebt opgeslagen in de laatste stap van de procedure Eenmalige aanmelding van Azure AD configureren.

12. Selecteer de pijlknop om de metagegevens te downloaden uit Azure AD.

13. Wanneer het downloaden is voltooid **,** wordt er een bevestigingsberichtweergegeven.

14. Selecteer **Next**.

15. Sla de optie **Test login** maar even over en klik op de knop **Next**.

16. Selecteer **windowsaccountname** in de vervolgkeuzelijst **Claim to use**.

17. Selecteer **Finish**.

18. U keert nu terug naar het scherm **Edit general settings**. Scrol omlaag naar de onderkant van de pagina en selecteer **OK** om de configuratie op te slaan.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel iProva klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van in het toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer iProva met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [IProva beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)