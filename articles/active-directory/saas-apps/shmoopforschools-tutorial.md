---
title: 'Zelfstudie: Azure Active Directory-integratie met Shmoop For Schools | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Shmoop For Schools.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06ce2e51f72b9b4f709bf26ce6d92ad3861d36db
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74081650"
---
# <a name="tutorial-integrate-shmoop-for-schools-with-azure-active-directory"></a>Zelfstudie: Shmoop voor scholen integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Shmoop For Schools integreert met Azure Active Directory (Azure AD). Wanneer u Shmoop For Schools integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Shmoop For Schools.
* Stel uw gebruikers in om automatisch te worden aangemeld bij Shmoop For Schools met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Shmoop For Schools single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Shmoop For Schools steunt **SP** geïnitieerde SSO
* Shmoop For Schools ondersteunt **Just In Time** gebruikersinrichting

## <a name="adding-shmoop-for-schools-from-the-gallery"></a>Het toevoegen van Shmoop voor scholen uit de galerij

Als u de integratie van Shmoop For Schools in Azure AD wilt configureren, moet u Shmoop For Schools uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Shmoop Voor scholen** in het zoekvak in de sectie **Toevoegen in de galerijsectie.**
1. Selecteer **Shmoop For Schools** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-shmoop-for-schools"></a>Azure AD-eenmaligaanmelding voor Shmoop for Schools configureren en testen

Azure AD SSO configureren en testen met Shmoop For Schools met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Shmoop For Schools.

Voer de volgende bouwstenen uit om Azure AD SSO te configureren en te testen met Shmoop For Schools:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
2. **[Configureer Shmoop For Schools SSO](#configure-shmoop-for-schools-sso)** - om de instellingen voor enkele aanmelding aan de toepassingszijde te configureren.
    * **[Shmoop For Schools test gebruiker](#create-shmoop-for-schools-test-user)** - om een tegenhanger van B.Simon in Shmoop Voor scholen die is gekoppeld aan de Azure AD vertegenwoordiging van de gebruiker.
3. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Shmoop For** Schools-toepassingsintegratie de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [Shmoop For Schools Client support team](mailto:support@shmoop.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Shmoop For Schools-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

    > [!NOTE]
    > Shmoop for School ondersteunt twee rollen voor gebruikers: **Docent** en **Student**. Stel deze rollen in Azure AD in, zodat gebruikers de juiste rollen kunnen toegewezen krijgen. Zie [hier](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)voor meer informatie over het configureren van rollen in Azure AD.

1. In aanvulling op bovenstaande, Shmoop For Schools applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name |  Bronkenmerk|
    | --------- | --------------- |
    | role      | user.assignedroles |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Shmoop For Schools.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Shmoop For Schools**in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-shmoop-for-schools-sso"></a>Shmoop configureren voor scholen SSO

Als u eenmalige aanmelding wilt configureren op de kant **van Shmoop For Schools,** moet u het ondersteuningsteam van de **app-federatie-metagegevens** naar [het ondersteuningsteam van Shmoop For Schools](mailto:support@shmoop.com)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-shmoop-for-schools-test-user"></a>Shmoop For Schools testgebruiker maken

In deze sectie wordt een gebruiker genaamd B.Simon gemaakt in Shmoop For Schools. Shmoop For Schools ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Shmoop For Schools, wordt er een nieuwe gemaakt na verificatie.

> [!NOTE]
> Als u handmatig een gebruiker wilt maken, neemt u contact op met het [ondersteuningsteam shmoop for schools.](mailto:support@shmoop.com)

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Shmoop For Schools in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Shmoop For Schools waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Shmoop voor scholen met Azure AD](https://aad.portal.azure.com/)