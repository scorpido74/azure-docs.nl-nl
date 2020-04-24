---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met SAP-Cloud voor klant | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Cloud for Customer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 837787d375a7570b7daf0a149960ca0020bcdced
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72264062"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met SAP-Cloud voor klanten

In deze zelf studie leert u hoe u de SAP-Cloud kunt integreren voor de klant met Azure Active Directory (Azure AD). Wanneer u de SAP-Cloud voor klanten integreert met Azure AD, kunt u het volgende doen:

* Beheer in azure AD die toegang heeft tot de SAP-Cloud voor de klant.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij een SAP-Cloud voor klanten met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* SAP-Cloud voor Customer SSO (single sign-on) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* SAP Cloud for Customer ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>SAP Cloud for Customer uit de galerie toevoegen

Voor het configureren van de integratie van SAP Cloud for Customer met Azure AD moet u SAP Cloud for Customer uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SAP-Cloud voor klant** in het zoekvak.
1. Selecteer **SAP-Cloud voor klant** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>Eenmalige aanmelding voor Azure AD voor de SAP-Cloud configureren en testen voor klant

Azure AD SSO configureren en testen met SAP-Cloud voor klanten met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de SAP-Cloud voor de klant.

Als u Azure AD SSO wilt configureren en testen met SAP Cloud voor klant, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SAP-Cloud configureren voor Customer SSO](#configure-sap-cloud-for-customer-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een SAP-Cloud voor de gebruiker van de klant test](#create-sap-cloud-for-customer-test-user)** , zodat deze een soort is van B. Simon in de SAP-Cloud voor de klant die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina **SAP-Cloud voor de integratie van klant** toepassingen, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<server name>.crm.ondemand.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [SAP Cloud for Customer-ondersteuningsteam](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. SAP-Cloud voor klant toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![installatiekopie](common/edit-attribute.png)

1. Voer in de sectie **Gebruikerskenmerken** in het dialoogvenster **Gebruikerskenmerken en claims** de volgende stappen uit:

    a. Klik op **pictogram bewerken** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![installatiekopie](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![installatiekopie](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Selecteer **Transformatie** als **bron**.

    c. Selecteer in de lijst **Transformatie****ExtractMailPrefix()**.

    d. Selecteer in de lijst **Parameter 1** het gebruikerskenmerk dat u wilt gebruiken voor uw implementatie.
    Als u bijvoorbeeld de werknemer-id wilt gebruiken als unieke gebruikers-id en u de waarde van het kenmerk in de ExtensionAttribute2 hebt opgeslagen, selecteert u vervolgens user.extensionattribute2.

    e. Klik op **Opslaan**.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **SAP-Cloud instellen voor de klant** kopieert u de juiste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de SAP-Cloud voor de klant.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **SAP-Cloud voor klant**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-sap-cloud-for-customer-sso"></a>SAP-Cloud configureren voor Customer SSO

1. Open een nieuw webbrowser venster en meld u aan bij uw SAP-Cloud voor de klant-bedrijfs site als beheerder.

2. Klik aan de linkerkant van het menu op **id-providers** > **zakelijke id-providers** > **toevoegen** en voeg in het pop-upvenster de naam van de ID-provider toe, zoals **Azure AD**. Klik vervolgens op **Opslaan** en vervolgens op **SAML 2,0-configuratie**.

    ![SAP-configuratie](./media/sap-customer-cloud-tutorial/configure01.png)

3. Voer de volgende stappen uit in de sectie **SAML 2,0-configuratie** :

    ![SAP-configuratie](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Klik op **Bladeren** om het XML-bestand met federatieve meta gegevens te uploaden, dat u hebt gedownload van Azure Portal.

    b. Zodra het XML-bestand is geüpload, worden de onderstaande waarden automatisch ingevuld en vervolgens klikt u op **Opslaan**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Een SAP Cloud for Customer-testgebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij de SAP-Cloud voor klanten, moeten ze worden ingericht in een SAP-Cloud voor de klant. In SAP-Cloud voor klant is inrichting een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij de SAP-Cloud voor de klant als beveiligings beheerder.

2. Klik aan de linkerkant van het menu op **gebruikers & autorisaties** > **gebruikers beheer** > **gebruiker toevoegen**.

    ![SAP-configuratie](./media/sap-customer-cloud-tutorial/configure03.png)

3. Voer de volgende stappen uit in de sectie **nieuwe gebruiker toevoegen** :

    ![SAP-configuratie](./media/sap-customer-cloud-tutorial/configure04.png)

    a. Voer in het tekstvak **voor naam** de naam van de gebruiker in zoals **B**.

    b. Voer in het tekstvak **laatste naam** de naam in van de gebruiker, zoals **Simon**.

    c. Voer in het tekstvak **e-mail** het e-mail adres van `B.Simon@contoso.com`de gebruiker in.

    d. Voer in het tekstvak **aanmeldings naam** de naam van de gebruiker in zoals **B. Simon**.

    e. Selecteer het **gebruikers type** volgens uw vereiste.

    f. Selecteer de optie **account activering** conform uw vereiste.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SAP Cloud for Customer klikt in het toegangsvenster, wordt u automatisch aangemeld bij het exemplaar van SAP Cloud for Customer waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Extra resources

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [SAP-Cloud voor klanten uitproberen met Azure AD](https://aad.portal.azure.com/)

