---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met prisma Cloud | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en prisma Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 00f2ee0d-92e7-4f5a-a865-837de26b5255
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd2e74b51e690c13bbc7d3b00603405ed80dd953
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106937"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-prisma-cloud"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met prisma Cloud

In deze zelf studie leert u hoe u prisma Cloud kunt integreren met Azure Active Directory (Azure AD). Wanneer u prisma-Cloud integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de cloud van Prisma.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij prisma Cloud met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Prisma-abonnement met eenmalige aanmelding voor de Cloud (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Prisma Cloud ondersteunt **IDP** GEÏNITIEERDe SSO

* Prisma Cloud ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-prisma-cloud-from-the-gallery"></a>Een prisma-Cloud toevoegen vanuit de galerie

Als u de integratie van Prisma Cloud wilt configureren in azure AD, moet u prisma Cloud toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **prisma Cloud** in het zoekvak.
1. Selecteer **prisma Cloud** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-prisma-cloud"></a>Eenmalige aanmelding voor Azure AD voor prisma Cloud configureren en testen

Azure AD SSO met prisma Cloud configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in prisma Cloud.

Als u Azure AD SSO wilt configureren en testen met prisma Cloud, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Prisma Cloud SSO configureren](#configure-prisma-cloud-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een prisma-test gebruiker](#create-prisma-cloud-test-user)** voor de Cloud, zodat deze een soort is van B. Simon in de prisma-Cloud die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **prisma Cloud** Application Integration de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **eenmalige aanmelding met SAML instellen** de waarden in voor de volgende velden:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://app2.prismacloud.io/customer/<CUSTOMERID>`

    b. De waarden van de **antwoord-URL** zijn vast en worden al vooraf ingevuld in azure Portal. U moet de juiste URL selecteren volgens uw vereiste.

    > [!NOTE]
    > De id-waarde is niet echt. Werk de waarde bij met de werkelijke-id. Neem contact op met het [ondersteunings team van Prisma cloud client](mailto:support@paloaltonetworks.com) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/certificatebase64.png)

1. Kopieer de juiste URL ('s) op basis van uw vereiste in het gedeelte **prisma-Cloud instellen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** aan de bovenkant van het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot de prisma-Cloud.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **prisma Cloud**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-prisma-cloud-sso"></a>Prisma Cloud SSO configureren

Voor het configureren van eenmalige aanmelding op **prisma Cloud** zijde moet u het gedownloade **certificaat (base64)** en de juiste gekopieerde url's verzenden van Azure Portal naar [prisma Cloud support team](mailto:support@paloaltonetworks.com). Ze stelt u deze optie om de SAML SSO-verbinding instellen goed aan beide zijden.

### <a name="create-prisma-cloud-test-user"></a>Prisma Cloud test gebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in prisma Cloud. Prisma Cloud biedt ondersteuning voor Just-in-time-inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker zich nog niet in prisma Cloud bevindt, wordt er een nieuwe gemaakt wanneer u toegang probeert te krijgen tot de prisma-Cloud.

## <a name="test-sso"></a>SSO testen 

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u in het toegangs venster op de Cloud tegel prisma klikt, moet u automatisch worden aangemeld bij de prisma-Cloud waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer prisma Cloud met Azure AD](https://aad.portal.azure.com/)

