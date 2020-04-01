---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met BeyondTrust Remote Support | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en BeyondTrust Remote Support.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74082066"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met BeyondTrust Remote Support

In deze zelfstudie leert u hoe u BeyondTrust Remote Support integreren met Azure Active Directory (Azure AD). Wanneer u BeyondTrust Remote Support integreert met Azure AD, u het als:

* Controle in Azure AD die toegang heeft tot BeyondTrust Remote Support.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij BeyondTrust Remote Support met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* BeyondTrust Remote Support single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* BeyondTrust Remote Support ondersteunt **SP** geïnitieerde SSO
* BeyondTrust Remote Support ondersteunt **Just In Time** gebruikersinrichting

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>BeyondTrust-ondersteuning op afstand toevoegen vanuit de galerie

Als u de integratie van BeyondTrust Remote Support in Azure AD wilt configureren, moet u BeyondTrust Remote Support vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **BeyondTrust Remote Support** in het zoekvak in de sectie **Toevoegen in de galeriesectie.**
1. Selecteer **BeyondTrust Remote Support** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>Azure AD-aanmelding configureren en testen voor BeyondTrust-ondersteuning op afstand

Azure AD SSO configureren en testen met BeyondTrust Remote Support met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in BeyondTrust Remote Support.

Als u Azure AD SSO wilt configureren en testen met BeyondTrust Remote Support, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer BeyondTrust Remote Support SSO](#configure-beyondtrust-remote-support-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[BeyondTrust Remote Support testgebruiker](#create-beyondtrust-remote-support-test-user)** maken - om een tegenhanger van B.Simon te hebben in BeyondTrust Remote Support die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **BeyondTrust Remote** Support-toepassingsintegratie de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<HOSTNAME>.bomgar.com/saml`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<HOSTNAME>.bomgar.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. U krijgt deze waarden later uitgelegd in de tutorial.

1. BeyondTrust Remote Support-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, BeyondTrust Remote Support applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name |  Bronkenmerk|
    | ---------------| ----------|
    | Givenname | user.givenname |
    | Emailaddress | user.mail |
    | Name | user.userprincipalname |
    | Gebruikersnaam | user.userprincipalname |
    | Groepen | user.groups |
    | Unieke gebruikers-id | user.userprincipalname |

    > [!NOTE]
    > Wanneer u Azure AD-groepen toewijs voor de BeyondTrust Remote Support-toepassing, moet de optie 'Groepen geretourneerd in claim' worden gewijzigd van Geen naar SecurityGroup. De groepen worden geïmporteerd in de toepassing als hun object-id's. De object-id van de Azure AD-groep kan worden gevonden door de eigenschappen in de Azure Active Directory-interface te controleren. Dit is vereist om Azure AD-groepen te verwijzen en toe te wijzen aan het juiste groepsbeleid.

1. Wanneer u de unieke gebruikers-id instelt, moet deze waarde worden ingesteld op NameID-notatie: **Persistent**. We vereisen dat dit een permanente id is om de gebruiker correct te identificeren en te koppelen aan het juiste groepsbeleid voor machtigingen. Klik op het pictogram Bewerken om het dialoogvenster **Gebruikerskenmerken & claims** te openen om de waarde unieke gebruikers-id te bewerken.

1. Klik in de sectie **Claim beheren** op de **indeling Naam-id kiezen** en stel de waarde in op **Persistent** en klik op **Opslaan**.

    ![Gebruikerskenmerken en -claims](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **BeyondTrust Remote Support** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot BeyondTrust Remote Support.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **BeyondTrust Remote Support**in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-beyondtrust-remote-support-sso"></a>BeyondTrust Remote Support SSO configureren

1. Meld u in een ander browservenster aan bij BeyondTrust Remote Support als beheerder.

1. Klik op het menu **STATUS** en kopieer de **id,** **de URL van antwoord** en Teken op **URL** en gebruik deze waarden in de sectie **BasisSAML-configuratie** in de Azure-portal.

    ![BeyondTrust-ondersteuning op afstand configureren](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. Navigeer naar de BeyondTrust Remote `https://support.example.com/login` Support /login-interface op de plaats waar **support.example.com** de primaire hostnaam van uw toestel is en verifieer met uw beheerdersreferenties.

1. Navigeer naar gebruikers & > **beveiligingsproviders.** **Users & Security**

1. Selecteer **SAML** in de vervolgkeuzelijst en klik op de knop **Provider maken.**

1. Onder de sectie Instellingen van de identiteitsprovider is er een optie om de metagegevens van de identiteitsprovider te uploaden. Zoek het XML-bestand met metagegevens dat u hebt gedownload van de Azure-portal en klik op de knop **Uploaden.** De URL en het certificaat van de **entiteits-id,** **eenmalige aanmeldingsservice** worden automatisch geüpload en de **protocolbinding** moet worden gewijzigd in **HTTP POST**. Zie screenshot hieronder:

    ![BeyondTrust-ondersteuning op afstand configureren](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>BeyondTrust Remote Support testgebruiker maken

We configureren hier de instellingen voor gebruikersvoorziening. De waarden die in deze sectie worden gebruikt, worden verwezen vanuit de sectie **Gebruikerskenmerken & claims** in de Azure-portal. We hebben dit geconfigureerd als de standaardwaarden die al zijn geïmporteerd op het moment van creatie, maar de waarde kan indien nodig worden aangepast.

![Gebruiker maken](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> De groepen en het e-mailattribuut zijn niet nodig voor deze implementatie. Als u Azure AD-groepen gebruikt en deze toewijs aan BeyondTrust Remote Support Group Policies voor machtigingen, moet de object-id van de groep worden verwezen via de eigenschappen ervan in de Azure-portal en in de sectie 'Beschikbare groepen' worden geplaatst. Zodra dit is voltooid, is de Object-ID/AD-groep nu beschikbaar voor toewijzing aan een groepsbeleid voor machtigingen.

![Gebruiker maken](./media/bomgarremotesupport-tutorial/config-user2.png)

![Gebruiker maken](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> U ook een standaardgroepsbeleid instellen op de SAML2-beveiligingsprovider. Door deze optie te definiëren, worden alle gebruikers die via SAML de machtigingen verifiëren die zijn opgegeven in het groepsbeleid toegewezen. Het beleid voor algemene leden is opgenomen in BeyondTrust Remote Support/Privileged Remote Access met beperkte machtigingen, die kunnen worden gebruikt om verificatie te testen en gebruikers toe te wijzen aan het juiste beleid. Gebruikers worden niet in de SAML2-gebruikerslijst ingevuld via /login > Users & Security tot de eerste geslaagde verificatiepoging. Aanvullende informatie over groepsbeleid vindt u op de volgende link:`https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Externe ondersteuning van BeyondTrust klikt in het toegangspaneel, moet u automatisch worden aangemeld bij de BeyondTrust Remote Support waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer BeyondTrust Remote Support met Azure AD](https://aad.portal.azure.com/)
