---
title: 'Zelf studie: integratie Azure Active Directory met Predictix-volg orde | Microsoft Docs'
description: In deze zelf studie leert u hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory-en Predictix-volg orde.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2fe2f976-e97f-4368-9695-3e1624409e8b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 457ab3a0d5e816becbd2b32d858d5172951f27ad
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67094124"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-ordering"></a>Zelf studie: integratie Azure Active Directory met Predictix-volg orde

In deze zelf studie leert u hoe u Predictix ordening integreert met Azure Active Directory (Azure AD).
Deze integratie biedt de volgende voor delen:

* U kunt Azure AD gebruiken om te bepalen wie toegang heeft tot de Predictix-bestelling.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Predictix-bestelling (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts op één centrale locatie beheren: de Azure Portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Predictix-ordening wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/)aanvragen.
* Een Predictix-bestel abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u eenmalige aanmelding voor Azure AD in een test omgeving.

* Predictix best Ellen ondersteunt door SP geïnitieerde SSO.

## <a name="add-predictix-ordering-from-the-gallery"></a>Predictix-volg orde toevoegen vanuit de galerie

Als u de integratie van Predictix-volg orde in azure AD wilt instellen, moet u Predictix-bestelling vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Selecteer **Azure Active Directory**in het linkerdeel venster van de [Azure Portal](https://portal.azure.com):

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar**alle toepassingen**in **bedrijfs toepassingen** > :

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **nieuwe toepassing** boven aan het venster:

    ![Nieuwe toepassing selecteren](common/add-new-app.png)

4. Voer **Predictix-volg orde**in het zoekvak in. Selecteer **Predictix-volg orde** in de zoek resultaten en selecteer vervolgens **toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD met Predictix-bestelling configureren en testen met behulp van een test gebruiker met de naam Julia Simon.
Om eenmalige aanmelding in te scha kelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Predictix-volg orde.

U moet de volgende stappen uitvoeren om eenmalige aanmelding voor Azure AD te configureren en te testen met Predictix-bestelling:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te scha kelen.
2. **[Configureer Predictix best Ellen voor eenmalige aanmelding](#configure-predictix-ordering-single-sign-on)** aan de kant van de toepassing.
3. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om eenmalige aanmelding voor Azure AD in te scha kelen voor de gebruiker.
5. **[Maak een Predictix-test gebruiker](#create-a-predictix-ordering-test-user)** die is gekoppeld aan de Azure AD-weer gave van de gebruiker.
6. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u eenmalige aanmelding voor Azure AD in de Azure Portal.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Predictix-bestelling:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Predictix best Ellen** voor de integratie van toepassingen de optie **eenmalige aanmelding**:

    ![Eenmalige aanmelding selecteren](common/select-sso.png)

2. Selecteer in het dialoog venster **eenmalige aanmelding selecteren** de optie **SAML/WS-** gegevensinvoermodus om eenmalige aanmelding in te scha kelen:

    ![Selecteer een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het **bewerkings** pictogram om het dialoog venster **basis configuratie van SAML** te openen:

    ![Bewerkpictogram](common/edit-urls.png)

4. Voer in het dialoog venster **basis configuratie van SAML** de volgende stappen uit.

    ![Het dialoog venster basis configuratie van SAML](common/sp-identifier.png)

    1. Voer in het vak **URL voor aanmelden** een URL in dit patroon in:

       `https://<companyname-pricing>.ordering.predictix.com/sso/request`

    1. Voer in het vak **id (Entiteits-ID)** een URL in dit patroon in:

        | |
        |--|
        | `https://<companyname-pricing>.dev.ordering.predictix.com` |
        | `https://<companyname-pricing>.ordering.predictix.com` |
        | |

    > [!NOTE]
    > Deze waarden zijn tijdelijke aanduidingen. U moet de daad werkelijke aanmeldings-URL en-id gebruiken. Neem contact op met het [ondersteunings team van Predictix](https://www.predix.io/support/) om de waarden op te halen. U kunt ook verwijzen naar de patronen die worden weer gegeven in het dialoog venster **basis configuratie van SAML** in de Azure Portal.

5. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **Download** koppeling naast **certificaat (base64)**, volgens uw vereisten en sla het certificaat op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Predictix-bestelling instellen** de juiste url's, op basis van uw vereisten:

    ![De configuratie-Url's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    2. **Id van Azure AD**.

    3. **Afmeldings-URL**.

### <a name="configure-predictix-ordering-single-sign-on"></a>Eenmalige aanmelding Predictix best Ellen configureren

Als u eenmalige aanmelding wilt configureren voor de Predictix, moet u het certificaat dat u hebt gedownload en de Url's die u hebt gekopieerd van de Azure Portal naar het [Predictix best Ellen ondersteunings team](https://www.predix.io/support/)verzenden. Dit team zorgt ervoor dat de SAML SSO-verbinding aan beide zijden correct is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker met de naam Julia Simon in de Azure Portal.

1. Selecteer in de Azure Portal **Azure Active Directory** in het linkerdeel venster, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**:

    ![Selecteer Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm:

    ![Nieuwe gebruiker selecteren](common/new-user.png)

3. Voer in het dialoog venster **gebruiker** de volgende stappen uit.

    ![Dialoog venster gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer in het vak **gebruikers naam** **BrittaSimon@\<yourcompanydomain> in.\< extensie>**. (Bijvoorbeeld BrittaSimon@contoso.com.)

    1. Selecteer **wacht woord weer geven**en noteer de waarde in het vak **wacht woord** .

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure AD te gebruiken door haar toegang tot Predictix-bestelling te verlenen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens Predictix- **volg orde**:

    ![Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Predictix-volg orde**.

    ![Lijst met toepassingen](common/all-applications.png)

3. Selecteer in het linkerdeel venster **gebruikers en groepen**:

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** de optie **Julia Simon** in de lijst met gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een waarde van een rol verwacht in de SAML-verklaring, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik op de knop **selecteren** onder aan het scherm.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-predictix-ordering-test-user"></a>Een test gebruiker voor Predictix-volg orde maken

Vervolgens moet u een gebruiker met de naam Julia Simon maken in Predictix-volg orde. Werk samen met het [ondersteunings team voor Predictix-bestellingen](https://www.predix.io/support/) om gebruikers toe te voegen. Gebruikers moeten worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u de configuratie van de eenmalige aanmelding voor Azure AD testen met behulp van het toegangs venster.

Wanneer u de tegel Predictix best Ellen selecteert in het toegangs venster, moet u automatisch worden aangemeld bij het exemplaar van de Predictix-bestelling waarvoor u SSO hebt ingesteld. Zie voor meer informatie [apps openen en gebruiken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
