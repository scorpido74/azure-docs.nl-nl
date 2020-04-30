---
title: 'Zelf studie: integratie Azure Active Directory met Civic-platform | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Civic platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d790454-143e-40ac-b3cb-5a256977b4db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ccf124c5a4160715df4e685e405dcd591c49ae7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68496824"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Zelf studie: Civic-platform integreren met Azure Active Directory

In deze zelf studie leert u hoe u Civic platform integreert met Azure Active Directory (Azure AD). Wanneer u Civic-platform integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot het Civic-platform.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Civic platform met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proef versie van één maand ontvangen.
* Abonnement voor eenmalige aanmelding (SSO) van het Civic-platform.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Het Civic-platform ondersteunt door **SP** GEÏNITIEERDe SSO





## <a name="adding-civic-platform-from-the-gallery"></a>Het Civic-platform wordt toegevoegd vanuit de galerie

Als u de integratie van het Civic-platform wilt configureren in azure AD, moet u Civic-platform uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Civic platform** in het zoekvak.
1. Selecteer **Civic platform** in het resultaten paneel en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test Azure AD SSO met Civic-platform met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in het Civic-platform.

Als u Azure AD SSO wilt configureren en testen met Civic platform, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[Civic platform SSO configureren](#configure-civic-platform-sso)** : als u de instellingen voor eenmalige aanmelding aan de kant van de toepassing wilt configureren.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Maak een Civic-test gebruiker](#create-civic-platform-test-user)** voor het maken van een exemplaar van B. Simon in het Civic-platform dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Civic platform** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.accela.com`

    b. Typ een URL in het vak **Id (Entiteits-id)**: `civicplatform.accela.com`

    > [!NOTE]
    > De waarde van de aanmeldings-URL is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van Civic platform](mailto:skale@accela.com) om deze waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

1. Navigeer naar **Azure Active Directory** > **app-registraties** in azure AD en selecteer uw toepassing.

1. Kopieer de **Directory-id (Tenant)** en sla deze op in Klad blok.

    ![Kopieer de directory (Tenant-ID) en sla deze op in uw app-code](media/civic-platform-tutorial/directoryid.png)

1. Kopieer de **toepassings-id** en sla deze op in Klad blok.

   ![De toepassings-ID (client) kopiëren](media/civic-platform-tutorial/applicationid.png)

1. Navigeer naar **Azure Active Directory** > **app-registraties** in azure AD en selecteer uw toepassing. Selecteer **certificaten & geheimen**.

1. Selecteer **client geheimen-> nieuw client Secret**.

1. Geef een beschrijving op van het geheim en een duur. Wanneer u klaar bent, selecteert u **toevoegen**.

   > [!NOTE]
   > Nadat het client geheim is opgeslagen, wordt de waarde van het client geheim weer gegeven. Kopieer deze waarde omdat u de sleutel niet later kunt ophalen.

   ![Kopieer de geheime waarde omdat u deze later niet kunt ophalen](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Civic platform-SSO configureren

1. Open een nieuw webbrowser venster en meld u aan bij uw Atlassian Cloud-bedrijfs site als beheerder.

1. Klik op **standaard keuzes**.

    ![De link om het certificaat te downloaden](media/civic-platform-tutorial/standard-choices.png)

1. Maak een standaard keuze- **ssoconfig**.

1. Zoek naar **ssoconfig** en verzend.

    ![De link om het certificaat te downloaden](media/civic-platform-tutorial/sso-config.png)

1. Vouw SSOCONFIG uit door te klikken op rode stip.

    ![De link om het certificaat te downloaden](media/civic-platform-tutorial/sso-config01.png)

1. Geef SSO gerelateerde configuratie-informatie op in de volgende stap:

    ![De link om het certificaat te downloaden](media/civic-platform-tutorial/sso-config02.png)

    1. Voer in het veld **applicationid** de waarde van de **toepassings-id** in die u hebt gekopieerd van de Azure Portal.

    1. Voer in het veld **clientSecret** de **geheime** waarde in die u van de Azure Portal hebt gekopieerd.

    1. Voer in het veld **directoryId** de **Directory (Tenant) ID-** waarde in die u van de Azure Portal hebt gekopieerd.

    1. Voer de idpName in. Bijvoorbeeld:- `Azure`.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan het Civic-platform.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Civic platform**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-civic-platform-test-user"></a>Test gebruiker voor Civic-platform maken

In deze sectie maakt u een gebruiker met de naam B. Simon in Civic platform. Werk samen met het ondersteunings team van het Civic-platform om de gebruikers toe te voegen aan het [ondersteunings team](mailto:skale@accela.com)van de Civic Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Civic platform in het toegangs venster klikt, moet u automatisch worden aangemeld bij het Civic-platform waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

