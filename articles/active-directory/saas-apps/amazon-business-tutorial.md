---
title: 'Zelf studie: integratie Azure Active Directory met Amazon Business | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Amazon Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2597a66-afd9-4f11-b14b-646b597bb6c2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7ac085beaa85a7ddf3a6c3bfc61820e8e5a63ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68496908"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Zelf studie: Amazon Business integreren met Azure Active Directory

In deze zelf studie leert u hoe u Amazon Business integreert met Azure Active Directory (Azure AD). Wanneer u [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Amazon Business.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij Amazon Business met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proef versie van één maand ontvangen.
* Een abonnement dat is ingeschakeld voor Amazon Business single sign-on (SSO). Ga naar de pagina [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) om een Amazon Business-account te maken.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie kunt u Azure AD SSO configureren en testen in een bestaand Amazon Business-account.

* Amazon Business ondersteunt SSO **met SP en IDP**
* Amazon Business ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-amazon-business-from-the-gallery"></a>Amazon Business toevoegen vanuit de galerie

Als u de integratie van Amazon Business in azure AD wilt configureren, moet u Amazon Business uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **Amazon Business** in het zoekvak.
1. Selecteer **Amazon Business** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO met Amazon Business configureren en testen met behulp van een test gebruiker met de naam **B. Simon**.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Amazon Business:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[Amazon Business SSO configureren](#configure-amazon-business-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Maak Amazon Business Test User](#create-amazon-business-test-user)** -om een soort tegen te brengen van B. Simon in Amazon Business dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **Amazon Business** Application Integration de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u in de gestarte modus **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    1. Typ in het tekstvak **id (Entiteits-ID)** een URL met behulp van een van de volgende patronen:
    
       | | |
       |-|-|
       | `https://www.amazon.com`|
       | `https://www.amazon.co.jp`|
       | `https://www.amazon.de`|

    1. Typ in het tekstvak **antwoord-URL** een URL met behulp van een van de volgende patronen:
    
       | | |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`|

       > [!NOTE]
       > De waarde van de antwoord-URL is niet de echte waarde. Werk deze waarde bij met de werkelijke antwoord-URL. U krijgt de `<idpid>` waarde uit de configuratie sectie voor Amazon Business SSO, die verderop in de zelf studie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **URL voor aanmelding** :`https://www.amazon.com/`

1. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Bewerk de kenmerken door te klikken op het pictogram **bewerken** in de sectie **gebruikers kenmerken & claims** .

    ![Kenmerken](media/amazon-business-tutorial/map-attribute3.png)

1. Bewerk kenmerken en kopieer de waarde van de **naam ruimte** van deze kenmerken in het klad blok.

    ![Kenmerken](media/amazon-business-tutorial/map-attribute4.png)

1. Daarnaast verwacht de Business-toepassing van Amazon nog enkele kenmerken die kunnen worden door gegeven aan het SAML-antwoord. Voer de volgende stappen uit in de sectie **gebruikers kenmerken & claims** van het dialoog venster **groeps claims** :

    a. Klik op de **pen** naast **groepen die zijn geretourneerd in claim**.

    ![installatiekopie](./media/amazon-business-tutorial/config04.png)

    ![installatiekopie](./media/amazon-business-tutorial/config05.png)

    b. Selecteer **alle groepen** in de lijst met keuze rondjes.

    c. Selecteer **groeps-id** als **bron kenmerk**.

    d. Schakel **de naam van het selectie vakje voor de groeps claim in** en voer de groeps naam in op basis van uw organisatie vereiste.

    e. Klik op **Opslaan**.

1. Ga op de pagina **eenmalige aanmelding met SAML instellen** naar de sectie SAML **-** **handtekening certificaat** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **Amazon Business instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Amazon Business SSO configureren

1. Meld u in een ander webbrowser venster aan bij uw zakelijke bedrijfs site van Amazon als beheerder.

1. Klik op het **gebruikers profiel** en selecteer **bedrijfs instellingen**.

    ![Gebruikersprofiel](media/amazon-business-tutorial/user-profile.png)

1. Selecteer **eenmalige aanmelding (SSO)** in de wizard **systeem integratie** .

    ![Eenmalige aanmelding](media/amazon-business-tutorial/sso-settings.png)

1. Selecteer in de wizard **SSO instellen** de provider volgens de vereisten van uw organisatie en klik op **volgende**.

    ![Standaardgroep](media/amazon-business-tutorial/default-group1.png)

1. Selecteer in de wizard **standaard gebruikers account** de **standaard groep** en selecteer vervolgens **standaard kopen** op basis van gebruikersrol in uw organisatie en klik op **volgende**.

    ![Standaardgroep](media/amazon-business-tutorial/dafault-group2.png)

1. Klik in de wizard **uw meta gegevensbestand uploaden** op **Bladeren** om het **XML-bestand met meta gegevens** te uploaden, dat u hebt gedownload van de Azure Portal en klik op **uploaden**.

    ![Verbindings gegevens](media/amazon-business-tutorial/connection-data1.png)

1. Na het uploaden van het bestand met de gedownloade meta gegevens, worden de velden in de sectie **verbindings gegevens** automatisch gevuld. Klik daarna op **volgende**.

    ![Verbindings gegevens](media/amazon-business-tutorial/connection-data2.png)

1. Klik in de wizard **uw kenmerk overzicht uploaden** op **overs Laan**.

    ![Kenmerken](media/amazon-business-tutorial/map-attribute1.png)

1. Voeg in de wizard **kenmerk toewijzing** de velden voor vereisten toe door te klikken op de optie **+ een veld toevoegen** . Voeg de kenmerk waarden toe, met inbegrip van de naam ruimte, die u hebt gekopieerd uit de sectie **gebruikers kenmerken & claims** van Azure Portal naar het veld met het **SAML-kenmerknaam** en klik op **volgende**.

    ![Kenmerken](media/amazon-business-tutorial/map-attribute2.png)

1. Klik op **volgende**in de wizard **Amazon-verbindings gegevens** .

    ![Verbinding](media/amazon-business-tutorial/amazon-connect.png)

1. Controleer de **status** van de stappen die zijn geconfigureerd en klik op **testen starten**.

    ![Verbinding](media/amazon-business-tutorial/sso-connection1.png)

1. Klik in de wizard **SSO-verbinding testen** op **testen**.

    ![Verbinding](media/amazon-business-tutorial/sso-connection2.png)

1. Kopieer de waarde die is toegewezen aan **idpid** en plak deze in de **idpid** -para meter in de **antwoord-URL** in het gedeelte met de basis- **SAML-configuratie** in de Azure Portal op de **gestarte URL** -wizard, voordat u op **activeren**klikt.

    ![Verbinding](media/amazon-business-tutorial/sso-connection3.png)

1. Op de wizard **bent u klaar om over te scha kelen naar de actieve SSO?** Raadpleeg **ik volledig geteste SSO en ben klaar om live aan te gaan** en klik op **overschakelen naar actief**.

    ![Verbinding](media/amazon-business-tutorial/sso-connection4.png)

1. Ten slotte in het gedeelte **SSO-verbindings gegevens** wordt de **status** weer gegeven als **actief**.

    ![Verbinding](media/amazon-business-tutorial/sso-connection5.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

> [!NOTE]
> Beheerders moet de test gebruikers zo nodig in hun Tenant maken. De volgende stappen laten zien hoe u een test gebruiker maakt.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Een Azure AD-beveiligings groep maken in de Azure Portal

1. Klik op **Azure Active Directory alle groepen >**.

    ![Een Azure AD-beveiligings groep maken](./media/amazon-business-tutorial/all-groups-tab.png)

1. Klik op **nieuwe groep**:

    ![Een Azure AD-beveiligings groep maken](./media/amazon-business-tutorial/new-group-tab.png)

1. **Groeps type**, **groeps naam**, **groeps beschrijving**, **type lidmaatschap**. Klik op de pijl om leden te selecteren, zoek vervolgens naar of klik op het lid dat u aan de groep wilt toevoegen. Klik op **selecteren** om de geselecteerde leden toe te voegen en klik vervolgens op **maken**.

    ![Een Azure AD-beveiligings groep maken](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Amazon Business.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Amazon Business**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

    >[!NOTE]
    > Als u de gebruikers niet toewijst in azure AD, wordt de volgende fout weer geven.

    ![De koppeling gebruiker toevoegen](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Wijs de Azure AD-beveiligings groep toe aan de Azure Portal

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Amazon Business**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ in de lijst toepassingen de optie **Amazon Business**.

    ![De koppeling Amazon Business in de lijst toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de **gebruiker toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Zoek naar de beveiligings groep die u wilt gebruiken en klik vervolgens op de groep om deze toe te voegen aan de sectie leden selecteren. Klik op **selecteren**en vervolgens op **toewijzen**.

    ![Beveiligings groep zoeken](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Controleer de meldingen in de menu balk om een melding te ontvangen dat de groep is toegewezen aan de bedrijfs toepassing in de Azure Portal.

### <a name="create-amazon-business-test-user"></a>Amazon Business test-gebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Amazon Business. Amazon Business ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Amazon Business, wordt er na verificatie een nieuwe gemaakt.

### <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Amazon Business in het toegangs venster klikt, moet u automatisch worden aangemeld bij het Amazon-bedrijf waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
