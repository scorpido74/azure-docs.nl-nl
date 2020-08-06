---
title: 'Zelf studie: SuccessFactors inkomend inrichten configureren in AD en Azure AD | Microsoft Docs'
description: Meer informatie over het configureren van binnenkomende Provisioning vanuit SuccessFactors
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
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: 95c46550570d579af7ab8107686ad20838a3a62e
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809949"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning"></a>Zelf studie: SAP SuccessFactors configureren voor Active Directory gebruikers inrichting 
Het doel van deze zelf studie is het weer geven van de stappen die u moet uitvoeren om gebruikers in te richten vanuit SuccessFactors Employee Central in Active Directory (AD) en Azure AD, met een optionele write-back van e-mail adres naar SuccessFactors. 

>[!NOTE]
>Gebruik deze zelf studie als u wilt dat de gebruikers die u wilt inrichten vanuit SuccessFactors een on-premises AD-account en optioneel een Azure AD-account nodig hebben. Als de gebruikers van SuccessFactors alleen een Azure AD-account nodig hebben (alleen Cloud gebruikers), raadpleegt u de zelf studie over het [configureren van SAP SuccessFactors naar Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) -gebruikers inrichting. 


## <a name="overview"></a>Overzicht

De [Azure Active Directory User Provisioning Service](../app-provisioning/user-provisioning.md) kan worden geïntegreerd met het [SuccessFactors van werk nemers](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) , zodat de identiteits levenscyclus van gebruikers kan worden beheerd. 

De werk stromen voor het inrichten van SuccessFactors-gebruikers die worden ondersteund door de Azure AD User Provisioning-Service, maken het mogelijk om de volgende scenario's voor human resources en Identity Lifecycle Management te automatiseren:

* **Nieuwe werk nemers inhuren** : wanneer een nieuwe werk nemer wordt toegevoegd aan SuccessFactors, wordt automatisch een gebruikers account gemaakt in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md), met een terugschrijf bewerking van het e-mail adres naar SuccessFactors.

* **Updates van werknemers kenmerken en-profielen** : wanneer een werknemers record wordt bijgewerkt in SuccessFactors (zoals hun naam, titel of Manager), wordt het gebruikers account automatisch bijgewerkt in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

* **Beëindiging van werk nemers** : wanneer een werk nemer wordt beëindigd in SuccessFactors, wordt het gebruikers account automatisch uitgeschakeld in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

* Opnieuw **inhuren van werk nemers** : wanneer een werk nemer opnieuw wordt ingehuurd in SuccessFactors, kan het oude account automatisch opnieuw worden geactiveerd of worden ingericht (afhankelijk van uw voor keur) tot Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Wie is deze oplossing voor het inrichten van de gebruiker geschikt voor?

Deze SuccessFactors voor het Active Directory van de oplossing voor het inrichten van gebruikers is in het ideale geval geschikt voor:

* Organisaties die behoefte hebben aan een vooraf ontwikkelde, op de cloud gebaseerde oplossing voor het inrichten van SuccessFactors-gebruikers

* Organisaties die directe gebruikers inrichting van SuccessFactors naar Active Directory vereisen

* Organisaties die gebruikers moeten inrichten op basis van gegevens die zijn verkregen van de [SuccessFactors Employee Central (EG)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organisaties die aan een of meer Active Directory-forests, domeinen en organisatie-eenheden moeten worden gesynchroniseerd, kunnen deze verplaatsen en verlaten, alleen op basis van gewijzigde informatie die is gedetecteerd in [SuccessFactors Employee Central (EG)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organisaties die Office 365 gebruiken voor e-mail

## <a name="solution-architecture"></a>Architectuur van de oplossing

In deze sectie wordt de end-to-end-oplossings architectuur voor gebruikers ingericht voor algemene hybride omgevingen beschreven. Er zijn twee gerelateerde stromen:

* **Gezaghebbende HR-gegevens stroom: van SuccessFactors naar on-premises Active Directory:** In deze stroom worden werk gebeurtenissen (zoals nieuwe aanmeldingen, overdrachten, afsluitingen) voor het eerst uitgevoerd in de Cloud SuccessFactors Employee Central en vervolgens worden de gebeurtenis gegevens stromen naar on-premises Active Directory via Azure AD en de inrichtings agent. Afhankelijk van de gebeurtenis kan dit leiden tot het maken/bijwerken/inschakelen/uitschakelen van bewerkingen in AD.
* **Write-back-upflow: van on-premises Active Directory naar SuccessFactors:** Zodra het account is gemaakt in Active Directory, wordt het gesynchroniseerd met Azure AD via Azure AD Connect synchronisatie-en e-mail kenmerk kan worden teruggeschreven naar SuccessFactors.

  ![Overzicht](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Gegevens stroom van end-to-end-gebruikers

1. Het HR-team voert werknemers transacties (samenvoegers/verplaatsingen/Leavers of nieuwe huur/overdrachten/afsluitingen) uit in SuccessFactors werk nemers centraal
2. De Azure AD-inrichtings service voert geplande synchronisaties uit van identiteiten van SuccessFactors EC en identificeert wijzigingen die moeten worden verwerkt om te synchroniseren met on-premises Active Directory.
3. De Azure AD-inrichtings service roept de on-premises Azure AD Connect inrichtings agent aan met een aanvraag lading die AD-account maken/bijwerken/inschakelen/uitschakelen heeft.
4. De Azure AD Connect-inrichtings agent gebruikt een service account om AD-account gegevens toe te voegen of bij te werken.
5. De Azure AD Connect sync-engine voert Delta synchronisatie uit voor het ophalen van updates in AD.
6. De Active Directory updates worden gesynchroniseerd met Azure Active Directory.
7. Als de [SuccessFactors write-app](sap-successfactors-writeback-tutorial.md) is geconfigureerd, schrijft het het e-mail kenmerk terug naar SuccessFactors, op basis van het gebruikte kenmerk dat wordt gebruikt.

## <a name="planning-your-deployment"></a>Uw implementatie plannen

Het configureren van in de Cloud HR gestuurde gebruikers inrichten van SuccessFactors in AD vereist een aanzienlijke planning voor verschillende aspecten, zoals:
* Installatie van de Azure AD Connect-inrichtings agent 
* Aantal SuccessFactors aan AD-gebruikers die apps moeten inrichten
* Overeenkomende ID, kenmerk toewijzing, trans formatie en bereik filters

Raadpleeg het [implementatie plan voor Cloud HR](../app-provisioning/plan-cloud-hr-provision.md) voor uitgebreide richt lijnen rond deze onderwerpen. Raadpleeg de [SAP SuccessFactors-integratie referentie](../app-provisioning/sap-successfactors-integration-reference.md) voor meer informatie over de ondersteunde entiteiten, verwerkings gegevens en het aanpassen van de integratie voor verschillende HR-scenario's. 

## <a name="configuring-successfactors-for-the-integration"></a>SuccessFactors configureren voor de integratie

Een algemene vereiste van alle SuccessFactors-inrichtings connectors is dat er referenties van een SuccessFactors-account nodig zijn met de juiste machtigingen om de SuccessFactors OData-Api's aan te roepen. In deze sectie worden de stappen beschreven voor het maken van het service account in SuccessFactors en het verlenen van de juiste machtigingen. 

* [API-gebruikers account maken/identificeren in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Een functie voor API-machtigingen maken](#create-an-api-permissions-role)
* [Een machtigings groep maken voor de API-gebruiker](#create-a-permission-group-for-the-api-user)
* [Machtigings functie verlenen aan de machtigings groep](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>API-gebruikers account maken/identificeren in SuccessFactors
Werk samen met uw SuccessFactors-beheer team of implementatie partner om een gebruikers account in SuccessFactors te maken of te identificeren dat wordt gebruikt om de OData-Api's aan te roepen. De referenties van de gebruikers naam en het wacht woord van dit account zijn vereist bij het configureren van de inrichtings-apps in azure AD. 

### <a name="create-an-api-permissions-role"></a>Een functie voor API-machtigingen maken

* Meld u aan bij SAP SuccessFactors met een gebruikers account dat toegang heeft tot het beheer centrum.
* Zoek naar *machtigings rollen beheren*en selecteer vervolgens **machtigings rollen beheren** in de zoek resultaten.
  ![Machtigings rollen beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Klik in de lijst machtigings rollen op **nieuwe maken**.
  > [!div class="mx-imgBorder"]
  > ![Nieuwe machtigings functie maken](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Voeg een **rolnaam** en een **Beschrijving** voor de nieuwe machtigings functie toe. De naam en beschrijving moeten aangeven dat de rol is voor API-gebruiks machtigingen.
  > [!div class="mx-imgBorder"]
  > ![Details van machtigings rol](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Klik onder machtigings instellingen op **machtiging...**, Schuif omlaag in de lijst met machtigingen en klik op **integratie hulpprogramma's beheren**. Schakel het selectie vakje in om de beheerder toe te **staan om toegang te krijgen tot ODATA API via basis verificatie**.
  > [!div class="mx-imgBorder"]
  > ![Integratie hulpprogramma's beheren](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Schuif omlaag in hetzelfde vak en selecteer de **centrale API voor werk nemers**. Voeg machtigingen toe, zoals hieronder wordt weer gegeven, voor meer informatie over het gebruik van ODATA API en Edit met de ODATA-API. Selecteer de optie bewerken als u van plan bent hetzelfde account te gebruiken voor het terugschrijven naar het SuccessFactors-scenario. 
  > [!div class="mx-imgBorder"]
  > ![Lees machtigingen voor schrijven](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

  >[!NOTE]
  >Voor de volledige lijst met kenmerken die zijn opgehaald door deze inrichtings-app, verwijzen wij u naar [SuccessFactors-kenmerk verwijzing](../app-provisioning/sap-successfactors-attribute-reference.md)

* Klik op **gereed**. Klik op **Wijzigingen opslaan**.

### <a name="create-a-permission-group-for-the-api-user"></a>Een machtigings groep maken voor de API-gebruiker

* Zoek in het SuccessFactors-beheer centrum naar *machtigings groepen beheren*en selecteer vervolgens **machtigings groepen beheren** in de zoek resultaten.
  > [!div class="mx-imgBorder"]
  > ![Machtigings groepen beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Klik in het venster machtigings groepen beheren op **nieuwe maken**.
  > [!div class="mx-imgBorder"]
  > ![Nieuwe groep toevoegen](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Voeg een groeps naam voor de nieuwe groep toe. De groeps naam moet aangeven dat de groep voor API-gebruikers is.
  > [!div class="mx-imgBorder"]
  > ![Naam van de machtigings groep](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Leden toevoegen aan de groep. U kunt bijvoorbeeld **gebruikers naam** selecteren in de vervolg keuzelijst personen groep en vervolgens de gebruikers naam invoeren van het API-account dat wordt gebruikt voor de integratie. 
  > [!div class="mx-imgBorder"]
  > ![Groepsleden toevoegen](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Klik op **gereed** om het maken van de machtigings groep te volt ooien.

### <a name="grant-permission-role-to-the-permission-group"></a>Machtigings functie verlenen aan de machtigings groep

* Zoek in SuccessFactors-beheer centrum naar *machtigings rollen beheren*en selecteer vervolgens **machtigings rollen beheren** in de zoek resultaten.
* Selecteer in de **lijst machtigings rollen**de rol die u hebt gemaakt voor de machtigingen voor API-gebruik.
* Onder **deze rol toekennen aan..**. klikt u op de knop **toevoegen..** ..
* Selecteer de **machtigings groep..** . in de vervolg keuzelijst en klik vervolgens op **selecteren...** om het venster groepen te openen, te zoeken en de eerder gemaakte groep te selecteren. 
  > [!div class="mx-imgBorder"]
  > ![Machtigings groep toevoegen](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Controleer de machtigings rol verlenen aan de machtigings groep. 
  > [!div class="mx-imgBorder"]
  > ![Rol en groeps Details van machtiging](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Klik op **Wijzigingen opslaan**.

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Gebruikers inrichten configureren van SuccessFactors in Active Directory

In deze sectie vindt u de stappen voor het inrichten van gebruikers accounts van SuccessFactors naar elk Active Directory domein binnen het bereik van uw integratie.

* [De inrichtings connector-app toevoegen en de inrichtings agent downloaden](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [On-premises inrichtings agent (en) installeren en configureren](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Connectiviteit met SuccessFactors en Active Directory configureren](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Kenmerk toewijzingen configureren](#part-4-configure-attribute-mappings)
* [Gebruikers inrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Deel 1: de inrichtings connector-app toevoegen en de inrichtings agent downloaden

**SuccessFactors configureren voor het inrichten van Active Directory:**

1. Ga naar <https://portal.azure.com>

2. Selecteer in de linker navigatie balk de optie **Azure Active Directory**

3. Selecteer **bedrijfs toepassingen**en vervolgens **alle toepassingen**.

4. Selecteer **een toepassing toevoegen**en selecteer de categorie **alle** .

5. Zoek **SuccessFactors naar Active Directory gebruikers inrichten**en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weer gegeven, selecteert u **inrichting** maken

7. De **inrichtings** **modus** wijzigen in **automatisch**

8. Klik op het informatie banner dat wordt weer gegeven om de inrichtings agent te downloaden. 
   > [!div class="mx-imgBorder"]
   > ![Agent downloaden](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Scherm van de agent downloaden")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Deel 2: on-premises inrichtings agent (en) installeren en configureren

Als u een on-premises Active Directory wilt inrichten, moet de inrichtings agent zijn geïnstalleerd op een server met .NET 4.7.1 + Framework en netwerk toegang tot de gewenste Active Directory domein (en).

> [!TIP]
> U kunt de versie van .NET Framework op uw server controleren met behulp van de instructies die u [hier](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)kunt vinden.
> Als op de server geen .NET 4.7.1 of hoger is geïnstalleerd, kunt u deze [hier](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)downloaden.  

Zet het installatie programma van de gedownloade agent over naar de server host en volg de onderstaande stappen om de configuratie van de agent te volt ooien.

1. Meld u aan bij de Windows-Server waarop u de nieuwe agent wilt installeren.

1. Start het installatie programma voor de inrichtings agent, ga akkoord met de voor waarden en klik op de knop **installeren** .

   ![Scherm installeren](./media/workday-inbound-tutorial/pa_install_screen_1.png "Scherm installeren")
   
1. Nadat de installatie is voltooid, wordt de wizard gestart en wordt het scherm **verbinding maken met Azure AD** weer gegeven. Klik op de knop **verifiëren** om verbinding te maken met uw Azure AD-exemplaar.

   ![Verbinding maken met Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Verbinding maken met Azure AD")
   
1. Verifieer uw Azure AD-exemplaar met behulp van globale beheerders referenties.

   ![Beheerder auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Beheerder auth")

   > [!NOTE]
   > De referenties van de Azure AD-beheerder worden alleen gebruikt om verbinding te maken met uw Azure AD-Tenant. De-agent slaat de referenties niet lokaal op de server op.

1. Nadat de verificatie met Azure AD is geslaagd, wordt het scherm **verbinding maken Active Directory** weer gegeven. In deze stap voert u de naam van uw AD-domein in en klikt u op de knop **map toevoegen** .

   ![Map toevoegen](./media/workday-inbound-tutorial/pa_install_screen_4.png "Map toevoegen")
  
1. U wordt nu gevraagd om de referenties in te voeren die vereist zijn om verbinding te maken met het AD-domein. Op hetzelfde scherm kunt u de **prioriteit domein controller selecteren** gebruiken om domein controllers op te geven die de agent moet gebruiken voor het verzenden van inrichtings aanvragen.

   ![Domein referenties](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Na het configureren van het domein, wordt in het installatie programma een lijst met geconfigureerde domeinen weer gegeven. Op dit scherm kunt u stap #5 herhalen en #6 om meer domeinen toe te voegen of klikt u op **volgende** om door te gaan naar de agent registratie.

   ![Geconfigureerde domeinen](./media/workday-inbound-tutorial/pa_install_screen_6.png "Geconfigureerde domeinen")

   > [!NOTE]
   > Als u meerdere AD-domeinen hebt (bijvoorbeeld na.contoso.com, emea.contoso.com), moet u elk domein afzonderlijk toevoegen aan de lijst.
   > Het is niet voldoende om het bovenliggende domein (bijvoorbeeld contoso.com) toe te voegen. U moet elk onderliggend domein bij de agent registreren.
   
1. Controleer de configuratie gegevens en klik op **bevestigen** om de agent te registreren.
  
   ![Scherm bevestigen](./media/workday-inbound-tutorial/pa_install_screen_7.png "Scherm bevestigen")
   
1. De configuratie wizard geeft de voortgang weer van de registratie van de agent.
  
   ![Agent registratie](./media/workday-inbound-tutorial/pa_install_screen_8.png "Agent registratie")
   
1. Zodra de registratie van de agent is geslaagd, kunt u op **Afsluiten** klikken om de wizard af te sluiten.
  
   ![Scherm afsluiten](./media/workday-inbound-tutorial/pa_install_screen_9.png "Scherm afsluiten")
   
1. Controleer de installatie van de agent en zorg ervoor dat deze wordt uitgevoerd door de module ' Services ' te openen en te zoeken naar de service met de naam ' Microsoft Azure AD Connect inrichtings agent '.
  
   ![Services](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Deel 3: Configureer de verbinding met SuccessFactors en Active Directory in de app voor inrichting
In deze stap maken we verbinding met SuccessFactors en Active Directory in de Azure Portal. 

1. Ga in de Azure Portal terug naar de SuccessFactors naar Active Directory gebruikers inrichting app gemaakt in [deel 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Voer de sectie **beheerders referenties** als volgt uit:

   * **Gebruikers naam beheerder** : Voer de gebruikers naam in van het gebruikers account van de SUCCESSFACTORS-API, waarbij de bedrijfs-id is toegevoegd. Het heeft de volgende indeling: **gebruikers naam \@ companyID**

   * **Beheerders wachtwoord –** Voer het wacht woord van het gebruikers account van de SuccessFactors-API in. 

   * **Tenant-URL:** Voer de naam van het SuccessFactors OData API services-eind punt in. Voer alleen de hostnaam van de server in zonder http of https. Deze waarde moet er als volgt uitzien: **<-API-server naam>. successfactors.com**.

   * **Active Directory-forest-** De naam van uw Active Directory domein, zoals dit is geregistreerd bij de agent. Gebruik de vervolg keuzelijst om het doel domein te selecteren dat u wilt inrichten. Deze waarde is doorgaans een teken reeks zoals: *contoso.com*

   * **Active Directory-container-** Voer de container-DN in waar de agent standaard gebruikers accounts moet maken.
        Voor beeld: *OE = users, DC = contoso, DC = com*
        > [!NOTE]
        > Deze instelling is alleen beschikbaar voor het aanbrengen van gebruikers accounts als het kenmerk *parentDistinguishedName* niet is geconfigureerd in de kenmerk toewijzingen. Deze instelling wordt niet gebruikt voor zoek-of update bewerkingen voor gebruikers. De gehele onderliggende domein structuur valt binnen het bereik van de zoek bewerking.

   * **E-mail melding-** Voer uw e-mail adres in en schakel het selectie vakje e-mail verzenden als er een fout is opgetreden in.
    > [!NOTE]
    > De Azure AD-inrichtings service verzendt een e-mail melding als de inrichtings taak een [quarantaine](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) status heeft.

   * Klik op de knop **verbinding testen** . Als de verbindings test is geslaagd, klikt u bovenaan op de knop **Opslaan** . Als dit mislukt, controleert u of de SuccessFactors-referenties en de AD-referenties die zijn geconfigureerd voor de installatie van de agent geldig zijn.
    >[!div class="mx-imgBorder"]
    >![Azure-portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Zodra de referenties zijn opgeslagen, wordt in de sectie **toewijzingen** de standaard toewijzing weer gegeven **SuccessFactors-gebruikers synchroniseren met on-premises Active Directory**

### <a name="part-4-configure-attribute-mappings"></a>Deel 4: kenmerk toewijzingen configureren

In deze sectie configureert u hoe gebruikers gegevens stromen van SuccessFactors naar Active Directory.

1. Klik op het tabblad inrichting onder **toewijzingen**op **SuccessFactors gebruikers synchroniseren met on-premises Active Directory**.

1. In het veld **bereik van bron object** kunt u selecteren welke groepen gebruikers in SuccessFactors het bereik moeten hebben voor het INRICHTEN van AD door een set op kenmerken gebaseerde filters te definiëren. Het standaard bereik is alle gebruikers in SuccessFactors. Voorbeeld filters:

   * Voor beeld: bereik voor gebruikers met personIdExternal tussen 1000000 en 2000000 (m.u.v. 2000000)

      * Kenmerk: personIdExternal

      * Operator: overeenkomende REGEX

      * Waarde: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Voor beeld: alleen werk nemers en niet-voorwaardelijke werk nemers

      * Kenmerk: EmployeeID

      * Operator: IS niet NULL

   > [!TIP]
   > Wanneer u de inrichtings-app voor de eerste keer configureert, moet u de kenmerk toewijzingen en expressies testen en controleren om ervoor te zorgen dat het gewenste resultaat wordt weer geven. Micro soft raadt aan om de bereik filters onder het bereik van de **bron object** te gebruiken om uw toewijzingen te testen met een aantal test gebruikers van SuccessFactors. Wanneer u hebt gecontroleerd of de toewijzingen werken, kunt u het filter verwijderen of het bestand geleidelijk uitbreiden om meer gebruikers op te geven.

   > [!CAUTION] 
   > Het standaard gedrag van de inrichtings engine is het uitschakelen/verwijderen van gebruikers die buiten het bereik vallen. Dit is mogelijk niet wenselijk in uw SuccessFactors-to-AD-integratie. Als u dit standaard gedrag wilt overschrijven, raadpleegt u het artikel [verwijdering overs laan van gebruikers accounts die buiten het bereik](../app-provisioning/skip-out-of-scope-deletions.md) vallen
  
1. In het veld **acties doel object** kunt u globaal filteren welke acties er worden uitgevoerd op Active Directory. **Create** en **Update** worden het meest gebruikt.

1. In de sectie **kenmerk toewijzingen** kunt u definiëren hoe afzonderlijke SuccessFactors kenmerken worden toegewezen aan Active Directory kenmerken.

  >[!NOTE]
  >Voor de volledige lijst met SuccessFactors-kenmerken die door de toepassing worden ondersteund, verwijzen wij u naar [SuccessFactors-kenmerk verwijzing](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Klik op een bestaande kenmerk toewijzing om het bij te werken of Klik onder aan het scherm op **nieuwe toewijzing toevoegen** om nieuwe toewijzingen toe te voegen. Een individuele kenmerk toewijzing ondersteunt de volgende eigenschappen:

      * **Toewijzings type**

         * **Direct** : schrijft de waarde van het kenmerk SuccessFactors naar het kenmerk AD, zonder wijzigingen

         * **Constante** : een statische, constante teken reeks waarde schrijven naar het AD-kenmerk

         * **Expressie** : Hiermee kunt u een aangepaste waarde schrijven naar het AD-kenmerk, op basis van een of meer SuccessFactors-kenmerken. [Zie dit artikel over expressies voor meer informatie](../app-provisioning/functions-for-customizing-application-data.md).

      * **Bron kenmerk** -het gebruikers kenmerk van SuccessFactors

      * **Standaard waarde** : optioneel. Als het bron kenmerk een lege waarde heeft, wordt deze waarde in plaats daarvan door de toewijzing geschreven.
            De meest voorkomende configuratie is om dit leeg te laten.

      * **Doel kenmerk** : het gebruikers kenmerk in Active Directory.

      * **Objecten met dit kenmerk matchen** : bepaalt of deze toewijzing moet worden gebruikt om gebruikers te identificeren tussen SuccessFactors en Active Directory. Deze waarde wordt doorgaans ingesteld op het veld worker-ID voor SuccessFactors, die meestal wordt toegewezen aan een van de kenmerken van de werk nemer-ID in Active Directory.

      * **Overeenkomende prioriteit** : meerdere overeenkomende kenmerken kunnen worden ingesteld. Wanneer er meerdere zijn, worden deze geëvalueerd in de volg orde die is gedefinieerd door dit veld. Zodra er een overeenkomst wordt gevonden, worden er geen verdere overeenkomende kenmerken geëvalueerd.

      * **Deze toewijzing Toep assen**

         * **Altijd** : deze toewijzing Toep assen op zowel het maken van gebruikers als bij het bijwerken van acties

         * **Alleen tijdens het maken** : pas deze toewijzing alleen toe bij het maken van gebruikers acties

1. Klik boven aan de sectie kenmerk toewijzing op **Opslaan** om uw toewijzingen op te slaan.

Zodra de configuratie van de kenmerk toewijzing is voltooid, kunt u [de User Provisioning Service nu inschakelen en starten](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Gebruikers inrichting inschakelen en starten

Zodra de configuratie van de SuccessFactors-inrichting is voltooid, kunt u de inrichtings service inschakelen in de Azure Portal.

> [!TIP]
> Wanneer u de inrichtings service inschakelt, worden er standaard inrichtings bewerkingen gestart voor alle gebruikers binnen het bereik. Als er fouten zijn opgetreden in de toewijzings-of SuccessFactors, kan de inrichtings taak mislukken en gaat u naar de status van de quarantaine. Om dit te voor komen best practice, raden we u aan om het bereik filter voor **bron objecten** te configureren en uw kenmerk toewijzingen te testen met enkele test gebruikers voordat u de volledige synchronisatie voor alle gebruikers start. Wanneer u hebt gecontroleerd of de toewijzingen werken en u de gewenste resultaten krijgt, kunt u het filter verwijderen of het bestand geleidelijk uitbreiden om meer gebruikers op te geven.

1. Stel op het tabblad **inrichten** de **inrichtings status** in **op aan**.

2. Klik op **Opslaan**.

3. Met deze bewerking wordt de eerste synchronisatie gestart, wat een variabel aantal uur kan duren, afhankelijk van het aantal gebruikers in de SuccessFactors-Tenant. U kunt de voortgangs balk controleren om de voortgang van de synchronisatie cyclus bij te houden. 

4. Controleer op elk gewenst moment het tabblad **controle logboeken** in de Azure Portal om te zien welke acties de inrichtings service heeft uitgevoerd. In de controle logboeken worden alle afzonderlijke synchronisatie gebeurtenissen weer gegeven die door de inrichtings service worden uitgevoerd, bijvoorbeeld welke gebruikers worden gelezen van SuccessFactors en vervolgens worden toegevoegd of bijgewerkt aan Active Directory. 

5. Zodra de initiële synchronisatie is voltooid, wordt een overzichts rapport van de controle op het tabblad **inrichten** geschreven, zoals hieronder wordt weer gegeven.

   > [!div class="mx-imgBorder"]
   > ![Voortgangs balk inrichten](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over ondersteunde SuccessFactors-kenmerken voor inkomende inrichting](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Meer informatie over het configureren van write-back van e-mail naar SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
* [Meer informatie over het configureren van eenmalige aanmelding tussen SuccessFactors en Azure Active Directory](successfactors-tutorial.md)
* [Meer informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het exporteren en importeren van uw inrichtings configuraties](../app-provisioning/export-import-provisioning-configuration.md)
