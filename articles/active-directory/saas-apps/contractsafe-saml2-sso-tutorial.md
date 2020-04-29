---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met ContractSafe Saml2 SSO | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en ContractSafe Saml2 SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9d8c9eba-6a90-4c8f-b387-a6ead4af00af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ab2dc71f39164988e0d229fc994548a00447986
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77185617"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Zelf studie: Azure Active Directory eenmalige aanmelding (SSO) integreren met ContractSafe Saml2 SSO

In deze zelf studie leert u hoe u ContractSafe Saml2 SSO integreert met Azure Active Directory (Azure AD). Wanneer u ContractSafe Saml2 SSO integreert met Azure AD, kunt u het volgende doen:

* Bepaal wie toegang heeft tot ContractSafe Saml2 SSO in azure AD.
* Stel uw gebruikers in staat om zich automatisch aan te melden bij ContractSafe Saml2 SSO met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van SaaS-apps (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een ContractSafe Saml2 SSO-abonnement waarvoor SSO is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. ContractSafe Saml2 SSO ondersteunt door **IDP**geïnitieerde SSO.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>ContractSafe Saml2 SSO toevoegen vanuit de galerie

Als u de integratie van ContractSafe Saml2 SSO wilt configureren in azure AD, moet u ContractSafe Saml2 SSO vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ **ContractSafe SAML2 SSO** in het zoekvak in de sectie **toevoegen vanuit de galerie** .
1. Selecteer **ContractSafe SAML2 SSO** in het deel venster resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Azure AD SSO configureren en testen voor ContractSafe Saml2 SSO

Azure AD SSO met ContractSafe Saml2 SSO configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ContractSafe Saml2 SSO.

Als u Azure AD SSO wilt configureren en testen met ContractSafe Saml2 SSO, voltooit u de volgende bouw stenen:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
   * [Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user) om Azure AD SSO te testen met behulp van het account **B. Simon** .
   * [Wijs de gebruiker van Azure AD-test](#assign-the-azure-ad-test-user) toe om **B. Simon** in te scha kelen voor gebruik met Azure AD SSO.

1. [CONFIGUREER SSO van ContractSafe Saml2](#configure-contractsafe-saml2-sso) om de SSO-instellingen aan de kant van de toepassing te configureren.
   * [Maak een ContractSafe SAML2 SSO-test gebruiker](#create-a-contractsafe-saml2-sso-test-user) voor een tegen hanger van **B. Simon** in ContractSafe Saml2 SSO die is gekoppeld aan de Azure AD-representatie van de gebruiker.
2. [Test SSO](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Voer de volgende stappen uit om Azure AD SSO in te scha kelen in de Azure Portal:

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **ContractSafe Saml2 SSO** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het pictogram bewerken (pen) voor de **basis configuratie van SAML** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **eenmalige aanmelding met SAML instellen** de volgende waarden in de bijbehorende velden in:

    a. Voer in het tekstvak **id** een URL in met behulp van de volgende indeling:`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. Voer in het tekstvak **antwoord-URL** een URL in met behulp van de volgende indeling:`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteunings team van de ContractSafe SAML2 SSO-client](mailto:support@contractsafe.com) om deze waarden op te halen. U kunt ook verwijzen naar de indelingen die worden weer gegeven in de sectie **basis configuratie van SAML** in de Azure Portal.

1. ContractSafe Saml2 SSO verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Algemene standaard kenmerken](common/default-attributes.png)

1. Naast de standaard kenmerken verwacht de SSO-toepassing ContractSafe Saml2 nog enkele kenmerken die in het SAML-antwoord worden door gegeven. Deze kenmerken zijn vooraf ingevuld, maar u kunt ze controleren aan de hand van uw vereisten. De volgende lijst bevat de extra kenmerken.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | e-mail adres | user.userprincipalname |
    | e-mail | User. onpremisesuserprincipalname |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML**. Selecteer **downloaden** om het certificaat te downloaden en sla het op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer de gewenste URL ('s) in het gedeelte **SSO van ContractSafe Saml2 instellen** op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam **B. Simon**.

1. Selecteer **Azure Active Directory**in het linkerdeel venster van de Azure Portal. Selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** een e-mail adres in `username@companydomain.extension` met de notatie. Een voorbeeld is `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Selecteer **Maken**.

## <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u **B. Simon** in om Azure SSO te gebruiken door toegang te verlenen aan CONTRACTSAFE Saml2 SSO.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **ContractSafe SAML2 SSO**.
1. Zoek de sectie **beheren** op de pagina overzicht van de app en selecteer vervolgens **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

   ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst **gebruikers** . Selecteer vervolgens de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens de knop **selecteren** onder aan het scherm.
1. Selecteer de knop **toewijzen** in het dialoog venster **toewijzing toevoegen** .

## <a name="configure-contractsafe-saml2-sso"></a>ContractSafe Saml2 SSO configureren

Als u SSO wilt configureren op de **ContractSafe SAML2 SSO** -zijde, moet u het gedownloade **XML-bestand met federatieve meta gegevens** en de juiste gekopieerde url's verzenden van de Azure Portal naar het [ondersteunings team ContractSafe Saml2 SSO](mailto:support@contractsafe.com). Het team is verantwoordelijk voor het instellen van de SAML SSO-verbinding aan beide zijden.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>Een ContractSafe Saml2 SSO-test gebruiker maken

Maak een gebruiker met de naam B. Simon in ContractSafe Saml2 SSO. Werk met het [ContractSafe SAML2 SSO-ondersteunings team](mailto:support@contractsafe.com) om de gebruikers toe te voegen in het SSO-platform ContractSafe Saml2. Gebruikers moeten worden gemaakt en geactiveerd voordat u SSO kunt gebruiken.

## <a name="test-sso"></a>SSO testen

Test uw Azure AD SSO-configuratie met behulp van het toegangs venster. Wanneer u de SSO-tegel ContractSafe Saml2 in het toegangs venster selecteert, moet u automatisch worden aangemeld bij de ContractSafe Saml2 SSO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer ContractSafe Saml2 SSO uit met Azure AD](https://aad.portal.azure.com/)
