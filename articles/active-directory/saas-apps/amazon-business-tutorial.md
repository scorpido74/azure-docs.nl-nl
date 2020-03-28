---
title: 'Zelfstudie: Azure Active Directory-integratie met Amazon Business | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68496908"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Zelfstudie: Amazon Business integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Amazon Business integreren met Azure Active Directory (Azure AD). Wanneer u [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) integreert met Azure AD, u het:

* Beheer in Azure AD die toegang heeft tot Amazon Business.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Amazon Business met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Als je geen abonnement hebt, kun je [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proefperiode van een maand krijgen.
* Een Amazon Business-abonnement (SSO) ingeschakeld. Ga naar de [pagina Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) om een Amazon Business-account aan te maken.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een bestaand Amazon Business-account.

* Amazon Business ondersteunt **SP en IDP** gestart met SSO
* Amazon Business ondersteunt **Just In Time** gebruikersinrichting

## <a name="adding-amazon-business-from-the-gallery"></a>Amazon Business toevoegen vanuit de galerij

Als u de integratie van Amazon Business in Azure AD wilt configureren, moet u Amazon Business vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Amazon Business** in het zoekvak in de sectie Toevoegen in **de galerie.**
1. Selecteer **Amazon Business** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Amazon Business met behulp van een testgebruiker genaamd **B.Simon**.

Voer de volgende bouwstappen uit om Azure AD SSO met Amazon Business te configureren en te testen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Amazon Business SSO](#configure-amazon-business-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Maak Amazon Business-testgebruiker](#create-amazon-business-test-user)** - om een tegenhanger van B.Simon in Amazon Business te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Amazon Business-toepassingsintegratie** de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u wilt configureren in de **idp-modus:**

    1. Typ in het tekstvak **Id (Entity ID)** een URL met een van de volgende patronen:
    
       | | |
       |-|-|
       | `https://www.amazon.com`|
       | `https://www.amazon.co.jp`|
       | `https://www.amazon.de`|

    1. Typ in het tekstvak **URL beantwoorden** een URL met een van de volgende patronen:
    
       | | |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`|

       > [!NOTE]
       > De waarde van de antwoord-URL is niet de echte waarde. Werk deze waarde bij met de werkelijke antwoord-URL. U krijgt `<idpid>` de waarde van de Amazon Business SSO-configuratiesectie, die later in de zelfstudie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://www.amazon.com/`

1. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Bewerk de kenmerken door te klikken op het pictogram **Bewerken** in de sectie **Gebruikerskenmerken & claims.**

    ![Kenmerken](media/amazon-business-tutorial/map-attribute3.png)

1. Kenmerken bewerken en **de waarde van de naamruimte** van deze kenmerken naar het notitieblok kopiëren.

    ![Kenmerken](media/amazon-business-tutorial/map-attribute4.png)

1. Naast bovenstaande, Amazon Business applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie. Voer in de sectie **Gebruikerskenmerken & claims** in het dialoogvenster **Groepsclaims** de volgende stappen uit:

    a. Klik op de **pen** naast **Groepen die zijn geretourneerd in claim**.

    ![installatiekopie](./media/amazon-business-tutorial/config04.png)

    ![installatiekopie](./media/amazon-business-tutorial/config05.png)

    b. Selecteer **Alle groepen** in de radiolijst.

    c. Selecteer **Groeps-id** als **kenmerk Bron**.

    d. Schakel **Het selectievakje De naam van het groepsclaimselectievakje aanpassen** en voer de groepsnaam in op basis van uw organisatievereiste.

    e. Klik op **Opslaan**.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Metagegevens XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Amazon Business instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Amazon Business SSO configureren

1. Meld u in een ander browservenster aan bij uw Amazon Business-bedrijfssite als beheerder.

1. Klik op het **gebruikersprofiel** en selecteer **Bedrijfsinstellingen**.

    ![Gebruikersprofiel](media/amazon-business-tutorial/user-profile.png)

1. Selecteer in de wizard **Systeemintegraties** de optie **Eén aanmelding (SSO)**.

    ![Eenmalige aanmelding](media/amazon-business-tutorial/sso-settings.png)

1. Selecteer in de wizard **SSO instellen** de provider op basis van uw organisatievereisten en klik op **Volgende**.

    ![Standaardgroep](media/amazon-business-tutorial/default-group1.png)

1. Selecteer in de **standaardwizard Nieuw gebruikersaccount** de **standaardgroep** en selecteer **standaardkooprol** op basis van de gebruikersrol in uw organisatie en klik op **Volgende**.

    ![Standaardgroep](media/amazon-business-tutorial/dafault-group2.png)

1. Klik in de wizard **Uw metagegevensbestand uploaden** op **Bladeren** om het **XML-bestand met metagegevens te uploaden,** dat u hebt gedownload van de Azure-portal en klik op **Uploaden.**

    ![Verbindingsgegevens](media/amazon-business-tutorial/connection-data1.png)

1. Nadat het gedownloade metagegevensbestand is geüpload, worden de velden in de sectie **Verbindingsgegevens** automatisch ingevuld. Klik daarna op **Volgende**.

    ![Verbindingsgegevens](media/amazon-business-tutorial/connection-data2.png)

1. Klik in de wizard **Uw kenmerkverklaring uploaden** op **Overslaan**.

    ![Kenmerken](media/amazon-business-tutorial/map-attribute1.png)

1. Voeg in de wizard **Toewijzing van kenmerk** de vereistevelden toe door op de optie + Een veld toevoegen **te** klikken. Voeg de kenmerkwaarden inclusief de naamruimte toe, die u hebt gekopieerd uit de sectie **Gebruikerskenmerken & claims** van azure-portal in het veld **SAML-attribuutnaam** en klik op **Volgende**.

    ![Kenmerken](media/amazon-business-tutorial/map-attribute2.png)

1. Klik in de wizard **Amazon-verbindingsgegevens** op **Volgende**.

    ![Verbinding](media/amazon-business-tutorial/amazon-connect.png)

1. Controleer de **status** van de stappen die zijn geconfigureerd en klik op **Starttesten**.

    ![Verbinding](media/amazon-business-tutorial/sso-connection1.png)

1. Klik in de wizard **SSO-verbinding** testen op **Testen**.

    ![Verbinding](media/amazon-business-tutorial/sso-connection2.png)

1. Kopieer in de wizard **IDP geïnitieerde URL** voordat u op **Activeren**klikt , kopieer de waarde die is toegewezen aan **idpid** en plak in de **idpid-parameter** in de **URL van antwoord** in de sectie **BasisSAML-configuratie** in de Azure-portal.

    ![Verbinding](media/amazon-business-tutorial/sso-connection3.png)

1. Op de **Bent u klaar om over te schakelen naar actieve SSO?** wizard, check **Ik heb volledig getest SSO en ben klaar om live te gaan** checkbox en klik op Switch naar **actief**.

    ![Verbinding](media/amazon-business-tutorial/sso-connection4.png)

1. Ten slotte wordt in de sectie **SSO Connection details** de **status** weergegeven als **Actief**.

    ![Verbinding](media/amazon-business-tutorial/sso-connection5.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

> [!NOTE]
> Beheerders moeten indien nodig de testgebruikers in hun tenant maken. De volgende stappen laten zien hoe u een testgebruiker maakt.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Een Azure AD-beveiligingsgroep maken in de Azure-portal

1. Klik op **Azure Active Directory > Alle groepen**.

    ![Een Azure AD-beveiligingsgroep maken](./media/amazon-business-tutorial/all-groups-tab.png)

1. Klik op **Nieuwe groep:**

    ![Een Azure AD-beveiligingsgroep maken](./media/amazon-business-tutorial/new-group-tab.png)

1. Groepstype **Group type**invullen , **groepsnaam**, **groepsomschrijving**, **lidmaatschapstype**. Klik op de pijl om leden te selecteren en zoek naar of klik op het lid dat u aan de groep wilt toevoegen. Klik op **Selecteren** om de geselecteerde leden toe te voegen en klik vervolgens op **Maken**.

    ![Een Azure AD-beveiligingsgroep maken](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Amazon Business.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Amazon Business**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

    >[!NOTE]
    > Als u de gebruikers in het Azure AD niet toewijst, krijgt u de volgende fout.

    ![De koppeling Gebruiker toevoegen](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>De Azure AD-beveiligingsgroep toewijzen in de Azure-portal

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer Vervolgens **Amazon Business**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer Amazon **Business**in de lijst met toepassingen .

    ![De Koppeling Amazon Business in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik **op**de gebruiker toevoegen .

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Zoek naar de beveiligingsgroep die u wilt gebruiken en klik vervolgens op de groep om deze toe te voegen aan de sectie Leden selecteren. Klik **op Selecteren**en klik vervolgens op **Toewijzen**.

    ![Beveiligingsgroep zoeken](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Controleer de meldingen in de menubalk om te worden gemeld dat de groep is toegewezen aan de Enterprise-toepassing in de Azure-portal.

### <a name="create-amazon-business-test-user"></a>Amazon Business-testgebruiker maken

In deze sectie wordt een gebruiker genaamd B.Simon gemaakt in Amazon Business. Amazon Business ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Amazon Business, wordt er een nieuwe gemaakt na verificatie.

### <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Amazon Business in het toegangspaneel klikt, moet u automatisch worden aangemeld bij het Amazon Business waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
