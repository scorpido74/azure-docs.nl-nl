---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Palo Alto-netwerken-GlobalProtect | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Palo Alto Networks - GlobalProtect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8faa1d0ecfec629487c9cddee6a01fb9f809393f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "70166413"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Palo Alto-netwerken-GlobalProtect

In deze zelf studie leert u hoe u Palo Alto Networks-GlobalProtect integreert met Azure Active Directory (Azure AD). Wanneer u Palo Alto Networks-GlobalProtect integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Palo Alto-netwerken-GlobalProtect.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Palo Alto-netwerken-GlobalProtect met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Palo Alto Networks-GlobalProtect SSO (single sign-on) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Palo Alto Networks - GlobalProtect ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* Palo Alto Networks - GlobalProtect ondersteunt **Just In Time**-gebruikersinrichting

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Palo Alto Networks - GlobalProtect uit de galerie toevoegen

Als u de integratie van Palo Alto Networks - GlobalProtect met Azure AD wilt configureren, moet u Palo Alto Networks - GlobalProtect uit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie de** **Palo Alto Networks-GlobalProtect** in het zoekvak.
1. Selecteer **Palo Alto Networks-GlobalProtect** uit het paneel resultaten en voeg de app vervolgens toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-palo-alto-networks---globalprotect"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Palo Alto-netwerken-GlobalProtect

Azure AD SSO configureren en testen met Palo Alto Networks-GlobalProtect met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Palo Alto Networks-GlobalProtect.

Als u Azure AD SSO wilt configureren en testen met Palo Alto Networks-GlobalProtect, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configureer Palo Alto Networks-GLOBALPROTECT SSO](#configure-palo-alto-networks---globalprotect-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. **[Maak Palo Alto Networks-GlobalProtect test User](#create-palo-alto-networks---globalprotect-test-user)** -om een equivalent van B. Simon te hebben in Palo Alto Networks-GlobalProtect dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Palo Alto Networks-GlobalProtect** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<Customer Firewall URL>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteuningsteam van Palo Alto Networks - GlobalProtect](https://support.paloaltonetworks.com/support) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer op de sectie **Palo Alto Networks-GlobalProtect instellen** de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Palo Alto-netwerken-GlobalProtect.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Palo Alto Networks-GlobalProtect**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Palo Alto Networks-GlobalProtect SSO configureren

1. Open de gebruikersinterface voor firewallbeheer van Palo Alto Networks als beheerder in een ander browservenster.

2. Klik op **Apparaat**.

    ![Eenmalige aanmelding voor Palo Alto configureren](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Selecteer **SAML-id-provider** in de linkernavigatiebalk en klik op Importeren om het metagegevensbestand te importeren.

    ![Eenmalige aanmelding voor Palo Alto configureren](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Voer de volgende acties uit in het venster Importeren

    ![Eenmalige aanmelding voor Palo Alto configureren](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Geef in het tekstvak **Profielnaam** een naam op, bijvoorbeeld Azure AD GlobalProtect.

    b. Klik in **Metagegevens van id-provider** op **Bladeren** en selecteer het bestand metadata.xml dat u vanuit Azure Portal hebt gedownload

    c. Klik op **OK**

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Testgebruiker voor Palo Alto Networks - GlobalProtect maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Palo Alto Networks-GlobalProtect. Palo Alto Networks - GlobalProtect ondersteunt Just In Time-gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Palo Alto Networks - GlobalProtect bestaat, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel van Palo Alto Networks - GlobalProtect klikt in het toegangsvenster, moet u automatisch worden aangemeld bij de instantie van Palo Alto Networks - GlobalProtect waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Palo Alto Networks-GlobalProtect met Azure AD](https://aad.portal.azure.com/)