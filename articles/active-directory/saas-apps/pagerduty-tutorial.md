---
title: 'Zelfstudie: Azure Active Directory-integratie met PagerDuty | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 99d9988a02b046562c2517df8a81b8ef5c778ef2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683519"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met PagerDuty

In deze zelfstudie leert u hoe u PagerDuty integreren met Azure Active Directory (Azure AD). Wanneer u PagerDuty integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot PagerDuty.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij PagerDuty met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Abonnement op PagerDuty single sign-on (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* PagerDuty ondersteunt **door SP** geïnitieerde SSO
* Zodra u PagerDuty hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-pagerduty-from-the-gallery"></a>PagerDuty toevoegen vanuit de galerie

Als u de integratie van PagerDuty in Azure AD wilt configureren, moet u PagerDuty vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **PagerDuty** in het zoekvak in de sectie **Toevoegen in de galerijsectie.**
1. Selecteer **PagerDuty** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>Azure AD-aanmelding voor PagerDuty configureren en testen

Azure AD SSO configureren en testen met PagerDuty met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in PagerDuty.

Als u Azure AD SSO wilt configureren en testen met PagerDuty, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[PagerDuty SSO configureren](#configure-pagerduty-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[PagerDuty-testgebruiker maken](#create-pagerduty-test-user)** - om een tegenhanger van B.Simon in PagerDuty te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **PagerDuty-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenant-name>.pagerduty.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<tenant-name>.pagerduty.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met [het ondersteuningsteam van PagerDuty Client](https://www.pagerduty.com/support/) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **PagerDuty instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot PagerDuty.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **PagerDuty**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-pagerduty-sso"></a>PagerDuty SSO configureren

1. Meld u in een ander browservenster aan op uw Pagerduty-bedrijfssite als beheerder.

2. Klik in het menu bovenaan op **Accountinstellingen**.

    ![Accountinstellingen](./media/pagerduty-tutorial/ic778535.png "Accountinstellingen")

3. Klik **op Eén aanmelding**.

    ![Eenmalige aanmelding](./media/pagerduty-tutorial/ic778536.png "Eenmalige aanmelding")

4. Voer op de pagina **SSO inschakelen** de volgende stappen uit:

    ![Eén aanmelding inschakelen](./media/pagerduty-tutorial/ic778537.png "Eén aanmelding inschakelen")

    a. Open uw basis-64-gecodeerd certificaat dat is gedownload van de Azure-portal in kladblok, kopieer de inhoud ervan in uw klembord en plak het vervolgens op het tekstvak **x.509-certificaat**
  
    b. Plak in het tekstvak **Aanmeldings-URL** de **aanmeldings-URL** die u in de Azure-portal hebt gekopieerd.
  
    c. Plak in het tekstvak **Afmeldings-URL** de **afmeldings-URL** die u in de Azure-portal hebt gekopieerd.

    d. Selecteer **Gebruikersnaam/wachtwoord aanmelding toestaan**.

    e. Schakel selectievakje Schakel het selectievakje **SCHAKEL NAUWKEURIGE verificatiecontextvergelijking** vereisen in.

    f. Klik **op Wijzigingen opslaan**.

### <a name="create-pagerduty-test-user"></a>PagerDuty-testgebruiker maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij PagerDuty, moeten ze zijn ingericht in PagerDuty. In het geval van PagerDuty is inrichten een handmatige taak.

> [!NOTE]
> U alle andere hulpprogramma's voor het maken van gebruikersaccounts voor gebruikersaccounts van Pagerduty gebruiken die door Pagerduty worden verstrekt om Azure Active Directory-gebruikersaccounts in te richten.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw **Pagerduty-tenant.**

2. Klik in het menu bovenaan op **Gebruikers**.

3. Klik **op Gebruikers toevoegen**.
   
    ![Gebruikers toevoegen](./media/pagerduty-tutorial/ic778539.png "Gebruikers toevoegen")

4.  Voer **in het** dialoogvenster Uw team uitnodigen de volgende stappen uit:
   
    ![Nodig je team uit](./media/pagerduty-tutorial/ic778540.png "Nodig je team uit")

    a. Typ de **voor- en achternaam** van de gebruiker zoals **B.Simon**. 
   
    b. Voer **e-mailadres** van de gebruiker in, zoals **\@b.simon contoso.com**.
   
    c. Klik **op Toevoegen**en klik vervolgens op **Uitnodigingen verzenden**.
   
    > [!NOTE]
    > Alle toegevoegde gebruikers ontvangen een uitnodiging om een PagerDuty-account aan te maken.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel PagerDuty in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de PagerDuty waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [PagerDuty uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [PagerDuty beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

