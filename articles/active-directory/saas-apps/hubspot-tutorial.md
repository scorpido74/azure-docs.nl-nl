---
title: 'Zelfstudie: Azure Active Directory-integratie met HubSpot | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4b235426a7029abb9bb79ba56e582cccc3b14a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944454"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Zelfstudie: Azure Active Directory-integratie met HubSpot

In deze zelfstudie leert u hoe u HubSpot integreert met Azure Active Directory (Azure AD).

De integratie van HubSpot met Azure AD biedt u de volgende voordelen:

* U Azure AD gebruiken om te bepalen wie toegang heeft tot HubSpot.
* Gebruikers kunnen automatisch worden aangemeld bij HubSpot met hun Azure AD-accounts (single sign-on).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Single sign-on](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor toepassingen in Azure Active Directory voor meer informatie over de integratie van de SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met HubSpot wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een HubSpot-abonnement met enkele aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureer en test u Azure AD single sign-on in een testomgeving en integreert u HubSpot met Azure AD.

HubSpot ondersteunt de volgende functies:

* **SP-geïnitieerde single sign-on**
* **Door IDP geïnitieerde eenmalige aanmelding**

## <a name="add-hubspot-in-the-azure-portal"></a>HubSpot toevoegen aan de Azure-portal

Als u HubSpot wilt integreren met Azure AD, moet u HubSpot toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer Azure Active **Directory**in het linkermenu .

    ![De optie Azure Active Directory](common/select-azuread.png)

1. Selecteer **Enterprise-toepassingen** > **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.

    ![De optie Nieuwe toepassing](common/add-new-app.png)

1. Voer **hubspot**in in het zoekvak . Selecteer **HubSpot**in de zoekresultaten en selecteer **Vervolgens Toevoegen**.

    ![HubSpot in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met HubSpot op basis van een testgebruiker met de naam **Britta Simon**. Voor eenmalige aanmelding aan het werk moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in HubSpot.

Als u Azure AD-singlesign-aan met HubSpot wilt configureren en testen, moet u de volgende bouwstenen voltooien:

| Taak | Beschrijving |
| --- | --- |
| **[Azure AD configureren voor eenmalige aanmelding](#configure-azure-ad-single-sign-on)** | Hiermee kunnen uw gebruikers deze functie gebruiken. |
| **[HubSpot-aanmelding configureren](#configure-hubspot-single-sign-on)** | Hiermee configureert u de instellingen voor één aanmelding in de toepassing. |
| **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** | Test Azure AD single sign-on voor een gebruiker genaamd Britta Simon. |
| **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** | Hiermee kan Britta Simon azure AD-enkele aanmelding gebruiken. |
| **[Een HubSpot-testgebruiker maken](#create-a-hubspot-test-user)** | Hiermee maakt u een tegenhanger van Britta Simon in HubSpot die is gekoppeld aan de Azure AD-weergave van de gebruiker. |
| **[Eenmalige aanmelding testen](#test-single-sign-on)** | Controleert of de configuratie werkt. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie configureert u Azure AD single sign-on met HubSpot in de Azure-portal.

1. Selecteer in de [Azure-portal](https://portal.azure.com/)in het **deelvenster-HubSpot-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Optie eenmalig aanmelden configureren](common/select-sso.png)

1. Selecteer in het deelvenster **Eén aanmeldingsmethode** de optie **SAML-** of **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. **Selecteer** bewerken (het potloodpictogram) in het deelvenster Eén aanmelding instellen met SAML om het deelvenster **BasisSAML-configuratie** te openen. **Set up Single Sign-On with SAML**

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het deelvenster **BasisSAML-configuratie,** om de door *IDP geïnitieerde modus*te configureren, de volgende stappen uit:

    1. Voer in het vak **Id** een URL in\/met het\<volgende\>patroon: https: /api.hubspot.com/login-api/v1/saml/login?portalId= KLANT-ID .

    1. Voer in het vak **URL beantwoorden** een URL in\/met\<het\>volgende patroon: https: /api.hubspot.com/login-api/v1/saml/acs?portalId= KLANT-ID .

    ![HubSpot-domein- en URL's met eenmalige aanmelding](common/idp-intiated.png)

    > [!NOTE]
    > Als u de URL's wilt opmaken, u ook verwijzen naar de patronen die worden weergegeven in het deelvenster **BasisSAML-configuratie** in de Azure-portal.

1. Ga als u de toepassing in *de door SP geïnitieerde* modus configureren:

    1. Selecteer **Extra URL's instellen**.

    1. Voer in het vak **Aanmelding smijt** **https in:\//app.hubspot.com/login**.

    ![De optie Extra URL's instellen](common/metadata-upload-additional-signon.png)

1. Selecteer in het deelvenster **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Downloaden** naast **certificaat (Base64)**. Selecteer een downloadoptie op basis van uw vereisten. Sla het certificaat op uw computer op.

    ![De downloadoptie Certificaat (Base64)](common/certificatebase64.png)

1. Kopieer in de sectie **HubSpot instellen** de volgende URL's op basis van uw vereisten:

    * Aanmeldings-URL
    * Azure AD-id
    * Afmeldings-URL

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>HubSpot-aanmelding configureren

1. Open een nieuw tabblad in uw browser en meld u aan bij uw HubSpot-beheerdersaccount.

1. Selecteer het pictogram **Instellingen** in de rechterbovenhoek van de pagina.

    ![Het pictogram Instellingen in HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecteer **Accountstandaardinstellingen**.

    ![De optie Accountstandaarden in HubSpot](./media/hubspot-tutorial/config2.png)

1. Schuif omlaag naar de sectie **Beveiliging** en selecteer **Instellen**.

    ![De optie Instellen in HubSpot](./media/hubspot-tutorial/config3.png)

1. Voer in de sectie **Eén aanmelding instellen** de volgende stappen uit:

    1. Selecteer **Kopiëren** om de waarde te kopiëren in het vak **Doelgroep-URl (Service Provider Entity ID).** Plak de waarde in de **Azure-portal** in het deelvenster **BasisSAML-configuratie** in het vak Id.

    1. Selecteer **Kopiëren** om de waarde te kopiëren in het vak **Ondertekenen op URl, ACS, Ontvanger of Omleiding.** Plak de waarde in de Azure-portal in het deelvenster **BasisSAML-configuratie** in het vak **URL van antwoord.**

    1. Plak in HubSpot in het vak **Identity Provider Identifier of Issuer URL** de waarde voor Azure AD **Identifier** die u hebt gekopieerd in de Azure-portal.

    1. Plak in HubSpot in het vak **URL voor één aanmelding seinvan de identiteitsprovider** de waarde voor **aanmeldings-URL** die u hebt gekopieerd in de Azure-portal.

    1. Open in Windows Notepad het certificaatbestand (Base64) dat u hebt gedownload. Selecteer en kopieer de inhoud van het bestand. Plak het vervolgens in HubSpot in het vak **X.509-certificaat.**

    1. Selecteer **Verifiëren**.

        ![De sectie Eén aanmelding instellen in HubSpot](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in de Azure-portal **Azure Active Directory** > **Users** > **All users All users**.

    ![De opties voor gebruikers en alle gebruikers](common/users.png)

1. Selecteer **Nieuwe gebruiker**.

    ![De optie Nieuwe gebruiker](common/new-user.png)

1. Voer in het deelvenster **Gebruiker** de volgende stappen uit:

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer in het vak **Gebruikersnaam** **de> van brittasimon\@\<your-company-domain in.\< uitbreiding\>**. Bijvoorbeeld, **brittasimon\@contoso.com**.

    1. Schakel het selectievakje **Wachtwoord weergeven** in. Noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**

    1. Selecteer **Maken**.

    ![Het deelvenster Gebruiker](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon toegang tot HubSpot, zodat ze Azure-single sign-on kan gebruiken.

1. Selecteer in de Azure-portal De optie **Enterprise-toepassingen** > **Alle toepassingen** > **HubSpot**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **HubSpot**in de lijst met toepassingen .

    ![HubSpot in de lijst met toepassingen](common/all-applications.png)

1. Selecteer **Gebruikers en groepen** in het menu.

    ![De optie Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer **Britta Simon** in het deelvenster **Gebruikers en groepen** in de lijst met gebruikers. Kies **Selecteren**.

1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het **rolvenster Selecteren** de relevante rol voor de gebruiker in de lijst. Kies **Selecteren**.

1. Selecteer **Toewijzing toewijzen** in het deelvenster Toewijzing **toevoegen**.

### <a name="create-a-hubspot-test-user"></a>Een HubSpot-testgebruiker maken

Als u Azure AD wilt inschakelen dat een gebruiker zich aanmeldt bij HubSpot, moet de gebruiker zijn ingericht in HubSpot. In HubSpot is inrichten een handmatige taak.

Ga als lid van de gebruiker naar een gebruikersaccount in HubSpot:

1. Meld u aan bij uw HubSpot-bedrijfssite als beheerder.

1. Selecteer het pictogram **Instellingen** in de rechterbovenhoek van de pagina.

    ![Het pictogram Instellingen in HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecteer **gebruikers & Teams**.

    ![De optie Gebruikers & Teams in HubSpot](./media/hubspot-tutorial/user1.png)

1. Selecteer **Gebruiker maken**.

    ![De optie Gebruiker maken in HubSpot](./media/hubspot-tutorial/user2.png)

1. Voer in het vak **E-mailaddess(es)** het e-mailadres van\@de gebruiker in de indeling brittasimon contoso.com in en selecteer **Volgende**.

    ![Het vak E-mailadres(en toevoegen in de sectie Gebruikers maken in HubSpot](./media/hubspot-tutorial/user3.png)

1. Selecteer **in de** sectie Gebruikers maken elk tabblad. Stel op elk tabblad de relevante opties en machtigingen voor de gebruiker in. Selecteer vervolgens **Volgende**.

    ![Tabbladen in de sectie Gebruikers maken in HubSpot](./media/hubspot-tutorial/user4.png)

1. Als u de uitnodiging naar de gebruiker wilt verzenden, selecteert u **Verzenden**.

    ![De optie Verzenden in HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > De gebruiker wordt geactiveerd nadat de gebruiker de uitnodiging heeft geaccepteerd.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u uw Azure AD-configuratie met eenmalige aanmelding met de portal Mijn apps.

Nadat u één aanmelding hebt ingesteld, wordt u automatisch aangemeld bij HubSpot wanneer u **HubSpot** selecteert in de mijn apps-portal. Zie Apps openen en gebruiken [in de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md)voor meer informatie over de portal Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Bekijk deze artikelen voor meer informatie:

- [Lijst met zelfstudies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
