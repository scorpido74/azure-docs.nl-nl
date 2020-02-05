---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met hoek Steenpen | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Cornerstone OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34a92a11880d61ff3e75e0b2a982b6375841b4b7
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983518"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-ondemand"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met hoek Steene-demand

In deze zelf studie leert u hoe u de steen hoek kunt integreren met Azure Active Directory (Azure AD). Wanneer u de hoek steen van de stenen met Azure AD integreert, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de OnDemand.
* Stel uw gebruikers in staat om automatisch te worden aangemeld op hoek Steenpen met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Hoek steen van de OnDemand single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Cornerstone OnDemand ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* Cornerstone OnDemand biedt ondersteuning voor [Geautomatiseerd gebruikers inrichten](cornerstone-ondemand-provisioning-tutorial.md)
* Nadat u op de hoek van het scherm hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Cornerstone OnDemand toevoegen vanuit de galerie

Voor het configureren van de integratie van Cornerstone OnDemand in Azure AD moet u Cornerstone OnDemand vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **toevoegen vanuit de galerie de** tekst van de **hoek steen** in het zoekvak.
1. Selecteer de hoek steen op de optie **OnDemand** van het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cornerstone-ondemand"></a>Eenmalige aanmelding van Azure AD voor hoek Steenpen configureren en testen

Azure AD SSO configureren en testen met behulp van de OnDemand van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker op de hoek steen van de OnDemand.

Voer de volgende bouw stenen uit om Azure AD SSO te configureren en te testen met de OnDemand:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
2. Het configureren van de **[hoek steen. eenmalige aanmelding](#configure-cornerstone-ondemand-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een steen hoek van Ondemande test gebruiker](#create-cornerstone-ondemand-test-user)** : als u een tegen hanger wilt hebben van B. Simon in hoek steen en-OnDemand dat is gekoppeld aan de Azure AD-representatie van gebruiker.
3. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina hoek van de **OnDemand** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<company>.csod.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<company>.csod.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [Cornerstone OnDemand-clientondersteuningsteam](mailto:moreinfo@csod.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer de gewenste URL ('s) op basis van uw vereiste op de sectie **hoek stenen instellen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
    1. Voer in het veld **Naam**`B.Simon` in.  
    1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
    1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
    1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de hoek van de begeleider.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **hoek steen**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cornerstone-ondemand-sso"></a>Het configureren van de hoek steen. OnDemand

Als u eenmalige aanmelding aan de **Cornerstone OnDemand**-zijde wilt configureren, moet u het gedownloade **certificaat (Base64)** en de betreffende uit Azure Portal gekopieerde URL's naar het [Cornerstone OnDemand-ondersteuningsteam](mailto:moreinfo@csod.com) verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-cornerstone-ondemand-test-user"></a>Een Cornerstone OnDemand-testgebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam B. Simon op hoek steen van de OnDemand. Cornerstone OnDemand ondersteunt het automatisch inrichten van gebruikers, wat standaard is ingeschakeld. U kunt [hier](https://docs.microsoft.com/azure/active-directory/saas-apps/cornerstone-ondemand-provisioning-tutorial) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

Als u het inrichten van gebruikers wilt configureren, verzendt u de informatie (bijvoorbeeld: naam, e-mail adres) over de Azure AD-gebruiker die u wilt inrichten op het [OnDemand-ondersteunings team](mailto:moreinfo@csod.com)van de hoek.

>[!NOTE]
>U kunt andere hoek stenen voor het maken van OnDemand-gebruikers accounts of Api's die worden geleverd door hoek Steenpen gebruiken om Azure AD-gebruikers accounts in te richten.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Cornerstone OnDemand in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Cornerstone OnDemand waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer het opnieuw op de hoek van een OnDemand met Azure AD](https://aad.portal.azure.com)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)