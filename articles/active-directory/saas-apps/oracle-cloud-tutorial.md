---
title: 'Zelf studie: integratie Azure Active Directory met Oracle Cloud Infrastructure console | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Oracle Cloud Infrastructure console.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76289098"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Zelf studie: Oracle Cloud Infrastructure console integreren met Azure Active Directory

In deze zelf studie leert u hoe u de Oracle Cloud Infrastructure console integreert met Azure Active Directory (Azure AD). Wanneer u Oracle Cloud Infrastructure console integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de Oracle Cloud Infrastructure-console.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij de Oracle Cloud Infrastructure-console met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Oracle Cloud Infrastructure console-abonnement voor eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* De Oracle Cloud Infrastructure console ondersteunt door **SP** geïnitieerde SSO.
* Zodra u de Oracle Cloud Infrastructure-console hebt geconfigureerd, kunt u sessie besturings elementen afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie besturings elementen worden uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Een Oracle Cloud Infrastructure-console toevoegen vanuit de galerie

Als u de integratie van de Oracle Cloud Infrastructure-console wilt configureren in azure AD, moet u de Oracle Cloud Infrastructure console vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **Oracle Cloud Infrastructure console** in het zoekvak.
1. Selecteer **Oracle Cloud Infrastructure console** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Oracle Cloud Infrastructure console met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de Oracle Cloud Infrastructure-console.

Als u Azure AD SSO wilt configureren en testen met de Oracle Cloud Infrastructure-console, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. **[Configureer de Oracle Cloud Infrastructure console](#configure-oracle-cloud-infrastructure-console)** om de SSO-instellingen aan de kant van de toepassing te configureren.
    1. Een **[test gebruiker van de Oracle-Cloud infrastructuur console maken](#create-oracle-cloud-infrastructure-console-test-user)** die een soort is van B. Simon in de Oracle Cloud Infrastructure-console die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina Toepassings integratie van de **Oracle Cloud Infrastructure-console** en selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **basis configuratie van SAML** de waarden in voor de volgende velden:

   > [!NOTE]
   > U krijgt het bestand met meta gegevens van de service provider via de sectie **Oracle Cloud Infrastructure console eenmalige aanmelding configureren** van de zelf studie.
    
   1. Klik op **Metagegevensbestand uploaden**.

   1. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

   1. Zodra het meta gegevensbestand is geüpload, worden de waarden voor de **id** en de **antwoord-URL** automatisch ingevuld in het tekstvak voor de **basis configuratie van SAML** .
    
      > [!NOTE]
      > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

      Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met de [Oracle Cloud Infrastructure console client ondersteuning](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

1. De toepassing Oracle Cloud Infrastructure console verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

   ![installatiekopie](common/edit-attribute.png)

1. Daarnaast verwacht een Oracle Cloud Infrastructure console-toepassing nog enkele kenmerken die in de SAML-respons kunnen worden door gegeven. Voer de volgende stappen uit in de sectie **gebruikers kenmerken & claims** van het dialoog venster **groeps claims (preview)** :

   1. Klik op de **pen** naast **naam identificatie waarde**.

   1. Selecteer **permanent** als **naam-id-indeling kiezen**.
 
   1. Klik op **Opslaan**.

      ![installatiekopie](./media/oracle-cloud-tutorial/config07.png)
    
      ![installatiekopie](./media/oracle-cloud-tutorial/config11.png)

   1. Klik op de **pen** naast **groepen die zijn geretourneerd in claim**.

   1. Selecteer **beveiligings groepen** in de lijst met keuze zenders.

   1. Selecteer **bron kenmerk** van **groep-ID**.

   1. Controleer **de naam van de groepclaim aanpassen**.

   1. Typ **GroupName**in het tekstvak **naam** .

   1. Typ `https://auth.oraclecloud.com/saml/claims`in het tekstvak **naam ruimte (optioneel)** .

   1. Klik op **Opslaan**.

      ![installatiekopie](./media/oracle-cloud-tutorial/config08.png)

1. Op de sectie **Oracle Cloud Infrastructure console instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)



### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B. Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B. Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot de Oracle Cloud Infrastructure-console.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Oracle Cloud Infrastructure console**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

   ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-oracle-cloud-infrastructure-console"></a>De Oracle Cloud Infrastructure-console configureren

1. Meld u in een ander browser venster aan bij Oracle Cloud Infrastructure console als beheerder.

1. Klik aan de linkerkant van het menu en klik op **identiteit** en vervolgens naar **Federatie**.

   ![Configuratie](./media/oracle-cloud-tutorial/config01.png)

1. Sla het **META gegevensbestand van de service provider** op door te klikken op de koppeling **dit document downloaden** en het bestand te uploaden naar het gedeelte **basis configuratie van SAML** van Azure Portal en klik vervolgens op **ID-provider toevoegen**.

   ![Configuratie](./media/oracle-cloud-tutorial/config02.png)

1. Voer de volgende stappen uit in het pop-upvenster **ID-provider toevoegen** :

   ![Configuratie](./media/oracle-cloud-tutorial/config03.png)

   1. Voer uw naam in het tekstvak **naam** in.

   1. Voer in het tekstvak **Beschrijving** uw beschrijving in.

   1. Selecteer **micro soft Active Directory Federation service (ADFS) of ID-provider SAML 2,0-compatibel** als **type**.

   1. Klik op **Bladeren** om de XML met federatieve meta gegevens te uploaden, die u hebt gedownload van Azure Portal.

   1. Klik op **door gaan** en voer de volgende stappen uit in de sectie **ID-provider bewerken** :

      ![Configuratie](./media/oracle-cloud-tutorial/config09.png)

   1. De **ID-provider groep** moet worden geselecteerd als aangepaste groep. De GROEPs-ID moet de GUID van de groep van Azure Active Directory zijn. De groep moet worden toegewezen aan de bijbehorende groep in het veld **OCI-groep** .

   1. U kunt meerdere groepen toewijzen conform uw installatie in Azure Portal en uw organisatie. Klik op **+ toewijzing toevoegen** om het gewenste aantal groepen toe te voegen.

   1. Klik op **verzenden**.
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Test gebruiker voor de Oracle-Cloud infrastructuur console maken

 De Oracle Cloud Infrastructure-console biedt ondersteuning voor Just-in-time-inrichting. Dit is standaard. Er is geen actie-item voor u in deze sectie. Een nieuwe gebruiker wordt niet gemaakt tijdens een poging om toegang te krijgen en u hoeft de gebruiker ook niet te maken.

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel Oracle Cloud Infrastructure console in het toegangs venster selecteert, wordt u omgeleid naar de aanmeldings pagina van de Oracle-Cloud infrastructuur console. Selecteer de **ID-provider** in de vervolg keuzelijst en klik op **door gaan** , zoals hieronder wordt weer gegeven om u aan te melden. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

![Configuratie](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Oracle Cloud Infrastructure console beveiligen met geavanceerde zicht baarheid en besturings elementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
