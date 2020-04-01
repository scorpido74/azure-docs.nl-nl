---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Catchpoint | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Catchpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56d34a331821ffbc3d0d4f2cf5e9b033f4011ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968508"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-catchpoint"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Catchpoint

In deze zelfstudie leert u hoe u Catchpoint integreren met Azure Active Directory (Azure AD). Wanneer u Catchpoint integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Catchpoint.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Catchpoint met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Abonnement op Catchpoint single sign (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Catchpoint ondersteunt **SP en IDP** geïnitieerde SSO
* Catchpoint ondersteunt **Just In Time-gebruikersinrichting**
* Zodra u Catchpoint hebt geconfigureerd, u sessiebeheer afdwingen, wat exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermt. Sessiebesturingselement strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-catchpoint-from-the-gallery"></a>Catchpoint toevoegen vanuit de galerie

Als u de integratie van Catchpoint in Azure AD wilt configureren, moet u Catchpoint vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Catchpoint** in het zoekvak in de sectie **Toevoegen in de sectie Toevoegen in de galerie.**
1. Selecteer **Catchpoint** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Azure AD-aanmelding voor Catchpoint configureren en testen

Azure AD SSO configureren en testen met Catchpoint met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Catchpoint.

Als u Azure AD SSO wilt configureren en testen met Catchpoint, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Catchpoint SSO configureren](#configure-catchpoint-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Catchpoint-testgebruiker maken](#create-catchpoint-test-user)** - om een tegenhanger van B.Simon in Catchpoint te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Catchpoint-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Stel enkel aanmelding met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de in de **iDP** gestarte modus wilt configureren, voert u de waarden in voor de volgende velden:

    a. Typ de URL in het tekstvak **Id:**`https://portal.catchpoint.com/SAML2`

    b. Typ in het tekstvak **Antwoord-URL** de URL: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ de URL in het tekstvak **AANmeldings-URL:**`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Catchpoint-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, Catchpoint applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name | Bronkenmerk|
    | ------------ | --------- |
    | naamruimte | user.assignedrole |

    > [!NOTE]
    > naamruimteclaim moet worden toegewezen met de accountnaam. Deze accountnaam moet worden ingesteld als de rollen in Azure AD die worden teruggegeven in SAML-respons. Raadpleeg dit [artikel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) om te leren hoe u de rollen instelt

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Catchpoint instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Catchpoint.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Catchpoint**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-catchpoint-sso"></a>Catchpoint SSO configureren

1. Meld u in een ander browservenster aan bij de Catchpoint-toepassing als beheerder.

1. Klik op het pictogram **Instellingen** en selecteer **SSO Identity Provider**.

    ![Catchpoint-configuratie](./media/catchpoint-tutorial/configuration1.png)

1. Voer op de pagina **Eén aanmelding** de volgende stappen uit:

    ![Catchpoint-configuratie](./media/catchpoint-tutorial/configuration2.png)

    1. Voer in het tekstvak **Naamruimte** een geldige naamruimtewaarde in.

    1. Voer in het tekstvak **URL van id-provider** de waarde van de **Azure Ad-id** in die u hebt gekopieerd uit de Azure-portal.

    1. Voer in het tekstvak **Url voor één aanmelding** de **url-waarde aanmelden** in, die u hebt gekopieerd van de Azure-portal.

    1. Open het gedownloade **certificaatbestand (Base64)** in kladblok, kopieer de inhoud van het certificaatbestand en plak het in het tekstvak **certificaat.**

    1. U ook de **XML met federatiemetagegevens uploaden** door op de optie **Metagegevens uploaden** te klikken.

    1. Klik op **Opslaan**.

### <a name="create-catchpoint-test-user"></a>Catchpoint-testgebruiker maken

In deze sectie wordt een gebruiker genaamd Britta Simon gemaakt in Catchpoint. Catchpoint ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Catchpoint, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Catchpoint in het toegangspaneel klikt, moet u automatisch worden aangemeld bij het Catchpoint waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

> [!NOTE]
> Wanneer u zich aanmeldt bij de Catchpoint-toepassing via de aanmeldingspagina, voert u na het verstrekken van **Catchpoint-referenties**de geldige **naamruimtewaarde** in het tekstvak **Bedrijfsreferenties (SSO)** in en klikt u op **Aanmelden**.

![Catchpoint-configuratie](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Catchpoint uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)