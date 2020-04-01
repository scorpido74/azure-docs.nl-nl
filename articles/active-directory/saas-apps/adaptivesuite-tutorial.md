---
title: 'Zelfstudie: Azure Active Directory-integratie met Adaptive Insights | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Adaptive Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf05fc30a2b111a12c0b8e131db5117ec784075b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232127"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Zelfstudie: Adaptive Insights integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Adaptive Insights integreren met Azure Active Directory (Azure AD). Wanneer u Adaptive Insights integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Adaptive Insights.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Adaptive Insights met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Abonnement met één aanmelding (Adaptive Insights) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Adaptive Insights ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-adaptive-insights-from-the-gallery"></a>Adaptive Insights toevoegen vanuit de galerie

Als u de integratie van Adaptive Insights in Azure AD wilt configureren, moet u Adaptive Insights vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Adaptive Insights** in het zoekvak in de sectie Toevoegen in **de galerie.**
1. Selecteer **Adaptive Insights** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Adaptive Insights met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Adaptive Insights.

Als u Azure AD SSO wilt configureren en testen met Adaptive Insights, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Adaptive Insights SSO](#configure-adaptive-insights-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Maak Adaptive Insights-testgebruiker](#create-adaptive-insights-test-user)** - om een tegenhanger van B.Simon te hebben in Adaptive Insights die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Adaptive Insights-toepassingsintegratie** de sectie **Beheren** en selecteer Enkele **aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > U url-waarden (Id)- en antwoord-URL-waarden ophalen op de pagina **SAML SSO-instellingen** van Adaptive Insights.

4. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Adaptive Insights instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Adaptive Insights SSO configureren

1. Meld u in een ander browservenster aan bij uw Adaptive Insights-bedrijfssite als beheerder.

2. Ga naar **Administration**.

    ![Beheerder](./media/adaptivesuite-tutorial/ic805644.png "Beheerder")

3. Klik **in** de sectie Gebruikers en rollen op **SAML SSO-instellingen**.

    ![SAML SSO-instellingen beheren](./media/adaptivesuite-tutorial/ic805645.png "SAML SSO-instellingen beheren")

4. Voer op de pagina **SAML SSO-instellingen** de volgende stappen uit:

    ![SAML SSO-instellingen](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO-instellingen")

    a. Typ in het tekstvak Naam van de **identiteitsprovider** een naam voor uw configuratie.

    b. Plak de azure **AD-id-waarde** die is gekopieerd van azure-portal in het tekstvak **identiteitsprovider Entiteit-id.**

    c. Plak de **url-waarde van aanmelding** die is gekopieerd van azure-portal in het tekstvak van de **URL-url van de identiteitsprovider.**

    d. Plak de **URL-waarde Van Afmelden** die vanuit azure-portal is gekopieerd in het tekstvak **Aangepaste afmeldings-URL.**

    e. Als u uw gedownloade certificaat wilt uploaden, klikt u op **Bestand kiezen**.

    f. Selecteer het volgende voor:

     * **SAML-gebruikersnaam**, selecteer **de gebruikersnaam van Adaptive Insights van de gebruiker**.

     * **SAML-gebruikersnaamlocatie**, selecteer **Gebruikersnaam in NameID van onderwerp**.

     * **SAML NameID-indeling**, selecteer **E-mailadres**.

     * **Saml inschakelen,** selecteer **SAML-sso toestaan en direct Adaptive Insights-login**.

    g. Kopieer **de URL van Adaptive Insights SSO** en plak in de **tekstvakken Id(Entity ID)** en **Reply URL** in de sectie **BasisSAML-configuratie** in de Azure-portal.

    h. Klik op **Opslaan**.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Adaptive Insights.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Adaptive Insights**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-adaptive-insights-test-user"></a>Adaptive Insights-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Adaptive Insights, moeten ze zijn ingericht in Adaptive Insights. In het geval van Adaptive Insights is inrichten een handmatige taak.

**Voer de volgende stappen uit om de inrichting van gebruikers te configureren:**

1. Meld u aan bij uw **Adaptive Insights-bedrijfssite** als beheerder.

2. Ga naar **Administration**.

   ![Beheerder](./media/adaptivesuite-tutorial/IC805644.png "Beheerder")

3. Klik **in** de sectie Gebruikers en rollen op **Gebruikers**.

   ![Gebruiker toevoegen](./media/adaptivesuite-tutorial/IC805648.png "Gebruiker toevoegen")

4. Voer **in** de sectie Nieuwe gebruiker de volgende stappen uit:

   ![Verzenden](./media/adaptivesuite-tutorial/IC805649.png "Verzenden")

   a. Typ de **naam**, **gebruikersnaam**, **e-mail**, **wachtwoord** van een geldige Azure Active Directory-gebruiker die u wilt inrichten in de gerelateerde tekstvakken.

   b. Selecteer een **rol**.

   c. Klik **op Verzenden**.

> [!NOTE]
> U alle andere tools voor het maken van gebruikersaccounts voor Adaptive Insights gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Adaptive Insights in het Access Panel klikt, moet u automatisch worden aangemeld bij de Adaptive Insights waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

