---
title: 'Zelf studie: integratie Azure Active Directory met SAML SSO voor Jira door Resolution GmbH | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAML SSO for Jira by resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6f6cb87cf7628c48ce6adf12336c4b712dc0ff9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82202550"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Zelf studie: integratie Azure Active Directory met SAML SSO voor Jira by Solution GmbH

In deze zelf studie leert u hoe u SAML SSO kunt instellen voor Jira door Solution GmbH met Azure Active Directory (Azure AD).
Integratie van SAML SSO for Jira by resolution GmbH met Azure AD biedt de volgende voordelen:

* U kunt in azure AD beheren wie zich kan aanmelden bij Jira met de SAML SSO-invoeg toepassing met de oplossing GmbH.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Jira met hun Azure AD-accounts door gebruik te maken van SAML SSO voor Jira door de oplossing GmbH (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie en SAML SSO wilt configureren voor Jira door Solution GmbH, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een proefversie van één maand krijgen
* Een abonnement op SAML SSO for Jira by resolution GmbH waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAML SSO voor Jira by Solution GmbH ondersteunt door **SP** en **IDP** geïnitieerde SSO

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Een bedrijfs toepassing voor eenmalige aanmelding toevoegen

Om eenmalige aanmelding in te stellen in azure AD, moet u een nieuwe zakelijke toepassing toevoegen. In de galerie is er een vooraf geconfigureerde voor instelling voor de toepassing voor deze **SAML SSO voor Jira by Solution GmbH**.

**Voer de volgende stappen uit om SAML SSO for Jira by resolution GmbH toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **bedrijfs toepassingen**en klik vervolgens op **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, klikt u op de knop **nieuwe toepassing** aan de bovenkant van het dialoog venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **SAML SSO voor Jira by Solution GmbH**, selecteer **SAML SSO for Jira by resolution GmbH** in het deel venster result en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen. U kunt ook de naam van de Enter prise-app wijzigen.

     ![SAML SSO for Jira by resolution GmbH in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Eenmalige aanmelding configureren en testen met de SAML SSO-invoeg toepassing en Azure AD

In deze sectie gaat u eenmalige aanmelding testen en configureren voor Jira voor een Azure AD-gebruiker. Dit wordt gedaan voor een test gebruiker met de naam **Julia Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in SAML SSO for Jira by resolution GmbH tot stand is gebracht.

Om eenmalige aanmelding te configureren en te testen, moet u de volgende stappen uitvoeren:

1. **[De Azure AD-bedrijfs toepassing voor eenmalige aanmelding configureren](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** : de Azure AD-bedrijfs toepassing voor eenmalige aanmelding configureren
2. **[De SAML SSO-invoeg toepassing van uw Jira-exemplaar configureren](#configure-the-saml-sso-plugin-of-your-jira-instance)** : Configureer de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : een test gebruiker maken in azure AD.
1. **[Wijs de gebruiker van Azure AD-test](#assign-the-azure-ad-test-user)** toe om de test gebruiker in te scha kelen voor gebruik van de eenmalige aanmelding aan de Azure-zijde.
1. **[De test gebruiker maken in Jira](#create-the-test-user-also-in-jira)** : Maak een vergelijk test gebruiker in Jira voor de Azure AD-test gebruiker.
1. **[Eenmalige aanmelding testen](#test-single-sign-on)** : Controleer of de configuratie werkt.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>De Azure AD-bedrijfs toepassing configureren voor eenmalige aanmelding

In deze sectie kunt u eenmalige aanmelding instellen in de Azure Portal.

Voer de volgende stappen uit om de eenmalige aanmelding te configureren met SAML SSO voor Jira door Solution GmbH:

1. Selecteer in het [Azure Portal](https://portal.azure.com/), in de zojuist gemaakte **SAML SSO voor Jira door de oplossing GmbH** Enter prise-toepassing, de optie **eenmalige aanmelding** in het linkerdeel venster.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer bij **Selecteer een methode voor eenmalige aanmelding**de **SAML** -modus om eenmalige aanmelding in te scha kelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Klik daarna op het **bewerkings** pictogram om het dialoog venster **basis configuratie van SAML** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    ![Informatie over domein en URL's voor eenmalige aanmelding met SAML SSO for Jira by resolution GmbH](common/idp-intiated.png)

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://<server-base-url>/plugins/servlet/samlsso`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klik op **aanvullende Url's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Informatie over domein en URL's voor eenmalige aanmelding met SAML SSO for Jira by resolution GmbH](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Voor de id, de antwoord-URL en de aanmeldings-URL vervangt ** \<u de server-base-URL>** door de basis-URL van uw Jira-exemplaar. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal. Als u een probleem ondervindt, neemt u contact met ons op via [SAML SSO voor Jira door de Resolution GmbH client support-team](https://www.resolution.de/go/support).

4. Down load op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **XML voor federatieve meta gegevens** en sla deze op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>De SAML SSO-invoeg toepassing van uw Jira-exemplaar configureren 

1. Meld u in een ander browser venster aan bij uw Jira-exemplaar als beheerder.

2. Beweeg de muis aanwijzer over de tandwiel aan de rechter kant en klik op **apps beheren**.
    
    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon1.png)

3. Als u wordt omgeleid naar de beheerders toegangs pagina, voert u het **wacht woord** in en klikt u op de knop **bevestigen** .

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon2.png)

4. Jira leidt u normaal gesp roken over naar de Atlassian Marketplace. Als dat niet het geval is, klikt u op **nieuwe apps zoeken** in het linkerdeel venster. Zoek naar **SAML single sign on (SSO) voor Jira** en klik op de knop **installeren** om de SAML-invoeg toepassing te installeren.

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/store.png)

5. De installatie van de invoegtoepassing wordt gestart. Wanneer u klaar bent, klikt u op de knop **sluiten** .

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/store-2.png)

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/store-3.png)

6. Klik vervolgens op **beheren**.

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/store-4.png)
    
8. Klik daarna op **configureren** om de zojuist geïnstalleerde invoeg toepassing te configureren.

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/store-5.png)

9. Klik in de configuratie wizard voor de **SAML SingleSignOn-invoeg toepassing** op **nieuwe IDP toevoegen** om Azure ad te configureren als een nieuwe ID-provider.

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon4.png) 

10. Voer de volgende stappen uit op de pagina **uw SAML-ID-provider kiezen** :

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon5a.png)
 
    a. Stel **Azure AD** als het type id-provider.
    
    b. Voeg de **naam** van de ID-provider toe (bijvoorbeeld Azure AD).
    
    c. Voeg een (optionele) **Beschrijving** toe van de ID-provider (bijvoorbeeld Azure AD).
    
    d. Klik op **Volgende**.
    
11. Klik op de pagina configuratie van de **identiteits provider** op **volgende**.
 
    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon5b.png)

12. Op de pagina **SAML-IDP-metagegevens importeren** voert u de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon5c.png)

    a. Klik op de knop **XML-bestand voor meta gegevens selecteren** en kies het **XML-bestand met federatieve meta gegevens** dat u eerder hebt gedownload.

    b. Klik op de knop **importeren** .
     
    c. Wacht even totdat het importeren is gelukt.  
     
    d. Klik op de knop **Next**
    
13. Op de pagina **gebruikers-id en trans formatie** klikt u op de knop **volgende** .

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon5d.png)
    
14. Klik op de pagina **maken en bijwerken** van de gebruiker op **& volgende** om de instellingen op te slaan.
    
    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon6a.png)
    
15. Klik op de pagina **uw instellingen testen** op **overs Laan testen & hand matig configureren** om de gebruikers test nu over te slaan. Dit wordt in de volgende sectie uitgevoerd en vereist enkele instellingen in de Azure Portal.
    
    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon6b.png)
    
16. Klik op **OK** om de waarschuwing over te slaan.
    
    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken. Met de gebruiker wordt eenmalige aanmelding getest.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Kies **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. Voer de volgende stappen uit in de eigenschappen van de **gebruiker**:

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **naam** **Julia Simon**in.
  
    b. Typ <b>BrittaSimon@contoso.com</b>in het veld **gebruikers naam** .

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie voegt u Julia Simon toe aan de bedrijfs toepassing, zodat hij eenmalige aanmelding kan gebruiken.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**. 

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Zoek in de lijst toepassingen naar de Enter prise-toepassing die u aan het begin van deze zelf studie hebt gemaakt. Als u de stappen van de zelf studie volgt, wordt het **SAML SSO genoemd voor Jira by Solution GmbH**. Als u het een andere naam hebt gegeven, zoekt u deze naam.

    ![De koppeling SAML SSO for Jira by resolution GmbH in de lijst met toepassingen](common/all-applications.png)

3. Klik in het linkerdeel venster op **gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** de optie **Julia Simon** van de lijst met gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol in de SAML-bewering verwacht, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u vervolgens op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-the-test-user-also-in-jira"></a>Maak de test gebruiker ook in Jira

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij SAML SSO voor Jira door de oplossing GmbH, moeten ze worden ingericht in SAML SSO voor Jira door de oplossing GmbH. In het geval van deze zelf studie moet u de inrichting hand matig uitvoeren. Er zijn echter ook andere inrichtings modellen beschikbaar voor de SAML SSO-invoeg toepassing per oplossing, bijvoorbeeld **just-in-time** -inrichting. Raadpleeg voor meer informatie de documentatie op [SAML SSO door de oplossing GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Als u hierover een vraag hebt, neemt u contact op met ondersteuning bij de ondersteuning van de [oplossing](https://www.resolution.de/go/support).

**Voer de volgende stappen uit om een gebruikers account hand matig in te richten:**

1. Meld u aan bij Jira instance als beheerder.

2. Beweeg de muis aanwijzer over de tandwiel en selecteer **gebruikers beheer**.

   ![Werknemer toevoegen](./media/samlssojira-tutorial/user1.png)

3. Als u wordt omgeleid naar de beheerders toegangs pagina, voert u het **wacht woord** in en klikt u op de knop **bevestigen** .

    ![Werknemer toevoegen](./media/samlssojira-tutorial/user2.png) 

4. Klik onder de sectie **gebruikers beheer** op **gebruiker maken**.

    ![Werknemer toevoegen](./media/samlssojira-tutorial/user3-new.png) 

5. Voer de volgende stappen uit op de dialoog pagina **' nieuwe gebruiker maken '** . U moet de gebruiker op dezelfde manier maken als in azure AD:

    ![Werknemer toevoegen](./media/samlssojira-tutorial/user4-new.png) 

    a. Typ in het tekstvak **e-mail adres** het e-mail adres van <b>BrittaSimon@contoso.com</b>de gebruiker:.

    b. Typ in het tekstvak **volledige naam** de volledige naam van de gebruiker: **Julia Simon**.

    c. Typ in het tekstvak **username** het e-mail adres van de gebruiker <b>BrittaSimon@contoso.com</b>:. 

    d. Voer in het tekstvak **wacht woord** het wacht woord van de gebruiker in.

    e. Klik op **gebruiker maken** om het maken van de gebruiker te volt ooien.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Als u in het toegangsvenster op de tegel SAML SSO for Jira by resolution GmbH klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van SAML SSO for Jira by resolution GmbH waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

U kunt ook eenmalige aanmelding testen als u navigeert naar `https://<server-base-url>/plugins/servlet/samlsso`. Vervang ** \<server-base-URL>** door de basis-URL van uw Jira-instantie.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Omleiding van eenmalige aanmelding voor Jira inschakelen

Zoals vermeld in de sectie voor, zijn er momenteel twee manieren om eenmalige aanmelding te activeren. Door de **Azure Portal** te gebruiken of **een speciale koppeling naar uw Jira-exemplaar te**gebruiken. Met de SAML SSO-invoeg toepassing via Solution GmbH kunt u eenmalige aanmelding activeren door eenvoudigweg **toegang te krijgen tot een URL die verwijst naar uw Jira-exemplaar**.

In essentie worden alle gebruikers die toegang hebben tot Jira, omgeleid naar de eenmalige aanmelding na het activeren van een optie in de invoeg toepassing.

Ga als volgt te werk in **uw Jira-exemplaar**om SSO-omleiding te activeren:

1. Open de configuratie pagina van de SAML SSO-invoeg toepassing in Jira.
1. Klik op **omleiding** in het linkerdeel venster.
![](./media/samlssojira-tutorial/ssore1.png)

1. Tik **SSO-omleiding inschakelen**.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Klik in de rechter bovenhoek op de knop **instellingen opslaan** .

Nadat u de optie hebt geactiveerd, kunt u de prompt voor gebruikers naam en wacht woord nog steeds bereiken als de optie **nosso inschakelen** is `https://\<server-base-url>/login.jsp?nosso`ingeschakeld door naar te navigeren. Vervang altijd de ** \<server-base-URL>** door de basis-URL.


## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

