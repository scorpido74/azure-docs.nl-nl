---
title: 'Zelf studie: integratie Azure Active Directory met Projectplace | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 179721075484c35c5ebbb3d936b83bc407b75a8d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67093526"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Zelf studie: Projectplace integreren met Azure Active Directory

In deze zelf studie leert u hoe u Projectplace integreert met Azure Active Directory (Azure AD). Wanneer u Projectplace integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Projectplace.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Projectplace met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.
* Gebruikers kunnen automatisch worden ingericht in Projectplace.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Projectplace-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. Projectplace ondersteunt SSO die door **SP en IDP** is geïnitieerd en ondersteunt **just-in-time** -gebruikers inrichting.

## <a name="adding-projectplace-from-the-gallery"></a>Projectplace toevoegen uit de galerie

Als u de integratie van Projectplace in azure AD wilt configureren, moet u Projectplace uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Projectplace** in het zoekvak.
1. Selecteer **Projectplace** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO met Projectplace configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Projectplace.

Als u Azure AD SSO wilt configureren en testen met Projectplace, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Projectplace](#configure-projectplace)** om de SSO-instellingen aan de kant van de toepassing te configureren.
3. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
5. **[Maak een Projectplace-test gebruiker](#create-projectplace-test-user)** voor het maken van een equivalent van B. Simon in Projectplace dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Projectplace** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, wordt de toepassing vooraf geconfigureerd in de sectie **basis configuratie van SAML** en zijn de benodigde url's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door te klikken op de knop **Opslaan** .

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **URL voor aanmelding** :`https://service.projectplace.com`

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , op **pictogram** kopiëren om de URL voor de **federatieve meta gegevens**van de app te kopiëren, conform uw vereiste en op te slaan in Klad blok.

   ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

1. Op de sectie **Projectplace instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Projectplace configureren

Als u eenmalige aanmelding aan de **Projectplace** zijde wilt configureren, moet u de gekopieerde **app federatieve meta gegevens-Url** van de Azure Portal naar het [ondersteunings team van Projectplace](https://success.planview.com/Projectplace/Support)verzenden. Dit team zorgt ervoor dat de SAML SSO-verbinding aan beide zijden correct is ingesteld.

>[!NOTE]
>De configuratie voor eenmalige aanmelding moet worden uitgevoerd door het [Projectplace-ondersteunings team](https://success.planview.com/Projectplace/Support). U ontvangt een melding zodra de configuratie is voltooid. 

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B. Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Projectplace.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Projectplace**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-projectplace-test-user"></a>Projectplace-test gebruiker maken

>[!NOTE]
>U kunt deze stap overs Laan als u inrichten hebt ingeschakeld in Projectplace. U kunt het [ondersteunings team van Projectplace](https://success.planview.com/Projectplace/Support) vragen om provisoning in te scha kelen wanneer gebruikers tijdens de eerste aanmelding worden gemaakt in Projectplace.

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Projectplace, moet u deze toevoegen aan Projectplace. U moet deze hand matig toevoegen.

**Voer de volgende stappen uit om een gebruikers account te maken:**

1. Meld u als beheerder aan bij uw **Projectplace** -bedrijfs site.

2. Ga naar **personen**en selecteer **leden**:
   
    ![Ga naar personen en selecteer leden](./media/projectplace-tutorial/ic790228.png "People")

3. Selecteer **lid toevoegen**:
   
    ![Lid toevoegen selecteren](./media/projectplace-tutorial/ic790232.png "Leden toevoegen")

4. Voer de volgende stappen uit in de sectie **lid toevoegen** .
   
    ![Sectie lid toevoegen](./media/projectplace-tutorial/ic790233.png "Nieuwe leden")
   
    1. Voer in het vak **nieuwe leden** het e-mail adres in van een geldig Azure ad-account dat u wilt toevoegen.
   
    1. Selecteer **Verzenden**.

   Een e-mail met een koppeling om het account te bevestigen voordat deze actief wordt, wordt verzonden naar de eigenaar van het Azure AD-account.

>[!NOTE]
>U kunt ook een ander hulp programma voor het maken van gebruikers accounts of API van Projectplace gebruiken om Azure AD-gebruikers accounts toe te voegen.


### <a name="test-sso"></a>SSO testen

Wanneer u de tegel Projectplace in het toegangs venster selecteert, wordt u automatisch aangemeld bij de Projectplace waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)