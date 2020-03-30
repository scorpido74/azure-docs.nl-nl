---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met 4me | Microsoft Documenten'
description: Lees hoe u eenmalige aanmelding configureert tussen Azure Active Directory en 4me.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 983eecc6-41f8-49b7-b7f6-dcf833dde121
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3e8b753a9afb6b6d27466adb086717b52838e3a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72596291"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-4me"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met 4me

In deze zelfstudie leert u hoe u 4me integreert met Azure Active Directory (Azure AD). Wanneer u 4me integreert met Azure AD, u het:

* Beheer in Azure AD die toegang heeft tot 4me.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij 4me met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* 4me single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* 4me ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* 4me ondersteunt het **Just-In-Time** inrichten van gebruikers

## <a name="adding-4me-from-the-gallery"></a>4me toevoegen vanuit de galerie

Om de integratie van 4me in Microsoft Azure Active Directory te configureren, moet u 4me vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **4me** in de sectie **Toevoegen in de galeriesectie** in het zoekvak.
1. Selecteer **4me** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-4me"></a>Azure AD-aanmelding voor 4 me configureren en testen

Azure AD SSO configureren en testen met 4me met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in 4me.

Als u Azure AD SSO met 4me wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer 4me SSO](#configure-4me-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak 4me-testgebruiker](#create-4me-test-user)** - om een tegenhanger van B.Simon in 4me te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **4me-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: 

    | Omgeving| URL|
    |---|---|
    | PRODUCTIE | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: 

    | Omgeving| URL|
    |---|---|
    | PRODUCTIE | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [het klantenondersteuningsteam van 4me](mailto:support@4me.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. 4me-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. In aanvulling op bovenstaande, 4me applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name | Bronkenmerk|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer in de sectie **SAML-handtekeningcertificaat** de waarde voor **VINGERAFDRUK** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. Kopieer in de sectie **4me instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot 4me.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **4me** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-4me-sso"></a>4me SSO configureren

1. Meld u in een ander browservenster aan bij 4me als beheerder.

1. Klik linksboven op het logo **Settings** en klik in de balk links op **Single Sign-On**.

    ![4me-instellingen](./media/4me-tutorial/tutorial_4me_settings.png)

1. Voer op de pagina **Single Sign-On** de volgende stappen uit:

    ![4me singleasignon](./media/4me-tutorial/tutorial_4me_singlesignon.png)

    a. Selecteer de optie **Enabled**.

    b. Plak in het tekstvak **Remote logout URL** de waarde van de **afmeldings-URL** die u hebt gekopieerd uit de Microsoft Azure-portal.

    c. Plak onder de sectie **SAML** in het tekstvak **SAML SSO URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit de Microsoft Azure-portal.

    d. Plak in het tekstvak **Certificate fingerprint** de waarde voor de **vingerafdruk** gescheiden door een dubbele punt in een volgorde van dubbele waarden (AA:BB:CC:DD:EE:FF:GG:HH:II) die u hebt gekopieerd uit de Microsoft Azure-portal.

    e. Klik op **Opslaan**.

### <a name="create-4me-test-user"></a>4me-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in 4me. 4me biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in 4me bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

> [!Note]
> Als u handmatig een gebruiker wilt maken, neemt u contact op met [4me-ondersteuningsteam](mailto:support@4me.com).

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel 4me klikt, wordt u automatisch aangemeld bij de instantie van 4me waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer 4me met Azure AD](https://aad.portal.azure.com/)
