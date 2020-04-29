---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met BeyondTrust-externe ondersteuning | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en BeyondTrust externe ondersteuning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff21c3ee7721c82232e668ddb9645895080cf79
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74082066"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met BeyondTrust-externe ondersteuning

In deze zelf studie leert u hoe u BeyondTrust externe ondersteuning integreert met Azure Active Directory (Azure AD). Wanneer u BeyondTrust externe ondersteuning integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot BeyondTrust externe ondersteuning.
* Stel in dat uw gebruikers automatisch worden aangemeld voor BeyondTrust externe ondersteuning met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* BeyondTrust externe ondersteuning voor eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Externe ondersteuning voor BeyondTrust ondersteunt door **SP** GEÏNITIEERDe SSO
* Externe ondersteuning voor BeyondTrust ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>Externe ondersteuning voor BeyondTrust toevoegen vanuit de galerie

Als u de integratie van BeyondTrust-externe ondersteuning wilt configureren in azure AD, moet u BeyondTrust externe ondersteuning toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **BeyondTrust externe ondersteuning** in het zoekvak.
1. Selecteer **BeyondTrust externe ondersteuning** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor externe ondersteuning van BeyondTrust

Azure AD SSO configureren en testen met BeyondTrust-externe ondersteuning met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in BeyondTrust externe ondersteuning.

Als u Azure AD SSO wilt configureren en testen met BeyondTrust externe ondersteuning, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[BEYONDTRUST SSO voor externe ondersteuning configureren](#configure-beyondtrust-remote-support-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een BeyondTrust-gebruiker](#create-beyondtrust-remote-support-test-user)** voor het maken van een externe ondersteuning: als u een equivalent van B. Simon wilt hebben in BeyondTrust externe ondersteuning dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/) **naar de integratie** pagina van de BeyondTrust-toepassing voor **externe ondersteuning** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://<HOSTNAME>.bomgar.com/saml`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<HOSTNAME>.bomgar.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Deze waarden worden verderop in de zelf studie uitgelegd.

1. De BeyondTrust-toepassing voor externe ondersteuning verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Daarnaast verwacht de BeyondTrust-toepassing voor externe ondersteuning nog maar enkele kenmerken die worden door gegeven aan de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam |  Bronkenmerk|
    | ---------------| ----------|
    | GivenName | user.givenname |
    | EmailAddress | user.mail |
    | Naam | user.userprincipalname |
    | Gebruikersnaam | user.userprincipalname |
    | Groepen | gebruikers. groepen |
    | Unieke gebruikers-id | user.userprincipalname |

    > [!NOTE]
    > Bij het toewijzen van Azure AD-groepen voor de BeyondTrust-toepassing voor externe ondersteuning moet de optie groepen die zijn geretourneerd in claim worden gewijzigd van geen in beveiligings groep. De groepen worden als object-Id's in de toepassing geïmporteerd. U kunt de object-ID van de Azure AD-groep vinden door de eigenschappen in de Azure Active Directory interface te controleren. Dit is vereist voor het verwijzen naar en toewijzen van Azure AD-groepen aan het juiste groeps beleid.

1. Bij het instellen van de unieke gebruikers-id moet deze waarde worden ingesteld op NameID-indeling: **persistent**. Dit moet een permanente id zijn om de gebruiker correct te identificeren en te koppelen aan de juiste groeps beleidsregels voor machtigingen. Klik op het pictogram bewerken om het dialoog venster **gebruikers kenmerken & claims** te openen om de unieke gebruikers-id-waarde te bewerken.

1. Klik in de sectie **claim beheren** op de **indeling naam-id kiezen** en stel de waarde in op **permanent** en klik op **Opslaan**.

    ![Gebruikers kenmerken en claims](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer de gewenste URL ('s) op basis van uw vereiste op de **BeyondTrust externe ondersteuning instellen** .

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

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot BeyondTrust externe ondersteuning.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **BeyondTrust externe ondersteuning**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-beyondtrust-remote-support-sso"></a>SSO voor externe ondersteuning van BeyondTrust configureren

1. Meld u in een ander browser venster aan bij BeyondTrust externe ondersteuning als beheerder.

1. Klik op het menu **status** en kopieer de **id**, de **antwoord-URL** en de aanmeldings- **URL** en gebruik deze waarden in de sectie **basis configuratie van SAML** in de Azure Portal.

    ![Externe ondersteuning voor BeyondTrust configureren](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. Ga naar de/login-interface van BeyondTrust Remote `https://support.example.com/login` support op de locatie **support.example.com** de primaire hostnaam van uw apparaat is en verificatie met uw beheerders referenties.

1. Navigeer naar **gebruikers & beveiligings** > **providers**voor beveiliging.

1. Selecteer in de vervolg keuzelijst **SAML** en klik op de knop **provider maken** .

1. Onder de sectie instellingen van de identiteits provider ziet u een optie voor het uploaden van de meta gegevens van de identiteits provider. Zoek het XML-bestand met meta gegevens dat u hebt gedownload van de Azure Portal en klik op de knop **uploaden** . De **entiteit-id**, de **URL van de service voor eenmalige aanmelding** en het certificaat worden automatisch geüpload en de **protocol binding** moet worden gewijzigd in **http post**. Zie de onderstaande scherm afbeelding:

    ![Externe ondersteuning voor BeyondTrust configureren](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>BeyondTrust-test gebruiker voor externe ondersteuning maken

De instellingen voor de gebruikers inrichting worden hier geconfigureerd. Voor de waarden die in deze sectie worden gebruikt, wordt verwezen vanuit het gedeelte **gebruikers kenmerken & claims** in de Azure Portal. We hebben dit geconfigureerd als de standaard waarden die al worden geïmporteerd op het moment dat ze worden gemaakt, maar de waarde kan indien nodig worden aangepast.

![Gebruiker maken](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> Het kenmerk groups en e-mail zijn niet nodig voor deze implementatie. Als u Azure AD-groepen gebruikt en toewijst aan BeyondTrust-beleid voor externe ondersteuning voor machtigingen, moet naar de object-ID van de groep worden verwezen via de eigenschappen in de Azure Portal en in de sectie beschik bare groepen worden geplaatst. Zodra dit is voltooid, is de object-ID/AD-groep nu beschikbaar voor toewijzing aan groeps beleid voor machtigingen.

![Gebruiker maken](./media/bomgarremotesupport-tutorial/config-user2.png)

![Gebruiker maken](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> U kunt ook een standaard groeps beleid instellen op de SAML2-beveiligings provider. Als u deze optie definieert, worden alle gebruikers die zijn geverifieerd via SAML de machtigingen die zijn opgegeven in het groeps beleid, toegewezen. Het beleid voor algemene leden is opgenomen in BeyondTrust externe ondersteuning/privileged Remote Access met beperkte machtigingen. Dit kan worden gebruikt om de verificatie te testen en gebruikers toe te wijzen aan het juiste beleid. Gebruikers vullen zich niet in de SAML2-gebruikers lijst via/login > gebruikers & beveiliging tot de eerste geslaagde verificatie poging. Meer informatie over groeps beleidsregels vindt u op de volgende koppeling:`https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel BeyondTrust externe ondersteuning in het toegangs venster klikt, moet u automatisch worden aangemeld bij de BeyondTrust externe ondersteuning waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer BeyondTrust externe ondersteuning met Azure AD](https://aad.portal.azure.com/)
