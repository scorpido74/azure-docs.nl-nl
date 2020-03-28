---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met Azure AD SAML Toolkit | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Azure AD SAML Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7902112c1694bacfeb45b5f20db80d5136642169
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77047955"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Azure AD SAML Toolkit

In deze zelfstudie leert u hoe u Azure AD SAML Toolkit integreert met Azure Active Directory (Azure AD). Wanneer u Azure AD SAML Toolkit integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Azure AD SAML Toolkit.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Azure AD SAML Toolkit met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Azure AD SAML Toolkit single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Azure AD SAML Toolkit ondersteunt **SP** geïnitieerde SSO
* Zodra u Azure AD SAML Toolkit hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Azure AD SAML Toolkit toevoegen vanuit de galerie

Als u de integratie van Azure AD SAML Toolkit in Azure AD wilt configureren, moet u Azure AD SAML Toolkit vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ Azure **AD SAML Toolkit** in het zoekvak in de sectie Toevoegen vanuit de **galerie.**
1. Selecteer **Azure AD SAML Toolkit** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-azure-ad-saml-toolkit"></a>Azure AD-aanmelding voor Azure AD SAML Toolkit configureren en testen

Azure AD SSO configureren en testen met Azure AD SAML Toolkit met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Azure AD SAML Toolkit.

Voer de volgende bouwstenen uit om Azure AD SSO te configureren en te testen met Azure AD SAML Toolkit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Azure AD SAML Toolkit SSO](#configure-azure-ad-saml-toolkit-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Azure AD SAML Toolkit-testgebruiker maken](#create-azure-ad-saml-toolkit-test-user)** - om een tegenhanger van B.Simon te hebben in Azure AD SAML Toolkit die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Azure AD SAML** Toolkit-toepassingsintegratie de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL: `https://samltoolkit.azurewebsites.net/`

    b. Typ een URL in het vak **Id (Entiteits-id)**: `https://samltoolkit.azurewebsites.net`

    c. Typ een URL in het tekstvak **URL beantwoorden:**`https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

1. Kopieer in de sectie **Azure AD SAML Toolkit** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Azure AD SAML Toolkit.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer Azure AD **SAML Toolkit**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Azure AD SAML Toolkit SSO configureren

1. Open een nieuw webbrowservenster als u zich niet hebt geregistreerd op de website van de Azure AD SAML Toolkit, registreer u eerst door op het **register**te klikken. Als u zich al hebt geregistreerd, meldt u zich aan bij uw azure AD SAML Toolkit-bedrijfssite met behulp van de geregistreerde aanmeldingsreferenties.

    ![Azure AD SAML Toolkit Register](./media/saml-toolkit-tutorial/register.png)

1. Klik op de **SAML-configuratie**.

    ![SAML-configuratie van Azure AD SAML-toolkit](./media/saml-toolkit-tutorial/saml-configure.png)

1. Klik **op Maken**.

    ![Azure AD SAML Toolkit SSO maken](./media/saml-toolkit-tutorial/createsso.png)

1. Voer op de pagina **SAML SSO-configuratie** de volgende stappen uit:

    ![Azure AD SAML Toolkit SSO maken](./media/saml-toolkit-tutorial/fill-details.png)

    1. Plak in het tekstvak **Vooraanmeldings-URL** de **waarde van de inlog-URL,** die u hebt gekopieerd uit de Azure-portal.

    1. Plak in het tekstvak **Azure AD-id** de azure **AD-id-waarde** die u hebt gekopieerd van de Azure-portal.

    1. Plak in het tekstvak voor de **afmeldings-URL** de waarde van de **afmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    1. Klik **op Bestand kiezen** en upload het **certificaatbestand (Raw)** dat u hebt gedownload van de Azure-portal.

    1. Klik **op Maken**.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Azure AD SAML Toolkit-testgebruiker maken

In deze sectie wordt een gebruiker genaamd B.Simon gemaakt in Azure AD SAML Toolkit. Azure AD SAML Toolkit ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Azure AD SAML Toolkit, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Azure AD SAML Toolkit klikt in het toegangspaneel, moet u automatisch worden aangemeld bij de Azure AD SAML Toolkit waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD SAML Toolkit uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Azure AD SAML Toolkit beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/protect-azure)