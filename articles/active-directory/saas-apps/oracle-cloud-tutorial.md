---
title: 'Zelfstudie: Azure Active Directory-integratie met Oracle Cloud Infrastructure Console | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Oracle Cloud Infrastructure Console.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: f045fe19-11f8-4ccf-a3eb-8495fdc8716f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64cae5812a380725d612d27190042797542ee255
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76289098"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Zelfstudie: Oracle Cloud Infrastructure Console integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Oracle Cloud Infrastructure Console integreren met Azure Active Directory (Azure AD). Wanneer u Oracle Cloud Infrastructure Console integreert met Azure AD, u het als:

* Beheer in Azure AD wie toegang heeft tot Oracle Cloud Infrastructure Console.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Oracle Cloud Infrastructure Console met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Oracle Cloud Infrastructure Console single sign-on (SSO) heeft een abonnement ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Oracle Cloud Infrastructure Console ondersteunt **SP** geïnitieerde SSO.
* Zodra u de Oracle Cloud Infrastructure Console hebt geconfigureerd, u sessiebesturingselementen afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebesturingselementen zijn van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Oracle Cloud Infrastructure Console toevoegen vanuit de galerij

Als u de integratie van Oracle Cloud Infrastructure Console in Azure AD wilt configureren, moet u Oracle Cloud Infrastructure Console vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Oracle Cloud Infrastructure Console** in het zoekvak in de sectie Toevoegen vanuit de **galerie.**
1. Selecteer **Oracle Cloud Infrastructure Console** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Oracle Cloud Infrastructure Console met behulp van een testgebruiker genaamd **B. Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Oracle Cloud Infrastructure Console.

Als u Azure AD SSO wilt configureren en testen met Oracle Cloud Infrastructure Console, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-eenmaligaanmelding met B. Simon te testen.
    1. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Oracle Cloud Infrastructure Console](#configure-oracle-cloud-infrastructure-console)** om de SSO-instellingen aan toepassingszijde te configureren.
    1. **[Maak oracle Cloud Infrastructure Console-testgebruiker](#create-oracle-cloud-infrastructure-console-test-user)** om een tegenhanger van B. Simon te hebben in Oracle Cloud Infrastructure Console die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina De toepassingsintegratie van **Oracle Cloud Infrastructure Console** de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **BasisSAML-configuratie** de waarden in voor de volgende velden:

   > [!NOTE]
   > U ontvangt het metagegevensbestand van de serviceprovider uit het gedeelte **Enkele aanmelding configureren** van oracle Cloud Infrastructure Console van de zelfstudie.
    
   1. Klik op **Metagegevensbestand uploaden**.

   1. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

   1. Zodra het metagegevensbestand is geüpload, worden de **URL-waarden Id** en **Antwoord** automatisch ingevuld in het tekstvak van de sectie **BasisSAML-configuratie.**
    
      > [!NOTE]
      > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

      Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [het ondersteuningsteam van Oracle Cloud Infrastructure Console Client](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Oracle Cloud Infrastructure Console-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

   ![installatiekopie](common/edit-attribute.png)

1. Naast bovenstaande, Oracle Cloud Infrastructure Console applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie. Voer in de sectie **Gebruikerskenmerken & claims** in het dialoogvenster **Groepsclaims (Preview)** de volgende stappen uit:

   1. Klik **op** de pen naast **de waarde van de naam-id**.

   1. Selecteer **Persistent** as **Choose name identifier format**.
 
   1. Klik op **Opslaan**.

      ![installatiekopie](./media/oracle-cloud-tutorial/config07.png)
    
      ![installatiekopie](./media/oracle-cloud-tutorial/config11.png)

   1. Klik op de **pen** naast **Groepen die zijn geretourneerd in claim**.

   1. Selecteer **Beveiligingsgroepen** in de radiolijst.

   1. Selecteer **Bronkenmerk** van **groeps-id**.

   1. Schakel **De naam van de groepsclaim aanpassen**in .

   1. Typ **groepNaam**in het tekstvak **Naam** .

   1. Typ in het tekstvak **Naamruimte (optioneel)** tekst `https://auth.oraclecloud.com/saml/claims`.

   1. Klik op **Opslaan**.

      ![installatiekopie](./media/oracle-cloud-tutorial/config08.png)

1. Kopieer in de sectie **Oracle Cloud Infrastructure Console** de juiste URL(s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)



### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B. Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B. Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B. Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B. Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Oracle Cloud Infrastructure Console.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer Oracle Cloud **Infrastructure Console**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

   ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **In** het dialoogvenster Gebruikers en groepen **B. Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-oracle-cloud-infrastructure-console"></a>Oracle Cloud Infrastructure Console configureren

1. Meld u in een ander browservenster aan bij Oracle Cloud Infrastructure Console als beheerder.

1. Klik aan de linkerkant van het menu en klik op **Identiteit** en navigeer vervolgens naar **Federatie.**

   ![Configuratie](./media/oracle-cloud-tutorial/config01.png)

1. Sla het **metagegevensbestand van de serviceprovider op** door op de koppeling Dit document **downloaden** te klikken en het te uploaden naar de sectie **BasisSAML-configuratie** van Azure-portal en klik vervolgens op **Identiteitsprovider toevoegen.**

   ![Configuratie](./media/oracle-cloud-tutorial/config02.png)

1. Voer in de pop-up **Identity Provider** toevoegen de volgende stappen uit:

   ![Configuratie](./media/oracle-cloud-tutorial/config03.png)

   1. Voer **in het** tekstvak NAAM uw naam in.

   1. Voer **in het tekstvak BESCHRIJVING** uw beschrijving in.

   1. Selecteer **Microsoft ACTIVE DIRECTORY FEDERATION SERVICE (ADFS) of SAML 2.0-compatibele identiteitsprovider** als **type**.

   1. Klik **op Bladeren** om de XML met federatiemetagegevens te uploaden, die u hebt gedownload van azure-portal.

   1. Klik **op Doorgaan** en voer in de sectie **Identiteitsprovider bewerken** de volgende stappen uit:

      ![Configuratie](./media/oracle-cloud-tutorial/config09.png)

   1. De **GROEP IDENTITEITSPROVIDER** moet worden geselecteerd als Aangepaste groep. De GROEPS-ID moet de GUID van de groep zijn uit Azure Active Directory. De groep moet in kaart worden gebracht met de bijbehorende groep in het veld **OCI GROUP.**

   1. U meerdere groepen toewijzen volgens uw installatie in azure-portal en uw organisatie. Klik op **+ Toewijzing toevoegen** om zoveel groepen toe te voegen als u nodig hebt.

   1. Klik **op Verzenden**.
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Testgebruiker oracle Cloud Infrastructure Console maken

 Oracle Cloud Infrastructure Console ondersteunt just-in-time provisioning, wat standaard is. Er is geen actie-item voor u in deze sectie. Een nieuwe gebruiker wordt niet gemaakt tijdens een poging om toegang te krijgen en ook geen noodzaak om de gebruiker te maken.

### <a name="test-sso"></a>Test SSO

Wanneer u de tegel Oracle Cloud Infrastructure Console selecteert in het toegangspaneel, wordt u doorgestuurd naar de aanmeldingspagina van de Oracle Cloud Infrastructure Console. Selecteer de **identiteitsprovider** in het vervolgkeuzemenu en klik op **Doorgaan** zoals hieronder wordt weergegeven om u aan te melden. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

![Configuratie](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Oracle Cloud Infrastructure Console beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
