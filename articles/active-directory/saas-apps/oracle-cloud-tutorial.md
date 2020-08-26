---
title: 'Zelfstudie: Azure Active Directory-integratie met de Oracle Cloud Infrastructure-console | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Oracle Cloud Infrastructure-console.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: d4a478c402346e33a4f27ff2a363de75285b435b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543836"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Zelfstudie: Oracle Cloud Infrastructure-console integreren met Azure Active Directory

In deze zelfstudie leert u hoe u de Oracle Cloud Infrastructure-console kunt integreren met Azure Active Directory (Azure AD). Als u de Oracle Cloud Infrastructure-console integreert met Azure Active Directory, kunt u:

* In Azure AD beheren wie toegang heeft tot Oracle Cloud Infrastructure-console.
* Uw gebruikers zich automatisch laten aanmelden bij de Oracle Cloud Infrastructure-console met hun Azure AD-account.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op de Oracle Cloud Infrastructure-console waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* De Oracle Cloud Infrastructure-console ondersteunt door **SP** geïnitieerde eenmalige aanmelding.
* Zodra u de Oracle Cloud Infrastructure-console hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Een Oracle Cloud Infrastructure-console toevoegen vanuit de galerie

Voor het configureren van de integratie van de Oracle Cloud Infrastructure-console in Azure AD moet u de Oracle Cloud Infrastructure-console uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Oracle Cloud Infrastructure-console** in het zoekvak.
1. Selecteer **Oracle Cloud Infrastructure-console** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD bij de Oracle Cloud Infrastructure-console met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Oracle Cloud Infrastructure-console.

Als u eenmalige aanmelding van Azure AD bij de Oracle Cloud Infrastructure-console wilt configureren en testen, moet u de volgende stappen voltooien:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Oracle Cloud Infrastructure-console configureren](#configure-oracle-cloud-infrastructure-console)** om de instelling voor eenmalige aanmelding aan de kant van de app te configureren.
    1. **[Een testgebruiker maken in de Oracle Cloud Infrastructure-console](#create-oracle-cloud-infrastructure-console-test-user)** : voor een equivalent van B.Simon in de Oracle Cloud Infrastructure-console dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Oracle Cloud Infrastructure-console** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

   > [!NOTE]
   > U krijgt het metagegevensbestand van de service provider via de sectie **Eenmalige aanmelding van de Oracle Cloud Infrastructure-console configureren** van de zelfstudie.
    
   1. Klik op **Metagegevensbestand uploaden**.

   1. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

   1. Zodra het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **Antwoord-URL** automatisch ingevuld in het tekstvak van de sectie **Standaard SAML-configuratie**.
    
      > [!NOTE]
      > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

      In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [het Client-ondersteuningsteam van Oracle Cloud Infrastructure-console](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden en vervolgens op te slaan op de computer.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

1. De Oracle Cloud Infrastructure-console-toepassing verwacht SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

   ![image](common/edit-attribute.png)

1. Bovendien verwacht de Oracle Cloud Infrastructure-console-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Voer in het gedeelte **Gebruikerskenmerken en -claims** in het dialoogvenster **Groepsclaims (preview)** de volgende stappen uit:

   1. Klik op de **pen** naast de **Waarde voor naam-id**.

   1. Selecteer **Permanent** als  **Naam-id-indeling kiezen**.
 
   1. Klik op **Opslaan**.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Klik op de **pen** naast **Groepen die zijn geretourneerd in claim**.

   1. Selecteer **Beveiligingsgroepen** in de lijst met keuzerondjes.

   1. Selecteer **Bronkenmerk** bij **Groep-ID**.

   1. Selecteer **De naam van de groepsclaim aanpassen**.

   1. Typ **groupName** in het tekstvak **Naam**.

   1. Typ in het tekstvak **Naamruimte (optioneel)** : `https://auth.oraclecloud.com/saml/claims`.

   1. Klik op **Opslaan**.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. In de sectie **Oracle Cloud Infrastructure-console instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)



### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B. Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B. Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B. Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u B.Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot de Oracle Cloud Infrastructure-console.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Oracle Cloud Infrastructure-console**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

   ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B. Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-oracle-cloud-infrastructure-console"></a>Oracle Cloud Infrastructure-console configureren

1. Meld u in een ander venster van de browser als beheerder aan bij Oracle Cloud Infrastructure-console.

1. Klik aan de linkerkant van het menu en klik op **Identiteit** en ga vervolgens naar **Federatie**.

   ![Configuratie](./media/oracle-cloud-tutorial/config01.png)

1. Sla het **metagegevensbestand van de service provider op** door te klikken op de link **Dit document downloaden** en upload het naar het gedeelte **Basisconfiguratie van SAML** van Azure Portal en klik vervolgens op **Id-provider toevoegen**.

   ![Configuratie](./media/oracle-cloud-tutorial/config02.png)

1. Voer de volgende stappen uit in het pop-upvenster **Nieuwe id-provider**:

   ![Configuratie](./media/oracle-cloud-tutorial/config03.png)

   1. Voer in het tekstvak **NAAM** uw naam in.

   1. Voer in het tekstvak **BESCHRIJVING** uw beschrijving in.

   1. Select **MICROSOFT ACTIVE DIRECTORY FEDERATION SERVICE (ADFS) OF SAML 2.0 COMPLIANT IDENTITY PROVIDER** als **TYPE**

   1. Klik op **Bladeren** om het XML-bestand met federatieve metagegevens, dat u hebt gedownload van Azure Portal te uploaden.

   1. Klik op **Doorgaan** en voer in de sectie **Id-provider bewerken** de volgende stappen uit:

      ![Configuratie](./media/oracle-cloud-tutorial/config09.png)

   1. De **IDENTITY PROVIDER GROUP** moet worden geselecteerd als Aangepaste groep. De GROEP-ID moet de GUID van de groep uit Azure Active Directory zijn. De groep moet worden toegewezen aan de overeenkomstige groep in het veld **OCI-GROEP**.

   1. U kunt meerdere groepen toewijzen conform uw installatie in Azure Portal en de behoeften van uw organisatie. Klik op **+ Toewijzen toevoegen** om zoveel groepen toe te voegen als u wilt.

   1. Klik op **Submit**
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Een testgebruiker voor Oracle Cloud Infrastructure-console maken

 Oracle Cloud Infrastructure-console biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Dit is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Er wordt geen nieuwe gebruiker gemaakt tijdens een poging om toegang te krijgen en u hoeft de gebruiker ook niet te maken.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u de tegel van Oracle Cloud Infrastructure-console in het toegangsvenster selecteert, wordt u omgeleid naar de aanmeldingspagina van de Oracle Cloud Infrastructure-console. Selecteer de **IDENTITY PROVIDER** in de vervolgkeuzelijst en klik op **Doorgaan** zoals hieronder wordt weergegeven om u aan te melden. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

![Configuratie](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [ Oracle Cloud Infrastructure-console beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
