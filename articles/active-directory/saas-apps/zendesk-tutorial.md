---
title: 'Zelf studie: integratie Azure Active Directory met Zendesk | Microsoft Docs'
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80585804"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Zendesk

In deze zelf studie leert u hoe u Zendesk integreert met Azure Active Directory (Azure AD). Wanneer u Zendesk integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Zendesk.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Zendesk met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Zendesk-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Zendesk ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)
* Zendesk ondersteunt het [**geautomatiseerd** inrichten van gebruikers](zendesk-provisioning-tutorial.md)
* Nadat u Zendesk hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-zendesk-from-the-gallery"></a>Zendesk toevoegen vanuit de galerie

Voor het configureren van de integratie van Zendesk in Azure AD moet u Zendesk uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Zendesk** in het zoekvak.
1. Selecteer **Zendesk** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zendesk"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Zendesk

Azure AD SSO met Zendesk configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zendesk.

Als u Azure AD SSO wilt configureren en testen met Zendesk, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[ZENDESK SSO configureren](#configure-zendesk-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Zendesk-test gebruiker](#create-zendesk-test-user)** -om een equivalent van B. Simon in Zendesk te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Zendesk** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

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

1. Kopieer de waarde van de **vinger afdruk** in de sectie **SAML-handtekening certificaat** en sla deze op uw computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. Op de sectie **Zendesk instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Zendesk.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Zendesk**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-zendesk-sso"></a>Zendesk SSO configureren

1. Als u Zendesk hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw Zendesk-bedrijfs site als beheerder en voert u de volgende stappen uit:

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

1. Meld u aan bij uw **Zendesk** -Tenant.

2. Selecteer het tabblad **Customer List**.

3. Selecteer het tabblad **User** en klik op **Add**.

    ![Gebruiker toevoegen](./media/zendesk-tutorial/ic773632.png "Gebruiker toevoegen")
4. Typ in **Name** en **Email** de naam en het e-mailadres van een bestaand Azure AD-account dat u wilt inrichten en klik op **Save**.

    ![Nieuwe gebruiker](./media/zendesk-tutorial/ic773633.png "Nieuwe gebruiker")

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van Zendesk-gebruikers accounts of Api's die worden geleverd door Zendesk, gebruiken om Azure AD-gebruikers accounts in te richten.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Zendesk in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Zendesk waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Zendesk met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Zendesk beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Gebruikers inrichten configureren](zendesk-provisioning-tutorial.md)