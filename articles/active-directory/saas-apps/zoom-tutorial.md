---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Zoom | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Zoom.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f9d727154adf0a2099d7a9144c109cef9c91238
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "70743971"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Zelfstudie: Azure Active Directory-integratie (SSO) met Zoom

In deze zelfstudie leert u hoe u Zoom integreert met Azure Active Directory (Azure AD). Wanneer u Zoom integreert met Azure AD, u het als:

* Beheer in Azure AD wie toegang heeft tot Zoom.
* Stel uw gebruikers in om automatisch te worden aangemeld bij Zoomen met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Inzoom op eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Zoom ondersteunt **SP** geïnitieerde SSO en 
* Zoom ondersteunt [ **geautomatiseerde** gebruikersinrichting](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial).

## <a name="adding-zoom-from-the-gallery"></a>Zoom toevoegen vanuit de galerie

Voor het configureren van de integratie van Zoom in Azure AD moet u Zoom uit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ Inzoomen in het zoekvak in de sectie **Toevoegen in de sectie Toevoegen in de galerie.** **Zoom**
1. Selecteer **Zoomen** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Azure AD-aanmelding voor Zoom configureren en testen

Azure AD SSO configureren en testen met Zoom met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Zoom.

Als u Azure AD SSO met Zoom wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
2. **[ZoomsSO configureren](#configure-zoom-sso)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
    1. **[Zoomtestgebruiker maken](#create-zoom-test-user)** - om een tegenhanger van B.Simon in Zoom te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Zoom-toepassingsintegratie** de sectie **Beheren** en selecteer Enkele **aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.zoom.us`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `<companyname>.zoom.us`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [Zoom-ondersteuningsteam](https://support.zoom.us/hc/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Zoom instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

> [!NOTE]
> Zie [Rolclaim configureren die is uitgegeven in het SAML-token voor bedrijfstoepassingen voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)meer informatie over het configureren van rol in Azure AD.

> [!NOTE]
> Zoom zou kunnen verwachten dat een groep claim in de SAML payload. Als u groepen hebt gemaakt, neemt u contact op met het [ondersteuningsteam van de client zoom](https://support.zoom.us/hc/) met de groepsgegevens, zodat ze de groepsinformatie op hun einde kunnen configureren. U moet ook de object-id verstrekken aan [het ondersteuningsteam van de client zoom,](https://support.zoom.us/hc/) zodat ze de object-id op hun einde kunnen configureren. Zie [Zoomen configureren met Azure](https://support.zoom.us/hc/articles/115005887566)voor de object-id.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Zoom.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Zoomen**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-zoom-sso"></a>ZoomsSO configureren

1. Meld u in een ander browservenster aan bij uw site van het Zoom-bedrijf als beheerder.

2. Klik op het tabblad **Eenmalige aanmelding**.

    ![Tabblad Eenmalig aanmelden](./media/zoom-tutorial/ic784700.png "Eenmalige aanmelding")

3. Klik op het tabblad **Beveiligingsbeheer** en ga vervolgens naar de instellingen voor **Eenmalige aanmelding**.

4. Voer in de sectie Eenmalige aanmelding de volgende stappen uit:

    ![Sectie eenmalig aanmelden](./media/zoom-tutorial/ic784701.png "Eenmalige aanmelding")

    a. Plak in het tekstvak **Aanmeldingspagina-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    b. Voor **de URL-waarde van de aanmeldingspagina** moet u naar de Azure-portal gaan en links op **Azure Active Directory** klikken en vervolgens naar **App-registraties**navigeren.

    ![De knop Azure Active Directory](./media/zoom-tutorial/appreg.png)

    c. Klik op **Eindpunten**

    ![De knop Eindpunt](./media/zoom-tutorial/endpoint.png)

    d. Kopieer het **EINDPUNT SAML-P SIGN-OUT** EN plak het in het **tekstvak van de aanmeldingspagina.**

    ![De knop Eindpunt kopiëren](./media/zoom-tutorial/endpoint1.png)

    e. Open het base-64 gecodeerde certificaat in Kladblok, kopieer de inhoud ervan naar het klembord en plak het in het tekstvak **Id-providercertificaat**.

    f. Plak in het tekstvak **Uitgever** de waarde van **Azure AD-id** die u hebt gekopieerd vanuit azure-portal. 

    g. Klik op **Opslaan**.

    > [!NOTE]
    > Ga voor meer informatie naar de zoomdocumentatie[https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Zoom-testgebruiker maken

Het doel van deze sectie is het creëren van een gebruiker genaamd B.Simon in Zoom. Zoom ondersteunt automatische gebruikersinrichting, die standaard is ingeschakeld. U kunt [hier](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

> [!NOTE]
> Als u handmatig een gebruiker wilt maken, moet u contact opnemen met [het ondersteuningsteam van De Client van Zoom](https://support.zoom.us/hc/)

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Zoom in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Zoom waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Zoomen met Azure AD proberen](https://aad.portal.azure.com/)
