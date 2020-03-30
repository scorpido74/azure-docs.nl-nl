---
title: 'Zelfstudie: Succesfactoren binnenkomende inrichting configureren in Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van inbound provisioning vanuit SuccessFactors
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: 1ff90231-1312-463e-8949-7d976e433fc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2019
ms.author: chmutali
ms.openlocfilehash: d9317a68c8967fbe0728e8c47e59dd33367c6163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249683"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>Zelfstudie: SAP-succesfactoren configureren voor active directory-gebruikersinrichting (voorbeeld)
Het doel van deze zelfstudie is om de stappen te laten zien die u moet uitvoeren om gebruikers van SuccessFactors Employee Central in Active Directory (AD) en Azure AD te voorzien, met optionele terugschrijfgegevens van e-mailadres naar SuccessFactors. Deze integratie is in openbare preview en ondersteunt het ophalen van meer dan [70 + gebruikerskenmerken](../app-provisioning/sap-successfactors-attribute-reference.md) van SuccessFactors Employee Central.

>[!NOTE]
>Gebruik deze zelfstudie als de gebruikers die u wilt inrichten van SuccessFactors een on-premises AD-account en optioneel een Azure AD-account nodig hebben. Als de gebruikers van SuccessFactors alleen Azure AD-account (alleen cloudgebruikers) nodig hebben, raadpleegt u de zelfstudie over [het configureren van SAP SuccessFactors naar Azure AD-gebruikersinrichting.](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 


## <a name="overview"></a>Overzicht

De [Azure Active Directory-service voor het inrichten van gebruikers](../app-provisioning/user-provisioning.md) integreert met de Service [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) om de levenscyclus van gebruikers te beheren. 

De gebruikersinrichtingsworkflows van SuccesFactors die worden ondersteund door de Azure AD-service voor het inrichten van gebruikers, maken automatisering mogelijk van de volgende scenario's voor het beheer van human resources en identiteitslevenscyclus:

* **Nieuwe werknemers inhuren** - Wanneer een nieuwe werknemer wordt toegevoegd aan SuccessFactors, wordt automatisch een gebruikersaccount gemaakt in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD,](../app-provisioning/user-provisioning.md)met terugschrijfgegevens van het e-mailadres naar SuccessFactors.

* **Werknemerskenmerk en profielupdates** : Wanneer een werknemersrecord wordt bijgewerkt in SuccessFactors (zoals hun naam, titel of manager), wordt hun gebruikersaccount automatisch bijgewerkt in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD.](../app-provisioning/user-provisioning.md)

* **Werknemersbeëindigingen** - Wanneer een werknemer wordt beëindigd in SuccessFactors, wordt zijn gebruikersaccount automatisch uitgeschakeld in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD.](../app-provisioning/user-provisioning.md)

* **Werknemer rehires** - Wanneer een werknemer opnieuw wordt aangenomen in SuccessFactors, kan hun oude account automatisch opnieuw worden geactiveerd of opnieuw worden ingericht (afhankelijk van uw voorkeur) naar Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD.](../app-provisioning/user-provisioning.md)

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Voor wie is deze gebruikersinrichtingsoplossing het meest geschikt?

Deze Oplossing voor het inrichten van Active Directory-gebruikers is bij uitstek geschikt voor:

* Organisaties die een vooraf gebouwde, cloudgebaseerde oplossing willen voor de gebruikersinrichting van SuccessFactors

* Organisaties die directe gebruikersinrichting van SuccessFactors naar Active Directory vereisen

* Organisaties die vereisen dat gebruikers worden ingericht met behulp van gegevens die zijn verkregen uit de [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organisaties die gebruikers moeten samenvoegen, verplaatsen en laten synchroniseren met een of meer Active Directory-forests, Domeinen en -gegevens op basis van wijzigingsgegevens die zijn gedetecteerd in [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organisaties die Office 365 gebruiken voor e-mail

## <a name="solution-architecture"></a>Oplossingsarchitectuur

In deze sectie wordt de end-to-end-inrichtingsoplossingsarchitectuur voor veelvoorkomende hybride omgevingen beschreven. Er zijn twee gerelateerde stromen:

* **Gezaghebbende HR-gegevensstroom – van SuccessFactors tot on-premises Active Directory:** In deze stroomwerkgebeurtenissen (zoals Nieuwe aanwervingen, Overdrachten, Beëindigingen) vinden eerst plaats in de cloud SuccessFactors Employee Central en vervolgens stromen de gebeurtenisgegevens via Azure AD en de inrichtingsagent in on-premises Active Directory. Afhankelijk van de gebeurtenis kan dit leiden tot het maken/bijwerken/inschakelen/uitschakelen van bewerkingen in AD.
* **E-mail Writeback Flow – van on-premises Active Directory naar SuccessFactors:** Zodra het aanmaken van een account is voltooid in Active Directory, wordt het gesynchroniseerd met Azure AD via Azure AD Connect-synchronisatie en kan het e-mailkenmerk worden teruggeschreven naar SuccessFactors.

  ![Overzicht](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Gegevensstroom van eind-tot-eindgebruiker

1. Het HR-team voert werknemerstransacties uit (Joiners/Verhuizers/Leavers of New Hires/Transfers/Terminations) in SuccessFactors Employee Central
2. De Azure AD Provisioning Service voert geplande synchronisaties van identiteiten van SuccessFactors EC uit en identificeert wijzigingen die moeten worden verwerkt voor synchronisatie met on-premises Active Directory.
3. De Azure AD Provisioning Service roept de on-premises Azure AD Connect Provisioning Agent op met een request payload met AD-account maken/bijwerken/inschakelen/uitschakelen bewerkingen.
4. De Azure AD Connect Provisioning Agent gebruikt een serviceaccount om AD-accountgegevens toe te voegen/bijwerken.
5. De Azure AD Connect Sync-engine voert deltasynchronisatie uit om updates in AD uit te voeren.
6. De Active Directory-updates worden gesynchroniseerd met Azure Active Directory.
7. Als de [App SuccessFactors Writeback](sap-successfactors-writeback-tutorial.md) is geconfigureerd, schrijft deze het e-mailkenmerk terug naar SuccessFactors, op basis van het gebruikte overeenkomende kenmerk.

## <a name="planning-your-deployment"></a>Uw implementatie plannen

Het configureren van cloud HR-gestuurde gebruikersinrichting van SuccessFactors naar AD vereist een aanzienlijke planning voor verschillende aspecten zoals:
* Installatie van de Azure AD Connect-inrichtingsagent 
* Aantal succesfactoren voor het inrichten van apps voor AD-gebruikers om te implementeren
* Matching ID, Attribuuttoewijzing, transformatie en scopingfilters

Raadpleeg het [cloud HR-implementatieplan](../app-provisioning/plan-cloud-hr-provision.md) voor uitgebreide richtlijnen rond deze onderwerpen. 

## <a name="configuring-successfactors-for-the-integration"></a>SuccessFactors configureren voor de integratie

Een veelvoorkomende vereiste van alle SuccessFactors-inrichtingsconnectoren is dat ze referenties van een SuccessFactors-account met de juiste machtigingen nodig hebben om de OData-API's van SuccessFactors aan te roepen. In deze sectie worden stappen beschreven om het serviceaccount in SuccessFactors te maken en de juiste machtigingen toe te kennen. 

* [API-gebruikersaccount maken/identificeren in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Een rol api-machtigingen maken](#create-an-api-permissions-role)
* [Een machtigingsgroep maken voor de API-gebruiker](#create-a-permission-group-for-the-api-user)
* [Machtigingsrol verlenen aan de machtigingsgroep](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>API-gebruikersaccount maken/identificeren in SuccessFactors
Werk samen met uw SuccessFactors-beheerteam of implementatiepartner om een gebruikersaccount te maken of te identificeren in SuccessFactors dat wordt gebruikt om de OData-API's aan te roepen. De gebruikersnaam en wachtwoordreferenties van dit account zijn vereist bij het configureren van de inrichtingsapps in Azure AD. 

### <a name="create-an-api-permissions-role"></a>Een rol api-machtigingen maken

* Meld u aan bij SAP SuccessFactors met een gebruikersaccount dat toegang heeft tot het Beheercentrum.
* Zoek *naar Machtigingenrollen beheren*en selecteer **vervolgens Machtigingenrollen beheren** in de zoekresultaten.
  ![Machtigingsrollen beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Klik in de lijst met machtigingsrollen op **Nieuw maken**.
  > [!div class="mx-imgBorder"]
  > ![Nieuwe machtigingsrol maken](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Voeg een **rolnaam** en **-beschrijving toe** voor de nieuwe machtigingsrol. De naam en beschrijving moeten aangeven dat de rol voor API-gebruiksmachtigingen is.
  > [!div class="mx-imgBorder"]
  > ![Machtigingsroldetail](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Klik onder Machtigingsinstellingen op **Machtiging...** en scrol vervolgens naar beneden in de machtigingslijst en klik op **Hulpmiddelen voor integratie beheren**. Schakel het selectievakje **Beheerder toegang geven tot De OData-API in via basisverificatie**.
  > [!div class="mx-imgBorder"]
  > ![Integratietools beheren](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Schuif omlaag in hetzelfde vak en selecteer **Employee Central API**. Voeg machtigingen toe zoals hieronder wordt weergegeven om te lezen met behulp van de ODATA API en bewerk met ODATA API. Selecteer de bewerkingsoptie als u van plan bent hetzelfde account te gebruiken voor het scenario Writeback to SuccessFactors. 
  > [!div class="mx-imgBorder"]
  > ![Schrijfmachtigingen lezen](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

  >[!NOTE]
  >Raadpleeg naslaginformatie over [de](../app-provisioning/sap-successfactors-attribute-reference.md) volledige lijst met kenmerken die door deze inrichtingsapp zijn opgehaald

* Klik op **Gereed**. Klik **op Wijzigingen opslaan**.

### <a name="create-a-permission-group-for-the-api-user"></a>Een machtigingsgroep maken voor de API-gebruiker

* Zoek in het SuccesFactors-beheercentrum naar *Machtigingsgroepen beheren*en selecteer **vervolgens Machtigingsgroepen beheren** in de zoekresultaten.
  > [!div class="mx-imgBorder"]
  > ![Machtigingsgroepen beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Klik in het venster Machtigingsgroepen beheren op **Nieuw maken**.
  > [!div class="mx-imgBorder"]
  > ![Nieuwe groep toevoegen](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Voeg een groepsnaam toe voor de nieuwe groep. De groepsnaam moet aangeven dat de groep voor API-gebruikers is.
  > [!div class="mx-imgBorder"]
  > ![Naam van de machtigingsgroep](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Leden toevoegen aan de groep. U bijvoorbeeld **gebruikersnaam** selecteren in het vervolgkeuzemenu Groep Personen en vervolgens de gebruikersnaam invoeren van het API-account dat voor de integratie wordt gebruikt. 
  > [!div class="mx-imgBorder"]
  > ![Groepsleden toevoegen](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Klik **op Gereed** om de machtigingsgroep te maken.

### <a name="grant-permission-role-to-the-permission-group"></a>Machtigingsrol verlenen aan de machtigingsgroep

* Zoek in het SuccesFactors-beheercentrum naar *Machtigingenrollen beheren*en selecteer **vervolgens Machtigingsrollen beheren** in de zoekresultaten.
* Selecteer in de **lijst met machtigingsrollen**de rol die u hebt gemaakt voor API-gebruiksmachtigingen.
* Klik **onder Deze rol verlenen aan...**, klik op Knop **Toevoegen...**
* Selecteer **Machtigingsgroep...** klik in het vervolgkeuzemenu op **Selecteren...** om het venster Groepen te openen om te zoeken en selecteer de groep die hierboven is gemaakt. 
  > [!div class="mx-imgBorder"]
  > ![Machtigingsgroep toevoegen](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Controleer de machtigingsrolverlening aan de machtigingsgroep. 
  > [!div class="mx-imgBorder"]
  > ![Machtigingsrol en groepsgegevens](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Klik **op Wijzigingen opslaan**.

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Gebruikersvoorziening configureren van SuccessFactors naar Active Directory

In deze sectie vindt u stappen voor het inrichten van gebruikersaccounts van SuccessFactors naar elk Active Directory-domein binnen het bereik van uw integratie.

* [De inrichtingsconnector-app toevoegen en de inrichtingsagent downloaden](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [On-premises inrichtingsagent(s) installeren en configureren](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Connectiviteit configureren met SuccessFactors en Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Kenmerkentoewijzingen configureren](#part-4-configure-attribute-mappings)
* [Gebruikersinrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Deel 1: De inrichtingsconnector-app toevoegen en de inrichtingsagent downloaden

**Ga als eerste voor een configureren op Active Directory-inrichting:**

1. Ga naar <https://portal.azure.com>

2. Selecteer **Azure Active Directory** op de linkernavigatiebalk

3. Selecteer **Enterprise-toepassingen**en **vervolgens Alle toepassingen**.

4. Selecteer **Een toepassing toevoegen**en selecteer de categorie **Alles.**

5. Zoek **naar SuccessFactors naar Active Directory User Provisioning**en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met app-details wordt weergegeven, selecteert u **Provisioning**

7. De **inrichtingsmodus** **Mode** wijzigen in **Automatisch**

8. Klik op de informatiebanner die wordt weergegeven om de provisioning agent te downloaden. 
   > [!div class="mx-imgBorder"]
   > ![Agent downloaden](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Scherm agent downloaden")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Deel 2: On-premises provisioning agent(s) installeren en configureren

Als u Active Directory on-premises wilt inrichten, moet de inrichtingsagent zijn geïnstalleerd op een server met .NET 4.7.1+ Framework en netwerktoegang tot het gewenste Active Directory-domein(en).

> [!TIP]
> U de versie van het .NET-framework op uw server controleren met behulp van de [instructies die hier](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)worden gegeven.
> Als de server geen .NET 4.7.1 of hoger heeft geïnstalleerd, u deze [vanaf hier](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)downloaden.  

Breng het gedownloade agent-installatieprogramma over naar de serverhost en volg de onderstaande stappen om de agentconfiguratie te voltooien.

1. Meld u aan bij de Windows Server waar u de nieuwe agent wilt installeren.

1. Start het installatiebedrijf, ga akkoord met de voorwaarden en klik op de knop **Installeren.**

   ![Scherm installeren](./media/workday-inbound-tutorial/pa_install_screen_1.png "Scherm installeren")
   
1. Nadat de installatie is voltooid, wordt de wizard gestart en ziet u het **SCHERM Azure AD verbinden.** Klik op de knop **Verifiëren** om verbinding te maken met uw Azure AD-exemplaar.

   ![Verbinding maken met Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Verbinding maken met Azure AD")
   
1. Verifieer naar uw Azure AD-exemplaar met globale beheerdersreferenties.

   ![Beheerder Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Beheerder Auth")

   > [!NOTE]
   > De Azure AD-beheerdersreferenties worden alleen gebruikt om verbinding te maken met uw Azure AD-tenant. De agent slaat de referenties niet lokaal op de server op.

1. Na succesvolle verificatie met Azure AD ziet u het **Active Directory-scherm verbinden.** Voer in deze stap uw AD-domeinnaam in en klik op de knop **Map toevoegen.**

   ![Map toevoegen](./media/workday-inbound-tutorial/pa_install_screen_4.png "Map toevoegen")
  
1. U wordt nu gevraagd om de referenties in te voeren die nodig zijn om verbinding te maken met het AD-domein. Op hetzelfde scherm u de **prioriteit Voor domeincontroller selecteren** gebruiken om domeincontrollers op te geven die de agent moet gebruiken voor het verzenden van inrichtingsaanvragen.

   ![Domeinreferenties](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Na het configureren van het domein geeft het installatieprogramma een lijst met geconfigureerde domeinen weer. Op dit scherm u stap #5 herhalen en #6 om meer domeinen toe te voegen of op **Volgende** klikken om over te gaan tot agentregistratie.

   ![Geconfigureerde domeinen](./media/workday-inbound-tutorial/pa_install_screen_6.png "Geconfigureerde domeinen")

   > [!NOTE]
   > Als u meerdere AD-domeinen hebt (bijvoorbeeld na.contoso.com, emea.contoso.com), voegt u elk domein afzonderlijk toe aan de lijst.
   > Alleen het toevoegen van het bovenliggende domein (bijvoorbeeld contoso.com) is niet voldoende. U moet elk onderliggend domein registreren bij de agent.
   
1. Bekijk de configuratiegegevens en klik op **Bevestigen** om de agent te registreren.
  
   ![Scherm bevestigen](./media/workday-inbound-tutorial/pa_install_screen_7.png "Scherm bevestigen")
   
1. De wizard Configuratie geeft de voortgang van de agentregistratie weer.
  
   ![Agent registratie](./media/workday-inbound-tutorial/pa_install_screen_8.png "Agent registratie")
   
1. Zodra de agentregistratie is geslaagd, u op **Afsluiten** klikken om de wizard te verlaten.
  
   ![Scherm afsluiten](./media/workday-inbound-tutorial/pa_install_screen_9.png "Scherm afsluiten")
   
1. Controleer de installatie van de agent en controleer of deze wordt uitgevoerd door de module 'Services' te openen en te zoeken naar de service met de naam 'Microsoft Azure AD Connect Provisioning Agent'.
  
   ![Services](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Deel 3: Configureer in de inrichtingsapp de verbinding met SuccessFactors en Active Directory
In deze stap maken we verbinding met SuccessFactors en Active Directory in de Azure-portal. 

1. Ga in de Azure-portal terug naar de SuccessFactors naar Active Directory User Provisioning App die is gemaakt in [deel 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Vul de sectie **Beheerdersreferenties** als volgt in:

   * **Gebruikersnaam beheerder** : voer de gebruikersnaam van het Account van de SuccessFactors API-gebruikers in, waarbij de bedrijfs-id is toegevoegd. Het heeft het formaat: **\@gebruikersnaam companyID**

   * **Beheerderswachtwoord –** Voer het wachtwoord in van het Account van de Api-gebruikersaccount van SuccessFactors. 

   * **URL van tenant –** Voer de naam in van het eindpunt van de OData API-services van SuccesFactors. Voer alleen de hostnaam van de server in zonder http of https. Deze waarde moet eruit zien als: **<api-server-naam>.successfactors.com**.

   * **Active Directory Forest -** De naam van uw Active Directory-domein, zoals geregistreerd bij de agent. Gebruik de vervolgkeuzelijst om het doeldomein voor inrichten te selecteren. Deze waarde is meestal een tekenreeks zoals: *contoso.com*

   * **Active Directory-container -** Voer de container DN in waar de agent standaard gebruikersaccounts moet maken.
        Voorbeeld: *OU=Gebruikers,DC=contoso,DC=com*
        > [!NOTE]
        > Deze instelling wordt alleen in het spel gebracht voor gebruikersaccountcreaties als het *kenmerk ParentDistinguishedName* niet is geconfigureerd in de kenmerktoewijzingen. Deze instelling wordt niet gebruikt voor zoekopdrachten of updatebewerkingen van gebruikers. De volledige substructuur van het domein valt in het bereik van de zoekbewerking.

   * **Melding e-mail –** Voer uw e-mailadres in en schakel het selectievakje 'E-mail verzenden als er mislukt' in.
    > [!NOTE]
    > De Azure AD Provisioning Service verzendt e-mailmelding als de inrichtingstaak in een [quarantainestatus](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) terechtkomt.

   * Klik op de knop **Verbinding testen.** Als de verbindingstest slaagt, klikt u bovenaan op de knop **Opslaan.** Als dit niet lukt, controleert u of de referenties van SuccessFactors en de AD-referenties die zijn geconfigureerd op de installatie van de agent geldig zijn.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Zodra de referenties zijn opgeslagen, wordt in de sectie **Toewijzingen** de standaardtoewijzing **Spaarfactorengebruikers gesynchroniseerd met Active Directory on premises weergegeven.**

### <a name="part-4-configure-attribute-mappings"></a>Deel 4: Toewijzingen van kenmerken configureren

In deze sectie configureert u hoe gebruikersgegevens stromen van SuccessFactors naar Active Directory.

1. Klik op het tabblad Inrichten onder **Toewijzingen**op **Gebruikers van Succesfactoren synchroniseren met Active Directory voor on-premises**.

1. In het veld **Bronobjectbereik** u selecteren welke sets gebruikers in SuccessFactors in het bereik van het bedrijf moeten zijn, door een set op kenmerken gebaseerde filters te definiëren. De standaardscope is "alle gebruikers in SuccessFactors". Voorbeeldfilters:

   * Voorbeeld: Scope voor gebruikers met personIdExternal tussen 1000000 en 2000000 (exclusief 2000000)

      * Kenmerk: personIdExternal

      * Operator: REGEX Match

      * Waarde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Voorbeeld: Alleen werknemers en geen voorwaardelijke werknemers

      * Kenmerk: EmployeeID

      * Operator: IS NIET NULL

   > [!TIP]
   > Wanneer u de inrichtingsapp voor de eerste keer configureert, moet u uw kenmerktoewijzingen en -expressies testen en verifiëren om ervoor te zorgen dat deze het gewenste resultaat oplevert. Microsoft raadt aan om de scopingfilters onder **Source Object Scope** te gebruiken om uw toewijzingen te testen met een paar testgebruikers van SuccessFactors. Zodra u hebt geverifieerd dat de toewijzingen werken, u het filter verwijderen of geleidelijk uitbreiden met meer gebruikers.

   > [!CAUTION] 
   > Het standaardgedrag van de inrichtingsengine is het uitschakelen/verwijderen van gebruikers die buiten het bereik vallen. Dit is mogelijk niet wenselijk in uw SuccessFactors naar AD-integratie. Als u dit standaardgedrag wilt overschrijven, verwijst u naar het artikel [Verwijdering van gebruikersaccounts](../app-provisioning/skip-out-of-scope-deletions.md) overslaan die buiten het bereik vallen
  
1. In het veld **Doelobjectacties** u globaal filteren welke acties worden uitgevoerd in Active Directory. **Maken** en **bijwerken** komen het meest voor.

1. In de sectie **Toewijzingen van kenmerken** u definiëren hoe afzonderlijke SuccessFactors-kenmerken worden toegewezen aan Active Directory-kenmerken.

  >[!NOTE]
  >Raadpleeg de verwijzing naar [successfactors-attribuut Naslagrecht](../app-provisioning/sap-successfactors-attribute-reference.md) voor de volledige lijst met kenmerken van SuccessFactors die door de toepassing wordt ondersteund


1. Klik op een bestaande kenmerktoewijzing om deze bij te werken of klik op **Nieuwe toewijzing** onder aan het scherm toevoegen om nieuwe toewijzingen toe te voegen. Een afzonderlijke kenmerktoewijzing ondersteunt deze eigenschappen:

      * **Toewijzingstype**

         * **Direct** : hiermee schrijft u de waarde van het kenmerk SuccesFactors aan het AD-kenmerk, zonder wijzigingen

         * **Constant** - Schrijf een statische, constante tekenreekswaarde naar het AD-kenmerk

         * **Expressie** : hiermee u een aangepaste waarde naar het AD-kenmerk schrijven op basis van een of meer kenmerken van SuccessFactors. [Zie dit artikel over expressies voor meer informatie.](../app-provisioning/functions-for-customizing-application-data.md)

      * **Bronkenmerk** - Het gebruikerskenmerk van SuccessFactors

      * **Standaardwaarde** – Optioneel. Als het bronkenmerk een lege waarde heeft, wordt deze waarde in de toewijzing geschreven.
            Meest voorkomende configuratie is om deze leeg te laten.

      * **Kenmerk Doel** : het gebruikerskenmerk in Active Directory.

      * **Objecten matchen met dit kenmerk** : of deze toewijzing al dan niet moet worden gebruikt om gebruikers op unieke wijze te identificeren tussen SuccessFactors en Active Directory. Deze waarde wordt meestal ingesteld op het veld Werknemers-id voor SuccesFactors, dat doorgaans is toegewezen aan een van de kenmerken van werknemers-id in Active Directory.

      * **Overeenkomende prioriteit** – Meerdere overeenkomende kenmerken kunnen worden ingesteld. Wanneer er meerdere zijn, worden ze geëvalueerd in de volgorde die door dit veld is gedefinieerd. Zodra een overeenkomst wordt gevonden, worden geen verdere overeenkomende kenmerken geëvalueerd.

      * **Deze toewijzing toepassen**

         * **Altijd** – Deze toewijzing toepassen op zowel acties voor het maken en bijwerken van gebruikers

         * **Alleen tijdens het maken** - Pas deze toewijzing alleen toe op acties voor het maken van gebruikers

1. Als u uw toewijzingen wilt opslaan, klikt u boven aan de sectie Kenmerktoewijzing op **Opslaan.**

Zodra de configuratie van de toewijzing van kenmerken is voltooid, u [nu de service voor het inrichten van de gebruiker inschakelen en starten.](#enable-and-launch-user-provisioning)

## <a name="enable-and-launch-user-provisioning"></a>Gebruikersinrichting inschakelen en starten

Zodra de app-configuraties voor het inrichten van 1-1-apps zijn voltooid, u de inrichtingsservice inschakelen in de Azure-portal.

> [!TIP]
> Wanneer u de inrichtingsservice inschakelt, wordt standaard de inrichtingsbewerking gestart voor alle gebruikers in het bereik. Als er fouten zijn in de gegevensproblemen met toewijzingof SuccessFactors, kan de inrichtingstaak mislukken en in de quarantainestatus gaan. Om dit te voorkomen, raden we u aan het filter **Source Object Scope** te configureren en uw kenmerktoewijzingen te testen met een paar testgebruikers voordat u de volledige synchronisatie voor alle gebruikers start. Zodra u hebt geverifieerd dat de toewijzingen werken en geven u de gewenste resultaten, dan u ofwel verwijderen van het filter of geleidelijk uit te breiden naar meer gebruikers op te nemen.

1. Stel op het tabblad **Inrichten** de **inrichtingsstatus** in **op Aan**.

2. Klik op **Opslaan**.

3. Met deze bewerking wordt de eerste synchronisatie gestart, die een variabel aantal uren kan duren, afhankelijk van het aantal gebruikers dat zich in de tenant SuccessFactors bevindt. U de voortgangsbalk controleren om de voortgang van de synchronisatiecyclus bij te houden. 

4. Controleer op elk gewenst moment het tabblad **Controlelogboeken** in de Azure-portal om te zien welke acties de inrichtingsservice heeft uitgevoerd. De controlelogboeken bevatten alle afzonderlijke synchronisatiegebeurtenissen die door de inrichtingsservice worden uitgevoerd, zoals welke gebruikers worden voorgelezen uit SuccessFactors en vervolgens worden toegevoegd of bijgewerkt naar Active Directory. 

5. Zodra de eerste synchronisatie is voltooid, wordt een controleoverzichtrapport geschreven op het tabblad **Inname,** zoals hieronder wordt weergegeven.

   > [!div class="mx-imgBorder"]
   > ![Voortgangsbalk inrichten](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over ondersteunde SuccessFactors-kenmerken voor inkomende inrichting](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Meer informatie over het configureren van e-mailterugschrijven naar SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
* [Meer informatie over het configureren van eenmalige aanmelding tussen SuccessFactors en Azure Active Directory](successfactors-tutorial.md)
* [Meer informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het exporteren en importeren van uw inrichtingsconfiguraties](../app-provisioning/export-import-provisioning-configuration.md)
