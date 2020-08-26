---
title: 'Zelfstudie: Azure Active Directory-integratie met Adaptive Insights | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Adaptive Insights.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.openlocfilehash: 3471d79875fb34ea0285c086e7d908aa254957dc
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88537734"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Zelfstudie: Adaptive Insights integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Adaptive Insights integreert met Azure Active Directory (Azure AD). Wanneer u Adaptive Insights integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Adaptive Insights.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Adaptive Insights.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Adaptive Insights waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Adaptive Insights ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-adaptive-insights-from-the-gallery"></a>Adaptive Insights toevoegen vanuit de galerie

Voor het configureren van de integratie van Adaptive Insights met Azure Active Directory moet u Adaptive Insights vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Adaptive Insights**.
1. Selecteer **Adaptive Insights** in de resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Adaptive Insights met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Adaptive Insights.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Adaptive Insights te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij Adaptive Insights configureren](#configure-adaptive-insights-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Een Adaptive Insights-testgebruiker maken](#create-adaptive-insights-test-user)** : als u een equivalent van B.Simon in Adaptive Insights wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Adaptive Insights**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > De waarden voor Id (entiteits-id) en Antwoord-URL ophalen van de pagina **SAML SSO Settings** van Adaptive Insights.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Adaptive Insights instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Eenmalige aanmelding configureren in Adaptive Insights

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van Adaptive Insights.

2. Ga naar **Administration**.

    ![Beheerder](./media/adaptivesuite-tutorial/ic805644.png "Beheerder")

3. Klik in de sectie **Users and Roles** op **SAML SSO Settings**.

    ![SAML SSO-instellingen beheren](./media/adaptivesuite-tutorial/ic805645.png "SAML SSO-instellingen beheren")

4. Voer op de pagina **SAML SSO Settings** de volgende stappen uit:

    ![SAML SSO Settings](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO-instellingen")

    a. Typ in het tekstvak **Identity provider name** een naam voor de configuratie.

    b. Plak de **Azure AD-id** die u hebt gekopieerd in Azure Portal, in het tekstvak **Identity provider Entity ID**.

    c. Plak de **aanmeldings-URL** die u hebt gekopieerd in Azure Portal, in het tekstvak **Identity provider SSO URL**.

    d. Plak de **afmeldings-URL** die u hebt gekopieerd in Azure Portal, in het tekstvak **Custom logout URL**.

    e. Klik op **Choose file** om het gedownloade certificaat te uploaden.

    f. Selecteer de volgende waarden:

     * **SAML user id**: selecteer **User’s Adaptive Insights user name**.

     * **SAML user id location**: selecteer **User id in NameID of Subject**.

     * **SAML NameID format**: selecteer **Email address**.

     * **Enable SAML**: selecteer **Allow SAML SSO and direct Adaptive Insights login**.

    g. Kopieer de waarde van **Adaptive Insights SSO URL** en plak deze in de tekstvakken **Id (entiteits-id)** en **Antwoord-URL** in de sectie **Standaard SAML-configuratie** in Azure Portal.

    h. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Adaptive Insights.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Adaptive Insights** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-adaptive-insights-test-user"></a>Testgebruiker maken voor Adaptive Insights

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Adaptive Insights, moeten ze worden ingericht in Adaptive Insights. In het geval van Adaptive Insights moet dit handmatig gebeuren.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u als beheerder aan bij de bedrijfssite van **Adaptive Insights**.

2. Ga naar **Administration**.

   ![Beheerder](./media/adaptivesuite-tutorial/IC805644.png "Beheerder")

3. Klik in de sectie **Users and Roles** op **Users**.

   ![Gebruiker toevoegen](./media/adaptivesuite-tutorial/IC805648.png "Gebruiker toevoegen")

4. Voer in het gedeelte **New User** de volgende stappen uit:

   ![Verzenden](./media/adaptivesuite-tutorial/IC805649.png "Verzenden")

   a. Typ waarden voor **Name**, **Username**, **Email** en **Password** voor een geldige Azure Active Directory-gebruiker die u wilt inrichten.

   b. Selecteer een rol in de vervolgkeuzelijst **Role**.

   c. Klik op **Submit**

> [!NOTE]
> U kunt voor het inrichten van Azure AD-gebruikersaccounts andere hulpprogramma's of API's voor het maken van Adaptive Insights-gebruikersaccount gebruiken die door Adaptive Insights worden verstrekt.

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Adaptive Insights klikt, wordt u automatisch aangemeld bij de instantie van Adaptive Insights waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

