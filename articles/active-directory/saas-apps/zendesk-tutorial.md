---
title: 'Zelfstudie: Azure Active Directory-integratie met Zendesk | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/26/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f70e7067a78b439d3dcaf0b83460296cad7b4485
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585804"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Zendesk

In deze zelfstudie leert u hoe u Zendesk integreren met Azure Active Directory (Azure AD). Wanneer u Zendesk integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Zendesk.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Zendesk met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Abonnement met één aanmelding sso (Zendesk single sign-on) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Zendesk ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)
* Zendesk ondersteunt het [**geautomatiseerd** inrichten van gebruikers](zendesk-provisioning-tutorial.md)
* Zodra u Zendesk configureert, u sessiebeheer afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-zendesk-from-the-gallery"></a>Zendesk toevoegen vanuit de galerie

Voor het configureren van de integratie van Zendesk in Azure AD moet u Zendesk uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Zendesk** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Zendesk** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zendesk"></a>Azure AD-aanmelding voor Zendesk configureren en testen

Azure AD SSO configureren en testen met Zendesk met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Zendesk.

Als u Azure AD SSO wilt configureren en testen met Zendesk, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Zendesk SSO](#configure-zendesk-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Zendesk-testgebruiker maken](#create-zendesk-test-user)** - om een tegenhanger van B.Simon in Zendesk te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de integratiepagina van **de Zendesk-toepassing** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.zendesk.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<subdomain>.zendesk.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [ondersteuningsteam van Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de Zendesk-toepassing wordt verwacht dat de SAML-asserties een specifieke indeling hebben. Er zijn geen verplichte SAML-kenmerken, maar u kunt kenmerken desgewenst beheren vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![installatiekopie](common/edit-attribute.png)

    > [!NOTE]
    > U gebruikt extensiekenmerken om attributen toe te voegen die niet standaard in Azure AD zitten. Klik op [Gebruikerskenmerken die kunnen worden ingesteld in SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) om de volledige lijst van SAML-kenmerken op te halen die in **Zendesk** worden geaccepteerd.

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer in de sectie **SAML-ondertekeningscertificaat** de **waarde voor duimafdruk** en sla deze op uw computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. Kopieer in de sectie **Zendesk instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Zendesk.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Zendesk**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-zendesk-sso"></a>Zendesk SSO configureren

1. Als u Zendesk handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij uw Zendesk-bedrijfssite en voert u de volgende stappen uit:

2. Klik op **Admin**.

3. Klik in het linkernavigatievenster op **Settings** en vervolgens op **Security**.

4. Voer op de pagina **Security** de volgende stappen uit:

    ![Beveiliging](./media/zendesk-tutorial/ic773089.png "Beveiliging")

    ![Eenmalige aanmelding](./media/zendesk-tutorial/ic773090.png "Eenmalige aanmelding")

    a. Klik op het tabblad **Admin & Agents**.

    b. Selecteer **Single sign-on (SSO) and SAML** en selecteer vervolgens **SAML**.

    c. Plak in het tekstvak **SAML SSO URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Plak in het tekstvak **Remote Logout URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    e. Plak in het tekstvak **Certificate Fingerprint** de waarde van **Vingerafdruk** die u hebt gekopieerd uit de Azure-portal.

    f. Klik op **Opslaan**.

### <a name="create-zendesk-test-user"></a>Een testgebruiker maken in Zendesk

Het doel van deze sectie is het maken van een gebruiker met de naam Britta Simon in Zendesk. Zendesk ondersteunt het automatisch inrichten van gebruikers, wat standaard is ingeschakeld. U kunt [hier](Zendesk-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

> [!NOTE]
> **Eindgebruikers**accounts worden automatisch ingericht bij het aanmelden. **Agent**- en **beheerders**accounts moeten handmatig worden ingericht in **Zendesk** voordat u zich aanmeldt.

1. Meld u aan bij uw **Zendesk-huurder.**

2. Selecteer het tabblad **Customer List**.

3. Selecteer het tabblad **User** en klik op **Add**.

    ![Gebruiker toevoegen](./media/zendesk-tutorial/ic773632.png "Gebruiker toevoegen")
4. Typ in **Name** en **Email** de naam en het e-mailadres van een bestaand Azure AD-account dat u wilt inrichten en klik op **Save**.

    ![Nieuwe gebruiker](./media/zendesk-tutorial/ic773633.png "Nieuwe gebruiker")

> [!NOTE]
> U alle andere hulpprogramma's voor het maken van Zendesk-gebruikersaccounts of API's die door Zendesk worden verstrekt, gebruiken om Azure AD-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Zendesk in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Zendesk waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Zendesk met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Zendesk beschermen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Gebruikersinrichting configureren](zendesk-provisioning-tutorial.md)