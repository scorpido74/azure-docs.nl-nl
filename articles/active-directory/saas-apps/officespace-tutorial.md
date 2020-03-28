---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met OfficeSpace Software | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en OfficeSpace Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80712c7f59845287006c1699524573c6094498b3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561708"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met OfficeSpace Software

In deze zelfstudie leert u hoe u OfficeSpace-software integreren met Azure Active Directory (Azure AD). Wanneer u OfficeSpace-software integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot OfficeSpace Software.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij OfficeSpace Software met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Abonnement met eenmalige aanmelding (OfficeSpace Software) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* OfficeSpace Software ondersteunt **SP** geïnitieerde SSO


* OfficeSpace Software ondersteunt **Just In Time** gebruikersinrichting


## <a name="adding-officespace-software-from-the-gallery"></a>OfficeSpace-software toevoegen vanuit de galerie

Als u de integratie van OfficeSpace-software in Azure AD wilt configureren, moet u OfficeSpace-software uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **OfficeSpace-software** in het zoekvak in de sectie **Toevoegen vanuit de galerie.**
1. Selecteer **OfficeSpace-software** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-officespace-software"></a>Azure AD-aanmelding voor OfficeSpace-software configureren en testen

Azure AD SSO configureren en testen met OfficeSpace Software met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in OfficeSpace-software.

Als u Azure AD SSO wilt configureren en testen met OfficeSpace Software, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[OfficeSpace Software SSO configureren](#configure-officespace-software-sso)** - om de instellingen voor één aanmelding aan toepassingszijde te configureren.
    1. **[OfficeSpace Software-testgebruiker maken](#create-officespace-software-test-user)** - om een tegenhanger van B.Simon in OfficeSpace-software te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **OfficeSpace** Software-toepassingsintegratie de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `<company name>.officespacesoftware.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [het ondersteuningsteam van OfficeSpace Software Client](mailto:support@officespacesoftware.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. OfficeSpace Software-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. OfficeSpace Software-toepassing verwacht **dat naam-id** wordt toegewezen met **user.mail,** dus u moet de toewijzing van het kenmerk bewerken door te klikken op pictogram **Bewerken** en de toewijzing van het kenmerk wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Naast bovenstaande, OfficeSpace Software applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw eis.

    | Name | Bronkenmerk|
    | ---------------| --------------- |
    | e-mail | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer in de sectie **SAML-ondertekeningscertificaat** de **waarde voor duimafdruk** en sla deze op uw computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. Kopieer in de sectie **OfficeSpace Software instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot OfficeSpace Software.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **OfficeSpace-software**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="configure-officespace-software-sso"></a>OfficeSpace-software SSO configureren

1. Meld u in een ander browservenster aan bij uw OfficeSpace Software-tenant als beheerder.

2. Ga naar **Instellingen** en klik op **Connectors**.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Klik **op SAML-verificatie**.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/officespace-tutorial/tutorial_officespace_003.png)

4. Voer in het gedeelte **SAML-verificatie** de volgende stappen uit:

    ![Eenmalige aanmelding aan app-zijde configureren](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. Plak in het tekstvak van de **uitlogprovider url** de waarde van **de URL van afmelden** die u hebt gekopieerd van azure-portal.

    b. Plak in het tekstvak **url-url** van client idp de waarde van **de aanmeldings-URL** die u hebt gekopieerd van azure-portal.

    c. Plak de **thumbprint-waarde** die u hebt gekopieerd van azure-portal, in het vingerafdruktekstvak van het **Client IDP-certificaat.** 

    d. Klik **op Instellingen opslaan**.

### <a name="create-officespace-software-test-user"></a>Testgebruiker van OfficeSpace-software maken

In deze sectie wordt een gebruiker genaamd B.Simon gemaakt in OfficeSpace Software. OfficeSpace Software ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in OfficeSpace Software, wordt er een nieuwe gemaakt na verificatie.

> [!NOTE]
> Als u handmatig een gebruiker wilt maken, moet u contact opnemen met [het ondersteuningsteam van OfficeSpace Software.](mailto:support@officespacesoftware.com)

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel OfficeSpace-software in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de OfficeSpace-software waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [OfficeSpace-software uitproberen met Azure AD](https://aad.portal.azure.com/)

