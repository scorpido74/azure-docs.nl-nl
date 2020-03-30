---
title: 'Zelfstudie: Azure Active Directory-integratie met Jobscience | Microsoft Documenten'
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
ms.openlocfilehash: 2f0ea5b922b2c958aabf5be3a6123bb81a8f0234
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048513"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Zelfstudie: Azure Active Directory-integratie met Jobscience

In deze zelfstudie leert u hoe u Jobscience integreren met Azure Active Directory (Azure AD).

Het integreren van Jobscience met Azure AD biedt u de volgende voordelen:

- U in Azure AD bepalen wie toegang heeft tot Jobscience
- U uw gebruikers in staat stellen om automatisch aangemeld te worden bij Jobscience (Single Sign-On) met hun Azure AD-accounts
- U uw accounts beheren op één centrale locatie: de Azure-portal

Als u meer informatie wilt over de integratie van De SaaS-app met Azure AD, raadpleegt u [wat toepassingstoegang en eenmalige aanmelding is met Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Jobscience wilt configureren, hebt u de volgende items nodig:

- Een Azure AD-abonnement
- Een Jobscience-abonnement met eenmalige aanmelding

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u geen Azure AD-proefomgeving hebt, u hier een proefversie van één maand krijgen: [Proefaanbieding](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeschrijving
In deze zelfstudie test u Azure AD single sign-on in een testomgeving. Het scenario dat in deze zelfstudie wordt beschreven, bestaat uit twee belangrijke bouwstenen:

1. Jobscience toevoegen vanuit de galerie
1. Azure AD-aanmelding configureren en testen

## <a name="adding-jobscience-from-the-gallery"></a>Jobscience toevoegen vanuit de galerie
Als u de integratie van Jobscience in Azure AD wilt configureren, moet u Jobscience vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Jobscience vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram. 

    ![Active Directory][1]

1. Navigeer naar **Enterprise-toepassingen**. Ga dan naar **Alle toepassingen.**

    ![Toepassingen][2]
    
1. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![Toepassingen][3]

1. Typ **Jobscience**in het zoekvak .

    ![Een Azure AD-testgebruiker maken](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. Selecteer **Jobscience**in het deelvenster Resultaten en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![Een Azure AD-testgebruiker maken](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD-aanmelding configureren en testen
In deze sectie configureert en test u Azure AD single sign-on met Jobscience op basis van een testgebruiker genaamd 'Britta Simon'.

Voor eenmalige aanmelding aan het werk moet Azure AD weten wat de tegenhangergebruiker in Jobscience is voor een gebruiker in Azure AD. Met andere woorden, er moet een koppeling worden gemaakt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Jobscience.

Wijs in Jobscience de waarde van de **gebruikersnaam** in Azure AD toe als de waarde van de **gebruikersnaam** om de koppelingsrelatie vast te stellen.

Als u Azure AD-single sign-on met Jobscience wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD Single Sign-On configureren](#configuring-azure-ad-single-sign-on)** om uw gebruikers in staat te stellen deze functie te gebruiken.
1. **[Een Azure AD-testgebruiker maken](#creating-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding met Britta Simon te testen.
1. **[Een Jobscience-testgebruiker maken](#creating-a-jobscience-test-user)** - om een tegenhanger van Britta Simon in Jobscience te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[De Azure AD-testgebruiker](#assigning-the-azure-ad-test-user)** toewijzen - zodat Britta Simon azure AD-aanmelding kan gebruiken.
1. **[Eén aanmelding testen](#testing-single-sign-on)** - om te controleren of de configuratie werkt.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD-aanmelding configureren

In deze sectie schakelt u Azure AD single sign-on in de Azure-portal in en configureert u eenmalige aanmelding in uw Jobscience-toepassing.

**Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Jobscience te configureren:**

1. Klik in de **Azure-portal**op de pagina **Jobscience-toepassingsintegratie** op Eén aanmelding .

    ![Eenmalige aanmelding configureren][4]

1. Selecteer in het dialoogvenster **Eén aanmelding** **modus** als **op SAML gebaseerde aanmelding** om eenmalige aanmelding in te schakelen.
 
    ![Eenmalige aanmelding configureren](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Voer in de sectie **Jobscience Domain en URL's** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Krijg deze waarde door [Jobscience Client support team](https://www.jobscience.com/support) of van het SSO profiel dat u maakt, die later in de tutorial wordt uitgelegd. 
 
1. Klik in de sectie **SAML-ondertekeningscertificaat** op **Certificaat (Base64)** en sla het certificaatbestand op uw computer op.

    ![Eenmalige aanmelding configureren](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Klik op de knop **Save**.

    ![Eenmalige aanmelding configureren](./media/jobscience-tutorial/tutorial_general_400.png)

1. Klik in de sectie **Jobscience-configuratie** op **Jobscience configureren** om **aanmeldingsvenster configureren** te openen. Kopieer de **URL voor afmelding, SAML-entiteits-id en DE URL van de SAML-aanmeldingsservice** vanuit de **sectie Snelnaslag.**

    ![Eenmalige aanmelding configureren](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Log in op uw Jobscience-bedrijfssite als beheerder.

1. Ga naar **Setup**.
   
   ![Installatie](./media/jobscience-tutorial/IC784358.png "Instellen")

1. Klik in het linkernavigatiedeelvenster in de sectie **Beheren** op **Domeinbeheer** om de gerelateerde sectie uit te vouwen en klik vervolgens op **Mijn domein** om de pagina **Mijn domein** te openen. 
   
   ![Mijn domein](./media/jobscience-tutorial/ic767825.png "Mijn domein")

1. Als u wilt controleren of uw domein correct is ingesteld, controleert u of het zich in Stap**4 geïmplementeerd in gebruikers**bevindt en controleert u uw " Mijn**domeininstellingen**".

    ![Domein geïmplementeerd op gebruiker](./media/jobscience-tutorial/ic784377.png "Domein geïmplementeerd op gebruiker")

1. Klik op de bedrijfssite van Jobscience op **Beveiligingsbesturingselementen**en klik vervolgens op **Instellingen voor aanmelding voor één aanmelding**.
    
    ![Beveiligingsmaatregelen](./media/jobscience-tutorial/ic784364.png "Beveiligingsmaatregelen")

1. Voer in de sectie **Instellingen voor aanmelding** smaken de volgende stappen uit:
    
    ![Instellingen voor eenmalig aanmelden](./media/jobscience-tutorial/ic781026.png "Instellingen voor eenmalig aanmelden")
    
    a. Selecteer **SAML Enabled**.

    b. Klik op **Nieuw**.

1. Voer in het dialoogvenster **Instelbewerking** van SAML de volgende stappen uit:
    
    ![INSTELLING VOOR eenmalig aanmelden van SAML](./media/jobscience-tutorial/ic784365.png "INSTELLING VOOR eenmalig aanmelden van SAML")
    
    a. Typ in het tekstvak **Name** een naam voor de configuratie.

    b. Plak **in het** tekstvak Uitgever de waarde van **SAML-entiteits-id**, die u hebt gekopieerd van Azure-portal.

    c. Typ in het tekstvak **Entiteit-id**`https://salesforce-jobscience.com`

    d. Klik **op Bladeren** om uw Azure AD-certificaat te uploaden.

    e. Als **SAML-identiteitstype** **selecteert u De federatie-id van het object Gebruiker**.

    f. Als **SAML-identiteitslocatie**selecteert u **Identiteit in het element NameIdentfier van de instructie Onderwerp**.

    g. Plak in het tekstvak **URL van de login-account** van de identiteitsprovider de waarde van de URL van **de SAML Single Sign-On Service**, die u hebt gekopieerd uit azure-portal.

    h. Plak in **het tekstvak URL-tekstvak van de identiteitsprovider** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd van azure-portal.

    i. Klik op **Opslaan**.

1. Klik in het linkernavigatiedeelvenster in de sectie **Beheren** op **Domeinbeheer** om de gerelateerde sectie uit te vouwen en klik vervolgens op **Mijn domein** om de pagina **Mijn domein** te openen. 
    
    ![Mijn domein](./media/jobscience-tutorial/ic767825.png "Mijn domein")

1. Klik op de pagina **Mijn domein** in de sectie **Aanmeldingspagina branding** op **Bewerken**.
    
    ![Aanmeldingspaginabranding](./media/jobscience-tutorial/ic767826.png "Aanmeldingspaginabranding")

1. Op de pagina **Aanmeldingspagina branding** wordt in de sectie **Verificatieservice** de naam van uw **SAML SSO-instellingen** weergegeven. Selecteer deze en klik op **Opslaan**.
    
    ![Aanmeldingspaginabranding](./media/jobscience-tutorial/ic784366.png "Aanmeldingspaginabranding")

1. Klik op de **instellingen voor eenmalig aanmelden** in de **menusectie Beveiligingsbesturingselementen** om de door de SP geïnitieerde URL voor eenmalig aanmelden te krijgen.

    ![Beveiligingsmaatregelen](./media/jobscience-tutorial/ic784368.png "Beveiligingsmaatregelen")
    
    Klik op het SSO-profiel dat u in de bovenstaande stap hebt gemaakt. Op deze pagina ziet u de URL voor `https://companyname.my.salesforce.com?so=companyid`eenmalig teken voor uw bedrijf (bijvoorbeeld .    

> [!TIP]
> U kunt nu een beknopte versie van deze instructies in [Azure Portal](https://portal.azure.com) lezen terwijl u de app instelt!  Klik nadat u deze app onder **Active Directory > Bedrijfstoepassingen** hebt toegevoegd op het tabblad **Eenmalige aanmelding** en open de ingesloten documentatie via het gedeelte **Configuratie** onderaan. U hier meer lezen over de ingesloten documentatiefunctie: [Azure AD-ingesloten documentatie]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken
Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

![Azure AD-gebruiker maken][100]

**Voer de volgende stappen uit om een testgebruiker in Azure AD te maken:**

1. Klik in de **Azure-portal**in het linkernavigatiedeelvenster op **azure Active Directory-pictogram.**

    ![Een Azure AD-testgebruiker maken](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Als u de lijst met gebruikers wilt weergeven, gaat u naar **Gebruikers en groepen** en klikt u op Alle **gebruikers**.
    
    ![Een Azure AD-testgebruiker maken](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Als u het dialoogvenster **Gebruiker** wilt openen, **klikt** u boven aan het dialoogvenster toevoegen.
 
    ![Een Azure AD-testgebruiker maken](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Voer op de pagina **Gebruiker** de volgende stappen uit:
 
    ![Een Azure AD-testgebruiker maken](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Typ **BrittaSimon**in het tekstvak **Naam** .

    b. Typ in het tekstvak **Gebruikersnaam** het **e-mailadres** van BrittaSimon.

    c. Selecteer **Wachtwoord weergeven** en noteer de waarde van het **wachtwoord**.

    d. Klik **op Maken**.
 
### <a name="creating-a-jobscience-test-user"></a>Een Jobscience-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Jobscience, moeten ze worden ingericht in Jobscience. In het geval van Jobscience is inrichten een handmatige taak.

>[!NOTE]
>U alle andere hulpprogramma's voor het maken van Gebruikersaccounts voor Jobscience gebruiken om Azure Active Directory-gebruikersaccounts in te richten.
>  
        
**Voer de volgende stappen uit om de inrichting van gebruikers te configureren:**

1. Log in op uw **Jobscience-bedrijfssite** als beheerder.

1. Ga naar Setup.
   
   ![Installatie](./media/jobscience-tutorial/ic784358.png "Instellen")
1. Ga naar **Gebruikers \> beheren**.
   
   ![Gebruikers](./media/jobscience-tutorial/ic784369.png "Gebruikers")
1. Klik op **New User**.
   
   ![Alle gebruikers](./media/jobscience-tutorial/ic784370.png "Alle gebruikers")
1. Voer **in** het dialoogvenster Gebruiker bewerken de volgende stappen uit:
   
   ![Gebruiker bewerken](./media/jobscience-tutorial/ic784371.png "Gebruiker bewerken")
   
   a. Typ in het tekstvak **Voornaam** een voornaam van de gebruiker zoals Britta.
   
   b. Typ in het tekstvak **Achternaam** een achternaam van de gebruiker zoals Simon.
   
   c. Typ in het tekstvak **Alias** een aliasnaam van de gebruiker, zoals britta's.

   d. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

   e. Typ in het tekstvak **Gebruikersnaam** een gebruikersnaam Brittasimon@contoso.comvan de gebruiker als .

   f. Typ in het tekstvak **Nick Name** een nicknaam van de gebruiker zoals Simon.

   g. Klik op **Opslaan**.

    
> [!NOTE]
> De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="assigning-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Jobscience.

![Gebruiker toewijzen][200] 

**Als u Britta Simon aan Jobscience wilt toewijzen, voert u de volgende stappen uit:**

1. Open in de Azure-portal de weergave toepassingen en navigeer vervolgens naar de mapweergave en ga naar **Enterprise-toepassingen** en klik vervolgens op **Alle toepassingen**.

    ![Gebruiker toewijzen][201] 

1. Selecteer **Jobscience**in de lijst met toepassingen .

    ![Eenmalige aanmelding configureren](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. Klik in het menu aan de linkerkant op **Gebruikers en groepen**.

    ![Gebruiker toewijzen][202] 

1. Klik op de knop **Add**. Selecteer **vervolgens Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![Gebruiker toewijzen][203]

1. Selecteer **Britta Simon** in het dialoogvenster Gebruikers **en groepen** in de lijst Gebruikers.

1. Klik **op Knop Selecteren** in het dialoogvenster Gebruikers en **groepen.**

1. Klik **op De** knop Toewijzen in het dialoogvenster Toewijzing **toevoegen.**
    
### <a name="testing-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Jobscience in het toegangspaneel klikt, wordt u automatisch aangemeld bij uw Jobscience-toepassing.
Zie [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](tutorial-list.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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

