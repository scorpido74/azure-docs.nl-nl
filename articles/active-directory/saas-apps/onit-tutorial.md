---
title: 'Zelfstudie: SSO-integratie (Single Sign-On) van Azure Active Directory met Onit | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Onit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e908cb76a57f027494230edc648b69da0730ac27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "70164243"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-onit"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Onit

In deze zelfstudie leert u hoe U Onit integreren met Azure Active Directory (Azure AD). Wanneer u Onit integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Onit.
* Stel uw gebruikers in om automatisch te worden aangemeld bij Onit met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Onit single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Onit steunt **SP** geïnitieerdse SSO

## <a name="adding-onit-from-the-gallery"></a>Onit toevoegen vanuit de galerie

Als u de integratie van Onit in Azure AD wilt configureren, moet u Onit vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Onit** in het zoekvak in de sectie **Toevoegen in de sectie Toevoegen in de galerie.**
1. Selecteer **Onit** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-onit"></a>Azure AD-aanmelding voor Onit configureren en testen

Azure AD SSO configureren en testen met Onit met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Onit.

Als u Azure AD SSO met Onit wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Onit SSO](#configure-onit-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Onit-testgebruiker maken](#create-onit-test-user)** - om een tegenhanger van B.Simon in Onit te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Onit-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<sub-domain>.onit.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<sub-domain>.onit.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [het ondersteuningsteam van Onit Client](https://www.onit.com/support) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer in de sectie **SAML-ondertekeningscertificaat** de **waarde voor duimafdruk** en sla deze op uw computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. Kopieer in de sectie **Onit instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Onit.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Onit**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-onit-sso"></a>Onit SSO configureren

1. Log in een ander browservenster in op uw Onit-bedrijfssite als beheerder.

2. Klik in het menu bovenaan op **Beheer**.
   
    ![Beheer](./media/onit-tutorial/IC791174.png "Beheer")

3. Klik **op Corporation bewerken**.
   
    ![Vennootschap bewerken](./media/onit-tutorial/IC791175.png "Vennootschap bewerken")
   
4. Klik op het tabblad **Beveiliging**.
    
    ![Bedrijfsgegevens bewerken](./media/onit-tutorial/IC791176.png "Bedrijfsgegevens bewerken")

5. Voer op het tabblad **Beveiliging** de volgende stappen uit:

    ![Eén aanmelding](./media/onit-tutorial/IC791177.png "Eenmalige aanmelding")

    a. Selecteer Als **verificatiestrategie**de optie **Eén aanmelding en wachtwoord**.
    
    b. Plak in **het tekstvak URL-url van Idp-doel** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd van azure-portal.

    c. Plak **in het tekstvak VAN IDP-uitloging** de waarde van de URL van **Afmelden**, die u hebt gekopieerd van azure-portal.

    d. Plak in **het tekstvak Idp Cert Fingerprint (SHA1)** de **thumbprint-waarde** van het certificaat, die u hebt gekopieerd van azure-portal.

### <a name="create-onit-test-user"></a>Onit-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Onit, moeten ze worden ingericht in Onit. In het geval van Onit is inrichten een handmatige taak.

**Voer de volgende stappen uit om de inrichting van gebruikers te configureren:**

1. Meld u aan op uw Site van Het Bedrijf **van Onit** als beheerder.

2. Klik **op Gebruiker toevoegen**.

    ![Beheer](./media/onit-tutorial/IC791180.png "Beheer")

3. Voer op de pagina **Gebruiker toevoegen** de volgende stappen uit:

    ![Gebruiker toevoegen](./media/onit-tutorial/IC791181.png "Gebruiker toevoegen")

    a. Typ de **naam** en het **e-mailadres** van een geldig Azure AD-account dat u wilt inrichten in de gerelateerde tekstvakken.

    b. Klik **op Maken**.

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om het account te bevestigen voordat het actief wordt.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Onit in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Onit waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Onit met Azure AD](https://aad.portal.azure.com/)