---
title: 'Zelf studie: integratie Azure Active Directory met Jobscience | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Jobscience.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23d2694aa4936090367cf881379f81911ae70f9d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870545"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Zelf studie: integratie Azure Active Directory met Jobscience

In deze zelf studie leert u hoe u Jobscience integreert met Azure Active Directory (Azure AD).

Het integreren van Jobscience met Azure AD biedt de volgende voor delen:

- U kunt beheren in azure AD die toegang heeft tot Jobscience
- U kunt uw gebruikers in staat stellen om automatisch aangemeld te komen bij Jobscience (eenmalige aanmelding) met hun Azure AD-accounts
- U kunt uw accounts op één centrale locatie beheren: de Azure Portal

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](../manage-apps/what-is-single-sign-on.md)als u meer wilt weten over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Jobscience wilt configureren, hebt u de volgende items nodig:

- Een Azure AD-abonnement
- Een abonnement met eenmalige aanmelding voor Jobscience

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u geen Azure AD-proef omgeving hebt, kunt u hier een proef versie van één maand krijgen: [proef versie](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeschrijving
In deze zelf studie test u eenmalige aanmelding voor Azure AD in een test omgeving. Het scenario dat in deze zelf studie wordt beschreven, bestaat uit twee belang rijke bouw stenen:

1. Jobscience toevoegen uit de galerie
1. Eenmalige aanmelding voor Azure AD configureren en testen

## <a name="adding-jobscience-from-the-gallery"></a>Jobscience toevoegen uit de galerie
Als u de integratie van Jobscience in azure AD wilt configureren, moet u Jobscience uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Jobscience toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram. 

    ![Active Directory][1]

1. Navigeer naar **bedrijfs toepassingen**. Ga vervolgens naar **alle toepassingen**.

    ![Toepassingen][2]
    
1. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![Toepassingen][3]

1. Typ **Jobscience**in het zoekvak.

    ![Een Azure AD-testgebruiker maken](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. Selecteer in het deel venster resultaten **Jobscience**en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

    ![Een Azure AD-testgebruiker maken](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Eenmalige aanmelding voor Azure AD configureren en testen
In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Jobscience op basis van een test gebruiker met de naam ' Julia Simon '.

Voor gebruik van eenmalige aanmelding moet Azure AD weten wat de tegen gebruiker in Jobscience is voor een gebruiker in azure AD. Met andere woorden, een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Jobscience moet tot stand worden gebracht.

In Jobscience wijst u de waarde van de **gebruikers naam** in azure AD toe als de waarde van de **naam** van de gebruiker om de koppelings relatie tot stand te brengen.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Jobscience, moet u de volgende bouw stenen volt ooien:

1. **[Eenmalige aanmelding van Azure AD configureren](#configuring-azure-ad-single-sign-on)** om uw gebruikers in staat te stellen deze functie te gebruiken.
1. **[Een Azure AD-test gebruiker maken](#creating-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met Julia Simon.
1. Het **[maken van een Jobscience-test gebruiker](#creating-a-jobscience-test-user)** : als u een equivalent van Julia Simon in Jobscience wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[De Azure AD-test gebruiker toewijzen](#assigning-the-azure-ad-test-user)** : om Julia Simon in staat te stellen om eenmalige aanmelding van Azure ad te gebruiken.
1. **[Eenmalige aanmelding testen](#testing-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configuring-azure-ad-single-sign-on"></a>Eenmalige aanmelding voor Azure AD configureren

In deze sectie schakelt u eenmalige aanmelding voor Azure AD in de Azure Portal en configureert u eenmalige aanmelding in uw Jobscience-toepassing.

**Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Jobscience:**

1. Klik in het Azure Portal op de pagina **Jobscience** toepassings integratie op **eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren][4]

1. Selecteer in het dialoog venster **eenmalige aanmelding** de optie **modus** als op **SAML gebaseerde aanmelding** om eenmalige aanmelding in te scha kelen.
 
    ![Eenmalige aanmelding configureren](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Voer de volgende stappen uit in de sectie **Jobscience domein en url's** :

    ![Eenmalige aanmelding configureren](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Haal deze waarde op door [Jobscience-client ondersteunings team](http://www.jobscience.com/support) of van het SSO-profiel dat u hebt gemaakt. dit wordt verderop in de zelf studie uitgelegd. 
 
1. Klik in de sectie **SAML-handtekening certificaat** op **certificaat (base64)** en sla het certificaat bestand op uw computer op.

    ![Eenmalige aanmelding configureren](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Klik op de knop **Save**.

    ![Eenmalige aanmelding configureren](./media/jobscience-tutorial/tutorial_general_400.png)

1. Klik in het gedeelte **Jobscience configuratie** op **Jobscience configureren** om **het venster aanmelden configureren** te openen. Kopieer de **URL voor het afmelden, de SAML-Entiteits-ID en de webservice voor de SSO-service voor eenmalige** aanmelding uit het **gedeelte snelle verwijzing.**

    ![Eenmalige aanmelding configureren](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Meld u als beheerder aan bij de Jobscience-bedrijfs site.

1. Ga naar **Setup**.
   
   ![Instellen](./media/jobscience-tutorial/IC784358.png "Instellen")

1. Klik in het navigatie deel venster links in de sectie **beheren** op **domein beheer** om de gerelateerde sectie uit te vouwen en klik vervolgens op **mijn domein** om de pagina **mijn domein** te openen. 
   
   ![Mijn domein](./media/jobscience-tutorial/ic767825.png "Mijn domein")

1. Als u wilt controleren of uw domein correct is ingesteld, controleert u of de**stap 4 is geïmplementeerd op gebruikers**en controleert u de**instellingen van mijn domein**.

    ![Domein geïmplementeerd voor gebruiker](./media/jobscience-tutorial/ic784377.png "Domein geïmplementeerd voor gebruiker")

1. Klik op de Jobscience-bedrijfs site op **beveiligings controles**en klik vervolgens op **instellingen voor eenmalige aanmelding**.
    
    ![Beveiligingsmaatregelen](./media/jobscience-tutorial/ic784364.png "Beveiligingsmaatregelen")

1. Voer de volgende stappen uit in de sectie **instellingen voor eenmalige aanmelding** :
    
    ![Instellingen voor eenmalige aanmelding](./media/jobscience-tutorial/ic781026.png "Instellingen voor eenmalige aanmelding")
    
    a. Selecteer **SAML Enabled**.

    b. Klik op **Nieuw**.

1. Voer de volgende stappen uit in het dialoog venster **instellingen voor eenmalige SAML-aanmelding bewerken** :
    
    ![Instelling voor eenmalige aanmelding voor SAML](./media/jobscience-tutorial/ic784365.png "Instelling voor eenmalige aanmelding voor SAML")
    
    a. Typ in het tekstvak **Name** een naam voor de configuratie.

    b. Plak in het tekstvak van de **Uitgever** de waarde van de **SAML-entiteit-id**, die u van Azure Portal hebt gekopieerd.

    c. Typ in het tekstvak **Entiteits-ID**`https://salesforce-jobscience.com`

    d. Klik op **Bladeren** om uw Azure AD-certificaat te uploaden.

    e. Als **SAML-identiteits type**, selecteert u **beweringen bevat de Federatie-id van het gebruikers object**.

    f. Als **SAML-identiteits locatie**selecteert u **identiteit in het NameIdentfier-element van de instructie subject**.

    g. Plak in het tekstvak **ID-provider aanmeld-URL** de waarde van de **service-URL voor eenmalige SAML-aanmelding**die u van Azure Portal hebt gekopieerd.

    h. Plak in het tekstvak voor de **Afmeldings-URL van de identiteits provider** de waarde van de **afmeldings-URL**, die u van Azure Portal hebt gekopieerd.

    i. Klik op **Opslaan**.

1. Klik in het navigatie deel venster links in de sectie **beheren** op **domein beheer** om de gerelateerde sectie uit te vouwen en klik vervolgens op **mijn domein** om de pagina **mijn domein** te openen. 
    
    ![Mijn domein](./media/jobscience-tutorial/ic767825.png "Mijn domein")

1. Klik op de pagina **mijn domein** in de sectie **huis stijl aanmeldings pagina** op **bewerken**.
    
    ![Huis stijl aanmeldings pagina](./media/jobscience-tutorial/ic767826.png "Huis stijl aanmeldings pagina")

1. Op de pagina **huis stijl aanmeldings pagina** , in de sectie **verificatie service** , wordt de naam van uw **SAML SSO-instellingen** weer gegeven. Selecteer deze en klik vervolgens op **Opslaan**.
    
    ![Huis stijl aanmeldings pagina](./media/jobscience-tutorial/ic784366.png "Huis stijl aanmeldings pagina")

1. Als u de aanmeldings-URL voor de door SP geïnitieerde aanmelding wilt ophalen, klikt u op de **instellingen voor eenmalige aanmelding** in de menu sectie **beveiligings besturings elementen** .

    ![Beveiligingsmaatregelen](./media/jobscience-tutorial/ic784368.png "Beveiligingsmaatregelen")
    
    Klik in de bovenstaande stap op het SSO-profiel dat u hebt gemaakt. Op deze pagina wordt de URL voor eenmalige aanmelding voor uw bedrijf weer gegeven `https://companyname.my.salesforce.com?so=companyid`(bijvoorbeeld.    

> [!TIP]
> U kunt nu een beknopte versie van deze instructies in [Azure Portal](https://portal.azure.com) lezen terwijl u de app instelt!  Klik nadat u deze app onder **Active Directory > Bedrijfstoepassingen** hebt toegevoegd op het tabblad **Eenmalige aanmelding** en open de ingesloten documentatie via het gedeelte **Configuratie** onderaan. U vindt hier meer informatie over de Inge sloten documentatie functie: [documentatie voor Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken
Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

![Azure AD-gebruiker maken][100]

**Als u een test gebruiker in azure AD wilt maken, voert u de volgende stappen uit:**

1. Klik in het **Azure Portal**, in het navigatie deel venster aan de linkerkant op **Azure Active Directory** pictogram.

    ![Een Azure AD-testgebruiker maken](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Als u de lijst met gebruikers wilt weer geven, gaat u naar **gebruikers en groepen** en klikt u op **alle gebruikers**.
    
    ![Een Azure AD-testgebruiker maken](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Klik boven in het dialoog venster op **toevoegen** om het dialoog venster **gebruiker** te openen.
 
    ![Een Azure AD-testgebruiker maken](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Voer de volgende stappen uit op de pagina **gebruikers** dialoogvenster:
 
    ![Een Azure AD-testgebruiker maken](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Typ **BrittaSimon**in het tekstvak **naam** .

    b. Typ in het tekstvak **gebruikers naam** het **e-mail adres** van BrittaSimon.

    c. Selecteer **wacht woord weer geven** en noteer de waarde van het **wacht woord**.

    d. Klik op **maken**.
 
### <a name="creating-a-jobscience-test-user"></a>Een Jobscience-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij Jobscience, moeten ze worden ingericht in Jobscience. In het geval van Jobscience is inrichting een hand matige taak.

>[!NOTE]
>U kunt alle andere hulpprogram ma's voor het maken van Jobscience-gebruikers accounts of Api's die worden geleverd door Jobscience, gebruiken om Azure Active Directory gebruikers accounts in te richten.
>  
        
**Voer de volgende stappen uit om de inrichting van gebruikers te configureren:**

1. Meld u als beheerder aan bij de **Jobscience** -bedrijfs site.

1. Ga naar Setup.
   
   ![Instellen](./media/jobscience-tutorial/ic784358.png "Instellen")
1. Ga naar gebruikers van gebruikers **beheren \> **.
   
   ![Gebruikers](./media/jobscience-tutorial/ic784369.png "Gebruikers")
1. Klik op **New User**.
   
   ![Alle gebruikers](./media/jobscience-tutorial/ic784370.png "Alle gebruikers")
1. Voer de volgende stappen uit in het dialoog venster **gebruiker bewerken** :
   
   ![Gebruiker bewerken](./media/jobscience-tutorial/ic784371.png "Gebruiker bewerken")
   
   a. Typ in het tekstvak **voor de voor naam** een voor naam van de gebruiker, zoals Julia.
   
   b. Typ in het tekstvak **Achternaam** een achternaam van de gebruiker, zoals Simon.
   
   c. Typ in het tekstvak **alias** een alias naam van de gebruiker, zoals Brittas.

   d. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

   e. Typ in het tekstvak **gebruikers naam** de gebruikers naam van de gebruiker zoals Brittasimon@contoso.com.

   f. Typ in het tekstvak **bijnaam** de Nick naam van de gebruiker, zoals Simon.

   g. Klik op **Opslaan**.

    
> [!NOTE]
> De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="assigning-the-azure-ad-test-user"></a>De Azure AD-test gebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Jobscience.

![Gebruiker toewijzen][200] 

**Voer de volgende stappen uit om Julia Simon toe te wijzen aan Jobscience:**

1. Open in de Azure Portal de weer gave toepassingen en navigeer vervolgens naar de mapweergave en ga naar **bedrijfs toepassingen** en klik vervolgens op **alle toepassingen**.

    ![Gebruiker toewijzen][201] 

1. Selecteer in de lijst toepassingen de optie **Jobscience**.

    ![Eenmalige aanmelding configureren](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. Klik in het menu aan de linkerkant op **gebruikers en groepen**.

    ![Gebruiker toewijzen][202] 

1. Klik op de knop **Add**. Selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![Gebruiker toewijzen][203]

1. Selecteer in het dialoog venster **gebruikers en groepen** de optie **Julia Simon** in de lijst gebruikers.

1. Klik op de knop **selecteren** in het dialoog venster **gebruikers en groepen** .

1. Klik op de knop **toewijzen** in het dialoog venster **toewijzing toevoegen** .
    
### <a name="testing-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Jobscience in het toegangs venster klikt, wordt u automatisch aangemeld bij uw Jobscience-toepassing.
Zie [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](tutorial-list.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

