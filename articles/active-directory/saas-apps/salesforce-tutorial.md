---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Salesforce | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/17/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a875cee7e6796a2c865bde4a62f2f0463eb12130
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78967738"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Salesforce

In deze zelfstudie leert u hoe u Salesforce integreren met Azure Active Directory (Azure AD). Wanneer u Salesforce integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Salesforce.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Salesforce met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Salesforce single sign-on (SSO) heeft een abonnement ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Salesforce biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

* Salesforce ondersteunt [ **geautomatiseerde** gebruikersinrichting en deprovisioning](salesforce-provisioning-tutorial.md) (aanbevolen)

* Salesforce biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers

* Salesforce Mobile-toepassing kan nu worden geconfigureerd met Azure AD voor het inschakelen van SSO. In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.
* Zodra u Salesforce hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-from-the-gallery"></a>Salesforce toevoegen vanuit de galerie

Voor het configureren van de integratie van Salesforce in Azure AD moet u Salesforce vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Salesforce** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Salesforce** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Azure AD-aanmelding voor Salesforce configureren en testen

Azure AD SSO configureren en testen met Salesforce met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Salesforce.

Als u Azure AD SSO wilt configureren en testen met Salesforce, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Salesforce SSO configureren](#configure-salesforce-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Salesforce-testgebruiker maken](#create-salesforce-test-user)** - om een tegenhanger van B.Simon in Salesforce te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Salesforce-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. Typ in het tekstvak **Aanmeldings-URL** een URL met de volgende indeling:

    Bedrijfsaccount: `https://<subdomain>.my.salesforce.com`

    Ontwikkelaarsaccount: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Typ in het tekstvak **Id** een waarde met de volgende indeling:

    Bedrijfsaccount: `https://<subdomain>.my.salesforce.com`

    Ontwikkelaarsaccount: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daadwerkelijke aanmeldings-URL en -id. Neem contact op met het [Salesforce-klantondersteuningsteam](https://help.salesforce.com/support) om deze waarden te verkrijgen.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Salesforce instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Salesforce.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Salesforce** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-salesforce-sso"></a>Salesforce SSO configureren

1. Als u de configuratie binnen Salesforce wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure sign-in** installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

1. Nadat je de extensie aan de browser hebt toegevoegd, klik je op **Salesforce instellen** en stuur je door naar de Salesforce Single Sign-On-applicatie. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Salesforce Single Sign-On. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-13.

    ![Configuratie instellen](common/setup-sso.png)

1. Als u Salesforce handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan op uw Salesforce-bedrijfssite en voert u de volgende stappen uit:

1. Klik op **Instellen** onder het **instellingenpictogram** in de rechterbovenhoek van de pagina.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/configure1.png)

1. Schuif in het navigatiedeelvenster omlaag naar **INSTELLINGEN** en klik op **Identiteit** om het bijbehorende gedeelte uit te vouwen. Klik vervolgens op **Instellingen voor eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/sf-admin-sso.png)

1. Op de pagina **Instellingen voor eenmalige aanmelding** klikt u op de knop **Bewerken**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Als u eenmalige aanmelding niet kunt inschakelen voor uw Salesforce-account, moet u mogelijk contact opnemen met het [Salesforce-klantondersteuningsteam](https://help.salesforce.com/support).

1. Selecteer **SAML ingeschakeld** en klik vervolgens op **Opslaan**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/sf-enable-saml.png)

1. Als u uw SAML-instellingen voor eenmalige aanmelding wilt configureren, klikt u op **Nieuw op basis van het bestand met metagegevens**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Klik op **Bestand kiezen** om het XML-bestand met metagegevens te uploaden dat u hebt gedownload uit Azure Portal. Klik dan op **Maken**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/xmlchoose.png)

1. Op de pagina **SAML-aanmeldingsinstellingen** worden de velden automatisch gevuld, selecteert u de **ingeschakelde gebruikersinrichting** en klikt u op **Opslaan**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/salesforcexml.png)

1. Klik in het navigatiedeelvenster links in Salesforce op **Bedrijfsinstellingen** om het bijbehorende gedeelte uit te vouwen. Klik dan op **Mijn domein**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/sf-my-domain.png)

1. Schuif omlaag naar het gedeelte **Verificatieconfiguratie** en klik op de knop **Bewerken**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. In het gedeelte **Verificatieconfiguratie** schakelt u **AzureSSO** in als **verificatieservice** voor de SAML-configuratie voor eenmalige aanmelding. Klik dan op **Opslaan **.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Als er meer dan één verificatieservice wordt geselecteerd, wordt gebruikers gevraagd met welke verificatieservice ze zich graag willen aanmelden om gebruik te maken van eenmalige aanmelding in uw Salesforce-omgeving. Als u niet wilt dat dit gebeurt, moet u **andere verificatieservices uitgeschakeld laten**.

### <a name="create-salesforce-test-user"></a>Een Salesforce-testgebruiker maken

In deze sectie wordt een gebruiker genaamd B.Simon gemaakt in Salesforce. Salesforce biedt ondersteuning voor just-in-time inrichten, wat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Salesforce, wordt er een nieuwe gemaakt wanneer u Salesforce opent. Salesforce ondersteunt ook automatische inrichting van gebruikers. Meer details over het configureren van automatische inrichting van gebruikers vindt u [hier](salesforce-provisioning-tutorial.md).

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Salesforce-tegel in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Salesforce waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="test-sso-for-salesforce-mobile"></a>Test SSO voor Salesforce (Mobile)

1. Open de mobiele salesforce-applicatie. Klik op de aanmeldingspagina op **Aangepast domein gebruiken**.

    ![Mobiele Salesforce-app](media/salesforce-tutorial/mobile-app1.png)

1. Voer in het tekstvak **Aangepast domein** uw geregistreerde aangepaste domeinnaam in en klik op **Doorgaan**.

    ![Mobiele Salesforce-app](media/salesforce-tutorial/mobile-app2.png)

1. Voer uw Azure AD-referenties in om u aan te melden bij de Salesforce-toepassing en klik op **Volgende**.

    ![Mobiele Salesforce-app](media/salesforce-tutorial/mobile-app3.png)

1. Klik op de pagina **Toegang toestaan** zoals hieronder wordt weergegeven **op Toestaan** om toegang te geven tot de Salesforce-toepassing.

    ![Mobiele Salesforce-app](media/salesforce-tutorial/mobile-app4.png)

1. Ten slotte na een succesvolle aanmelding, zal de homepage van de toepassing worden weergegeven.

    ![Mobiele Salesforce-app](media/salesforce-tutorial/mobile-app5.png) ![Salesforce mobiele app](media/salesforce-tutorial/mobile-app6.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Gebruikersinrichting configureren](salesforce-provisioning-tutorial.md)

- [Salesforce uitproberen met Azure AD](https://aad.portal.azure.com)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Salesforce beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/protect-salesforce)