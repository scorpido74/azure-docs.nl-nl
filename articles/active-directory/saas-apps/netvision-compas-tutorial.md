---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met Netvision Compas | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Netvision Compas.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a56f44c5-dc08-4c7c-ad20-b6e7127deb2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3015ea26d81505c4f058846dbcb3b7858f79267
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520106"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Netvision Compas

In deze zelfstudie leert u hoe u Netvision Compas integreert met Azure Active Directory (Azure AD). Wanneer u Netvision Compas integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Netvision Compas.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Netvision Compas met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Netvision Compas single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Netvision Compas ondersteunt **SP en IDP** geïnitieerd sso
* Zodra u Netvision Compas configureert, u sessiebeheer afdwingen, wat exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermt. Sessiebeheer strekt zich uit van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>Netvision Compas toevoegen vanuit de galerie

Als u de integratie van Netvision Compas in Azure AD wilt configureren, moet u Netvision Compas vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Netvision Compas** in het zoekvak in de sectie **Toevoegen vanuit de galerie.**
1. Selecteer **Netvision Compas** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Azure AD-aanmelding voor Netvision Compas configureren en testen

Azure AD SSO configureren en testen met Netvision Compas met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppeling smaken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Netvision Compas.

Als u Azure AD SSO wilt configureren en testen met Netvision Compas, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Netvision Compas SSO](#configure-netvision-compas-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Configureer Netvision Compas-testgebruiker](#configure-netvision-compas-test-user)** - om een tegenhanger van B.Simon in Netvision Compas te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Netvision Compas-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<TENANT>.compas.cloud/Identity/Saml20`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [het ondersteuningsteam van Netvision Compas Client](mailto:contact@net.vision) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het metagegevensbestand te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)



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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Netvision Compas.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Netvision Compas**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-netvision-compas-sso"></a>Netvision Compas SSO configureren

In deze sectie schakelt u SAML SSO in **Netvision Compas**in.
1. Log in bij **Netvision Compas** met een administratief account en krijg toegang tot het administratiegebied.

    ![Beheerdersgebied](media/netvision-compas-tutorial/admin.png)

1. Zoek het **systeemgebied** en selecteer **Identiteitsproviders**.

    ![Admin-id's](media/netvision-compas-tutorial/admin-idps.png)

1. Selecteer de actie **Toevoegen** om Azure AD te registreren als een nieuw IDP.

    ![IDP toevoegen](media/netvision-compas-tutorial/idps-add.png)

1. Selecteer **SAML** voor het **type Provider**.
1. Voer betekenisvolle waarden in voor de velden **Weergavenaam** en **Beschrijving.**
1. Wijs **Netvision Compas-gebruikers** toe aan het IDP door in de lijst **Beschikbare gebruikers** te selecteren en vervolgens de knop Geselecteerd **toevoegen te** selecteren. Gebruikers kunnen ook worden toegewezen aan het IDP tijdens het volgen van de inrichtingsprocedure.
1. Klik voor de optie **Saml met ametjes** op de knop **Bestand kiezen** en selecteer het metagegevensbestand dat eerder op uw computer is opgeslagen.
1. Klik op **Opslaan**.

    ![IDP bewerken](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Netvision Compas-testgebruiker configureren

In deze sectie configureert u een bestaande gebruiker in **Netvision Compas** om Azure AD voor SSO te gebruiken.
1. Volg de gebruiksprocedure van **Netvision Compas,** zoals gedefinieerd door uw bedrijf of bewerk een bestaand gebruikersaccount.
1. Controleer tijdens het definiëren van het profiel van de gebruiker of het **e-mailadres (Persoonlijk)** van de gebruiker overeenkomt met de Azure AD-gebruikersnaam: username@companydomain.extension. Bijvoorbeeld `B.Simon@contoso.com`.

    ![Gebruiker bewerken](media/netvision-compas-tutorial/user-config.png)

Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Test SSO 

In deze sectie test u uw configuratie met eenmalige aanmelding in Azure AD.

### <a name="using-the-access-panel-idp-initiated"></a>Het Access Panel gebruiken (IDP gestart).

Wanneer u op de tegel Netvision Compas in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Netvision Compas waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Rechtstreeks toegang tot Netvision Compas (SP geïnitieerd).

1. Toegang tot de **URL van Netvision Compas.** Bijvoorbeeld `https://tenant.compas.cloud`.
1. Voer de gebruikersnaam **netvision Compas** in en selecteer **Volgende**.

    ![Aangemelde gebruiker](media/netvision-compas-tutorial/login-user.png)

1. **(facultatief)** Als de gebruiker meerdere IdP's binnen **Netvision Compas**krijgt toegewezen, wordt een lijst met beschikbare IdP's weergegeven. Selecteer de Azure AD IDP die eerder is geconfigureerd in **Netvision Compas**.

    ![Aanmelden kiezen](media/netvision-compas-tutorial/login-choose.png)

1. U wordt doorgestuurd naar Azure AD om de verificatie uit te voeren. Zodra u met succes bent geverifieerd, moet u automatisch worden aangemeld bij **Netvision Compas** waarvoor u SSO hebt ingesteld.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Netvision Compas met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
