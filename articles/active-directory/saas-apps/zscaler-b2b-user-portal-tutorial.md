---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met Zscaler B2B-gebruikersportal | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zscaler B2B-gebruikersportal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 75b90449-095d-4e47-a1fd-8b5cbbfe7e3d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c28af94c92fa56d5217aee91053521d2c12b0aef
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668928"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-b2b-user-portal"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Zscaler B2B User Portal

In deze zelfstudie leert u hoe u Zscaler B2B-gebruikersportal integreren met Azure Active Directory (Azure AD). Wanneer u Zscaler B2B-gebruikersportal integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Zscaler B2B-gebruikersportal.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij De B2B-gebruikersportal van Zscaler met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Zscaler B2B User Portal single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Zscaler B2B User Portal ondersteunt **IDP** geïnitieerde SSO

* Zscaler B2B User Portal ondersteunt **Just In Time** gebruikersinrichting

* Zodra u De B2B-gebruikersportal van Zscaler hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-zscaler-b2b-user-portal-from-the-gallery"></a>Zscaler B2B-gebruikersportal toevoegen vanuit de galerie

Als u de integratie van Zscaler B2B-gebruikersportal in Azure AD wilt configureren, moet u Zscaler B2B-gebruikersportal vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Zscaler B2B-gebruikersportal** in het zoekvak in de sectie **Toevoegen vanuit de galerie.**
1. Selecteer **Zscaler B2B-gebruikersportal** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-b2b-user-portal"></a>Azure AD-singlesign-aan configureren en testen voor Zscaler B2B-gebruikersportal

Azure AD SSO configureren en testen met Zscaler B2B-gebruikersportal met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Zscaler B2B-gebruikersportal.

Als u Azure AD SSO wilt configureren en testen met Zscaler B2B-gebruikersportal, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Zscaler B2B User Portal SSO](#configure-zscaler-b2b-user-portal-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. Maak de gebruiker van **[de Zscaler B2B-gebruikersportal](#create-zscaler-b2b-user-portal-test-user)** - om een tegenhanger van B.Simon te hebben in Zscaler B2B-gebruikersportal die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de integratiepagina van de **Zscaler B2B-gebruikersportaltoepassing** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **Eén aanmelding instellen met SAML** de waarden in voor de volgende velden:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://samlsp.private.zscaler.com/auth/metadata/<UniqueID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://samlsp.private.zscaler.com/auth/login?domain=EXAMPLE`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met [het Ondersteuningsteam van Zscaler B2B User Portal Client](https://help.zscaler.com/) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Zscaler B2B-gebruikersportal** instellen de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Zscaler B2B-gebruikersportal.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Zscaler B2B-gebruikersportal**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-zscaler-b2b-user-portal-sso"></a>Zscaler B2B-gebruikersportal SSO configureren

1. Open een nieuw webbrowservenster en meld u aan bij uw bedrijf van de Zscaler B2B-gebruikersportal als beheerder en voer de volgende stappen uit:

1. Klik aan de linkerkant van het menu op **Beheer** en navigeer naar **de** sectie VERIFICATIE op **IdP-configuratie**.

    ![Beheer van de beheerder van de beheerder van de privétoegang van Zscaler](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-administration.png)

1. Klik in de rechterbovenhoek op **IdP-configuratie toevoegen**. 

    ![Zscaler Private Access Administrator idp](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-idp.png)

1. Voer op de pagina **IdP-configuratie toevoegen** de volgende stappen uit:
 
    ![Zscaler Private Access Administrator selecteren](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-select.png)

    a. Klik **op Bestand selecteren** om het gedownloade bestand met ametagegevens van Azure AD te uploaden in het veld **Metagegevensbestand uploaden van idp.**

    b. Het leest de **IdP metadata** van Azure AD en vult alle velden informatie zoals hieronder weergegeven.

    ![Zscaler Private Access Administrator config](./media/zscaler-b2b-user-tutorial/config.png)

    c. Selecteer uw domein in het veld **Domeinen.**
    
    d. Klik op **Opslaan**.

### <a name="create-zscaler-b2b-user-portal-test-user"></a>Zscaler B2B-gebruikersportaltestgebruiker maken

In deze sectie wordt een gebruiker genaamd Britta Simon gemaakt in Zscaler B2B User Portal. Zscaler B2B User Portal ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Zscaler B2B-gebruikersportal, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Zscaler B2B-gebruikersportal in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Zscaler B2B-gebruikersportal waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Zscaler B2B-gebruikersportal met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)