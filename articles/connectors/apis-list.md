---
title: Connectors voor Azure Logic Apps
description: Werkstromen automatiseren met connectors voor Azure Logic Apps, zoals ingebouwde, beheerde, on-premises, integratieaccount, ISE- en bedrijfsconnectoren
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: 3010f3c99a5b214c2503f890321cbb73427e3c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247330"
---
# <a name="connectors-for-azure-logic-apps"></a>Connectors voor Azure Logic Apps

Connectors bieden snel toegang vanuit Azure Logic Apps naar gebeurtenissen, gegevens en acties in andere apps, services, systemen, protocollen en platformen. Door connectors in uw logische apps te gebruiken, vergroot u de mogelijkheden voor uw cloud- en on-premises apps om taken uit te voeren met de gegevens die u maakt en die u al hebt.

Hoewel Logic Apps [honderden connectors](https://docs.microsoft.com/connectors)biedt, beschrijft dit artikel de populaire en *meest gebruikte* connectors die met succes worden gebruikt door duizenden apps en miljoenen uitvoeringen voor het verwerken van gegevens en informatie. Als u de volledige lijst met connectoren en de referentiegegevens van elke connector wilt vinden, zoals triggers, acties en limieten, controleert u de referentiepagina's voor [connectoren](https://docs.microsoft.com/connectors)onder overzicht connectors . Lees ook meer over [triggers en acties,](#triggers-actions) [logic apps-prijsmodel](../logic-apps/logic-apps-pricing.md)en [prijsdetails van Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

> [!TIP]
> Als u wilt integreren met een service of API die geen connector heeft, u de service rechtstreeks aanroepen via een protocol zoals HTTP of een [aangepaste connector](#custom)maken.

## <a name="connector-types"></a>Verbindingstypen

Connectors zijn beschikbaar als ingebouwde triggers en acties of als beheerde connectors.

<a name="built-in"></a>

* [**Ingebouwd:**](#built-ins)ingebouwde triggers en acties zijn 'native' voor Azure Logic Apps en helpen u deze taken uit te voeren voor uw logische apps:

  * Uitvoeren op aangepaste en geavanceerde schema's.

  * Organiseer en beheer de workflow van uw logische app, bijvoorbeeld loops en voorwaarden, en ook om te werken met variabelen en gegevensbewerkingen.

  * Communiceer met andere eindpunten.

  * Ontvangen en reageren op verzoeken.

  * Call Azure-functies, Azure API Apps (Web Apps), uw eigen API's worden beheerd en gepubliceerd met Azure API Management en geneste logische apps die aanvragen kunnen ontvangen.

<a name="managed-connectors"></a>

* [**Beheerde connectors**](#managed-api-connectors): Geïmplementeerd en beheerd door Microsoft, deze connectors bieden triggers en acties voor toegang tot cloudservices, on-premises systemen of beide, waaronder Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint en meer. Sommige connectors ondersteunen specifiek b2b-communicatiescenario's (business-to-business) en vereisen een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dat is gekoppeld aan uw logica-app. Voordat u bepaalde connectors gebruikt, moet u mogelijk eerst verbindingen maken die worden beheerd door Azure Logic Apps.

  Als u bijvoorbeeld Microsoft BizTalk Server gebruikt, kunnen uw logische apps verbinding maken met en communiceren met uw BizTalk-server met behulp van de [on-premises connector van biztalkserver.](#on-premises-connectors) U vervolgens BizTalk-achtige bewerkingen uitbreiden of uitvoeren in uw logische apps met behulp van de [integratieaccountconnectors.](#integration-account-connectors)

  Connectors worden geclassificeerd als Standaard of Enterprise. [Enterprise-connectors](#enterprise-connectors) bieden toegang tot bedrijfssystemen zoals SAP, IBM MQ en IBM 3270 tegen meerprijs. Zie de technische details in de referentiepagina van elke connector onder [Overzicht Connectors](https://docs.microsoft.com/connectors)om te bepalen of een connector standaard of enterprise is.

  U connectors ook identificeren met behulp van deze categorieën, hoewel sommige connectors meerdere categorieën kunnen overschrijden. SAP is bijvoorbeeld een Enterprise-connector en een on-premises connector:

  |   |   |
  |---|---|
  | [**Beheerde connectors**](#managed-api-connectors) | Maak logische apps die gebruikmaken van services zoals Azure Blob Storage, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online en nog veel meer. |
  | [**On-premises connectors**](#on-premises-connectors) | Nadat u de [on-premises gegevensgateway][gateway-doc]hebt geïnstalleerd en ingesteld, helpen deze connectors uw logische apps toegang te krijgen tot on-premises systemen zoals SQL Server, SharePoint Server, Oracle DB, bestandsshares en anderen. |
  | [**Integratieaccountconnectoren**](#integration-account-connectors) | Deze connectors zijn beschikbaar wanneer u een integratieaccount maakt en betaalt, transformeren en valideren van XML, coderen en decoderen van platte bestanden en verwerken B2B-berichten (business-to-business) met AS2-, EDIFACT- en X12-protocollen. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>Verbinding maken vanuit een integratieserviceomgeving

Voor logische apps die directe toegang tot bronnen in een virtueel Azure-netwerk nodig hebben, u een geïsoleerde [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) maken waar u uw logische apps bouwen, implementeren en uitvoeren op speciale bronnen. Wanneer u in de Logic App Designer door de connectors bladert die u wilt gebruiken voor logische apps in een ISE, wordt een **CORE-label** weergegeven op ingebouwde triggers en acties, terwijl het **ISE-label** op sommige connectoren wordt weergegeven:

* **CORE:** Ingebouwde triggers en acties met dit label worden uitgevoerd in dezelfde ISE als uw logische apps, bijvoorbeeld:

  ![Voorbeeld ISE-connector](./media/apis-list/example-core-connector.png)

* **ISE**: Beheerde connectors met dit label worden uitgevoerd in dezelfde ISE als uw logische apps, bijvoorbeeld:

  ![Voorbeeld ISE-connector](./media/apis-list/example-ise-connector.png)

  Als u een on-premises systeem hebt dat is verbonden met een virtueel Azure-netwerk, heeft een ISE uw logische apps rechtstreeks toegang tot dat systeem zonder de [on-premises datagateway.](../logic-apps/logic-apps-gateway-connection.md) In plaats daarvan u de **ISE-connector** van dat systeem gebruiken indien beschikbaar, een HTTP-actie of een [aangepaste connector.](#custom) Voor on-premises systemen die geen **ISE-connectors** hebben, gebruikt u on-premises datagateway. Zie [ISE-connectors](#ise-connectors)voor het bekijken van beschikbare ISE-connectors.

* Alle andere connectors zonder het **CORE-** of **ISE-label,** die u blijven gebruiken, worden uitgevoerd in de wereldwijde Logic Apps-service met meerdere tenant, bijvoorbeeld:

  ![Voorbeeld van multi-tenantconnector](./media/apis-list/example-multi-tenant-connector.png)

Logische apps die worden uitgevoerd in een ISE en hun connectors, ongeacht waar deze connectors worden uitgevoerd, volgen een vast prijsplan ten opzichte van het op verbruik gebaseerde prijsplan. Zie voor meer informatie de volgende pagina's:

* [Prijsmodel voor logische apps](../logic-apps/logic-apps-pricing.md)
* [Prijsgegevens van Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>Ingebouwd

Logic Apps biedt ingebouwde triggers en acties, zodat u werkstromen op basis van planning maken, uw logische apps helpen communiceren met andere apps en services, de werkstroom beheren via uw logische apps en gegevens beheren of manipuleren.

|   |   |   |   |
|---|---|---|---|
| [![][schedule-icon]<br>**API-pictogramplanning**][schedule-doc] | - Voer een logische app uit op een opgegeven herhaling, variërend van basis- tot geavanceerde schema's met de [ **Recidief-trigger** ][schedule-recurrence-doc]. <p>- Voer een logische app uit die gegevens in continue segmenten moet verwerken met de [ **trigger schuifvenster.** ][schedule-sliding-window-doc] <p>- Pauzeer uw logische app voor een bepaalde duur met de [ **actie Vertraging** ][schedule-delay-doc]. <p>- Pauzeer uw logische app tot de opgegeven datum en tijd met de [ **actie Vertraging tot** actie][schedule-delay-until-doc]. | [![][batch-icon]<br>**Batch** api-pictogram][batch-doc] | - Verwerk berichten in batches met de **trigger batchberichten.** <p>- Bel logische apps met bestaande batchtriggers met de actie **Berichten verzenden naar batch.** |
| [![API-pictogram][http-icon]<br>**HTTP**][http-doc] | Bel HTTP- of HTTPS-eindpunten met triggers en acties voor HTTP. Andere HTTP ingebouwde triggers en acties zijn [HTTP + Swagger][http-swagger-doc] en [HTTP + Webhook][http-webhook-doc]. | [![][http-request-icon]<br>**API-pictogramaanvraag**][http-request-doc] | - Maak uw logische app aanroepbaar vanuit andere apps of services, trigger op Gebeurtenisgrid resource gebeurtenissen, of trigger op reacties op Azure Security Center waarschuwingen met de **trigger Verzoek.** <p>- Stuur reacties naar een app of service met de **actie Respons.** |
| [![API-pictogram][azure-api-management-icon]<br>**Azure API-beheer <br>**][azure-api-management-doc] | Oproeptriggers en acties gedefinieerd door uw eigen API's die u beheert en publiceert met Azure API Management. | [![API-pictogram][azure-app-services-icon]<br>**Azure App <br>Services**][azure-app-services-doc] | Bel Azure API Apps of Web Apps, gehost op Azure App Service. De triggers en acties die door deze apps worden gedefinieerd, worden weergegeven als alle andere eersteklas triggers en acties wanneer Swagger is opgenomen.|
| [![API-pictogram][azure-logic-apps-icon]<br>**Azure Logic <br>Apps**][nested-logic-app-doc] | Roep andere logische apps die beginnen met de **trigger Van Verzoek.** |
|||||

### <a name="run-code-from-logic-apps"></a>Code uitvoeren vanuit logische apps

Logic Apps biedt ingebouwde acties voor het uitvoeren van uw eigen code in de werkstroom van uw logische app:

|   |   |   |   |
|---|---|---|---|
| [![API-pictogram][azure-functions-icon]<br>**Azure-functies**][azure-functions-doc] | Bel Azure-functies die aangepaste codefragmenten (C# of Node.js) uitvoeren vanuit uw logische apps. | [![API-pictogram][inline-code-icon]<br>**Inline-code**][azure-functions-doc] | JavaScript-codefragmenten toevoegen en uitvoeren vanuit uw logische apps. |
|||||

### <a name="control-workflow"></a>Werkstroom beheren

Logic Apps biedt ingebouwde acties voor het structureren en beheren van de acties in de werkstroom van uw logische app:

|   |   |   |   |
|---|---|---|---|
| [![Ingebouwde][condition-icon]<br>**pictogramvoorwaarde**][condition-doc] | Evalueer een voorwaarde en voer verschillende acties uit op basis van of de voorwaarde waar of onwaar is. | [![Ingebouwd pictogram][for-each-icon]<br>**voor elk**][for-each-doc] | Voer dezelfde acties uit op elk item in een array. |
| [![Ingebouwde][scope-icon]<br>**iconscope**][scope-doc] | Groepeer acties in *scopes,* die hun eigen status krijgen nadat de acties in de scope zijn uitgevoerd. | [![Ingebouwde][switch-icon]<br>**pictogramschakelaar**][switch-doc] | Groeper acties in *aanvragen,* waaraan unieke waarden zijn toegewezen, behalve voor de standaardaanvraag. Voer alleen die kwestie uit waarvan de toegewezen waarde overeenkomt met het resultaat van een expressie, object of token. Als er geen overeenkomsten bestaan, voert u de standaardaanvraag uit. |
| [![Ingebouwd pictogram][terminate-icon]<br>**beëindigen**][terminate-doc] | Een actief actieve logische app-werkstroom stoppen. | [![Ingebouwd pictogram][until-icon]<br>**tot**][until-doc] | Herhaal acties totdat de opgegeven voorwaarde true is of een bepaalde status is gewijzigd. |
|||||

### <a name="manage-or-manipulate-data"></a>Gegevens beheren of manipuleren

Logic Apps biedt ingebouwde acties voor het werken met gegevensuitvoer en hun indelingen:

|   |   |
|---|---|
| [![Ingebouwde][data-operations-icon]<br>**gegevensbewerkingen voor pictogramgegevens**][data-operations-doc] | Bewerkingen uitvoeren met gegevens: <p>- **Componeren:** Maak één uitvoer van meerdere ingangen met verschillende typen. <br>- **CSV-tabel maken:** maak een CSV-tabel (comma-separated-value) uit een array met JSON-objecten. <br>- **HTML-tabel maken:** een HTML-tabel maken op basis van een array met JSON-objecten. <br>- **Filterarray:** maak een array van items in een andere array die aan uw criteria voldoen. <br>- **Join:** Maak een tekenreeks van alle items in een array en scheid deze items met de opgegeven scheidingsteken. <br>- **Parse JSON**: Maak gebruiksvriendelijke tokens van eigenschappen en hun waarden in JSON-inhoud, zodat u deze eigenschappen in uw workflow gebruiken. <br>- **Selecteer**: Maak een array met JSON-objecten door items of waarden in een andere array te transformeren en deze items in kaart te brengen aan opgegeven eigenschappen. |
| ![Pictogram ingebouwde][date-time-icon]<br>**Datumtijd** | Bewerkingen uitvoeren met tijdstempels: <p>- **Toevoegen aan tijd:** Voeg het opgegeven aantal eenheden toe aan een tijdstempel. <br>- **Tijdzone converteren:** Converteer een tijdstempel van de brontijdzone naar de doeltijdzone. <br>- **Huidige tijd:** retourneer de huidige tijdstempel als tekenreeks. <br>- **Toekomstige tijd opdoen:** retourneer de huidige tijdstempel plus de opgegeven tijdseenheden. <br>- **De tijd voorbijkomen:** retourneer de huidige tijdstempel minus de opgegeven tijdeenheden. <br>- **Aftrekken van tijd**: Trek een aantal tijdeenheden af van een tijdstempel. |
| [![Ingebouwde][variables-icon]<br>**pictogramvariabelen**][variables-doc] | Bewerkingen uitvoeren met variabelen: <p>- **Toevoegen aan matrixvariabele:** voeg een waarde in als het laatste item in een array die is opgeslagen door een variabele. <br>- **Toevoegen aan tekenreeksvariabele**: Voeg een waarde in als het laatste teken in een tekenreeks die is opgeslagen door een variabele. <br>- **Decrement variabele**: Verlaag een variabele met een constante waarde. <br>- **Increment variabele**: Verhoog een variabele met een constante waarde. <br>- **Variabele initialiseren**: Maak een variabele en declareer het gegevenstype en de initiële waarde. <br>- **Variabele instellen:** wijs een andere waarde toe aan een bestaande variabele. |
|  |  |

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Beheerde connectors

Logic Apps biedt deze populaire standaardconnectors voor het automatiseren van taken, processen en workflows met deze services of systemen:

|   |   |   |   |
|---|---|---|---|
| [![API-pictogram][azure-service-bus-icon]<br>**Azure Service Bus**][azure-service-bus-doc] | Beheer asynchrone berichten, sessies en abonnementen op onderwerpen met behulp van de meest gebruikte connector in Logic Apps. | [![API-pictogram][sql-server-icon]<br>**SQL Server**][sql-server-doc] | Maak verbinding met uw SQL Server on-premises of een Azure SQL Database in de cloud, zodat u records beheren, opgeslagen procedures uitvoeren of query's uitvoeren. |
| [![API-pictogram][azure-blob-storage-icon]<br>**Azure Blob-opslag<br>**][azure-blob-storage-doc] | Maak verbinding met uw opslagaccount, zodat u blob-inhoud maken en beheren. | [![API-pictogram][office-365-outlook-icon]<br>**Office<br>365 Outlook**][office-365-outlook-doc] | Maak verbinding met uw Office 365-e-mailaccount, zodat u e-mails, taken, agenda-afspraken en vergaderingen, contactpersonen, verzoeken en meer maken en beheren. |
| [![API-pictogram][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc] | Maak verbinding met SFTP-servers die u vanaf internet openen via SSH, zodat u met uw bestanden en mappen werken. | [![API-pictogram][sharepoint-online-icon]<br>**<br>SharePoint Online**][sharepoint-online-doc] | Maak verbinding met SharePoint Online, zodat u bestanden, bijlagen, mappen en meer beheren. | 
| [![API-pictogram][dynamics-365-icon]<br>**Dynamics<br> 365**][dynamics-365-doc] | Maak verbinding met je Dynamics 365-account, zodat je records, items en meer maken en beheren. | [![Azure-wachtrijen voor][azure-queues-icon]<br>API-pictogram** <br>**][azure-queues-doc] | Maak verbinding met uw Azure Storage-account, zodat u wachtrijen en berichten maken en beheren |
| [![API-pictogram][ftp-icon]<br>**FTP**][ftp-doc] | Maak verbinding met FTP-servers die u vanaf internet openen, zodat u met uw bestanden en mappen werken. | [![Api-pictogram][file-system-icon]<br>**Bestandssysteem <br>**][file-system-doc] | Maak verbinding met uw on-premises bestandsshare, zodat u bestanden maken en beheren. |
| [![API-pictogram][azure-event-hubs-icon]<br>**Azure Event Hubs**][azure-event-hubs-doc] | Gebeurtenissen consumeren en publiceren via een gebeurtenishub. Download bijvoorbeeld uitvoer uit uw logische app met Gebeurtenishubs en stuur die uitvoer naar een realtime analyseprovider. | [![API-pictogram][azure-event-grid-icon]<br>**Azure Event**<br>**Grid**][azure-event-grid-doc] | Monitor gebeurtenissen die zijn gepubliceerd door een gebeurtenisraster, bijvoorbeeld wanneer Azure-resources of resources van derden worden gewijzigd. |
| [![API-pictogram][salesforce-icon]<br>**Salesforce**][salesforce-doc] | Maak verbinding met je Salesforce-account, zodat je items maken en beheren, zoals records, taken, objecten en meer. | [![API-pictogram][twitter-icon]<br>**Twitter**][twitter-doc] | Maak verbinding met je Twitter-account, zodat je tweets, volgers, je tijdlijn en meer beheren. Sla uw tweets op in SQL, Excel of SharePoint. |
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>On-premises connectors

Hier volgen enkele veelgebruikte standaardconnectors die Logic Apps biedt voor toegang tot gegevens en bronnen in on-premises systemen. Voordat u een verbinding met een on-premises systeem maken, moet u eerst [een on-premises gegevensgateway downloaden, installeren en instellen.][gateway-doc] Deze gateway biedt een veilig communicatiekanaal zonder de benodigde netwerkinfrastructuur in te stellen.

|   |   |   |   |   |
|---|---|---|---|---|
| [![API-pictogram][biztalk-server-icon]<br>**BizTalk-server** <br> **Server**][biztalk-server-doc] | [![Api-pictogram][file-system-icon]<br>**Bestandssysteem <br>**][file-system-doc] | [![API-pictogram][ibm-db2-icon]<br>**IBM DB2**][ibm-db2-doc] | [![API-pictogram][ibm-informix-icon]<br>**IBM** <br> **Informix**][ibm-informix-doc] | [![API-pictogram][mysql-icon]<br>**MySQL**][mysql-doc] |
| [![API-pictogram][oracle-db-icon]<br>**Oracle DB**][oracle-db-doc] | [![API-pictogram][postgre-sql-icon]<br>**PostgreSQL**][postgre-sql-doc] | [![API-pictogram][sharepoint-server-icon]<br>** <br>SharePoint Server**][sharepoint-server-doc] | [![API-pictogram][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc] | [![API-pictogram][teradata-icon]<br>**Teradata**][teradata-doc] |
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Integratieaccountconnectoren

Logic Apps biedt standaardconnectors voor het bouwen van B2B-oplossingen (business-to-business) met uw logische apps wanneer u een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)maakt en betaalt , dat beschikbaar is via het Enterprise Integration Pack (EIP) in Azure. Met dit account u B2B-artefacten maken en opslaan, zoals handelspartners, overeenkomsten, kaarten, schema's, certificaten, enzovoort. Als u deze artefacten wilt gebruiken, koppelt u uw logische apps aan uw integratieaccount. Als u momenteel BizTalk Server gebruikt, lijken deze connectors misschien al bekend.

|   |   |   |   |
|---|---|---|---|
| [![API-pictogram][as2-icon]<br>** <br>AS2-decodering**][as2-doc] | [![API-pictogram][as2-icon]<br>** <br>AS2-codering**][as2-doc] | [![API-pictogram][edifact-icon]<br>**EDIFACT-decodering <br>**][edifact-decode-doc] | [![API-pictogram][edifact-icon]<br>**EDIFACT-codering <br>**][edifact-encode-doc] |
| [![API-pictogram][flat-file-decode-icon]<br>**Platte bestandsdecodering <br>**][flat-file-decode-doc] | [![API-pictogram][flat-file-encode-icon]<br>**Platte bestandscodering <br>**][flat-file-encode-doc] | [![API-pictogram][integration-account-icon]<br>**Integratie-account <br>**][integration-account-doc] | [![API-pictogram][liquid-icon]<br>**Liquid** <br> **transformeert**][json-liquid-transform-doc] |
| [![API-pictogram][x12-icon]<br>**X12-decodering <br>**][x12-decode-doc] | [![API-pictogram][x12-icon]<br>**X12-codering <br>**][x12-encode-doc] | [![XML-transformaties][xml-transform-icon]<br>**voor** <br> **API-pictogram**][xml-transform-doc] | [![][xml-validate-icon]<br>**XML-validatie <br>** voor API-pictogram][xml-validate-doc] |  
|||||

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Bedrijfsconnectoren

Logic Apps biedt deze Enterprise-connectors voor toegang tot bedrijfssystemen, zoals SAP en IBM MQ:

|   |   |   |
|---|---|---|
| [![API-pictogram][ibm-3270-icon]<br>**IBM 3270**][ibm-3270-doc] | [![API-pictogram][ibm-mq-icon]<br>**IBM MQ**][ibm-mq-doc] | [![API-pictogram][sap-icon]<br>**SAP**][sap-connector-doc] |
||||

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>ISE-connectoren

Voor logische apps die u maakt en een geïsoleerde [integratieserviceomgeving (ISE)](#integration-service-environment)maakt en uitvoert, identificeert de Logic App Designer ingebouwde triggers en acties die in uw ISE worden uitgevoerd met behulp van het **CORE-label.** Beheerde connectors die in een ISE worden uitgevoerd, geven het **ISE-label** weer, terwijl connectors die worden uitgevoerd in de algemene Logic Apps-service met meerdere tenant's geen van beide labels weergeven. In deze lijst worden de connectors weergegeven die momenteel ISE-versies hebben:

|   |   |   |   |   |
|---|---|---|---|---|
[![API-pictogram][as2-icon]<br>**AS2**][as2-doc] | [![API-pictogram][azure-blob-storage-icon]<br>**Azure Blob-opslag<br>**][azure-blob-storage-doc] | [![API-pictogram][azure-cosmos-db-icon]<br>**Azure Cosmos <br> DB**][azure-cosmos-db-doc] | [![API-pictogram][azure-event-hubs-icon]<br>**Azure Event <br>Hubs**][azure-event-hubs-doc] | [![API-pictogram][azure-file-storage-icon]<br>**Azure File<br>Storage**][azure-file-storage-doc] |
| [![API-pictogram][azure-service-bus-icon]<br>**Azure Service <br>Bus**][azure-service-bus-doc] | [![API-pictogram][azure-sql-data-warehouse-icon]<br>**Azure <br>SQL Data Warehouse**][azure-sql-data-warehouse-doc] | [![API-pictogram][azure-table-storage-icon]<br>**Azure Table <br>Storage**][azure-table-storage-doc] | [![Azure-wachtrijen voor][azure-queues-icon]<br>API-pictogram** <br>**][azure-queues-doc] | [![API-pictogram][edifact-icon]<br>**EDIFACT**][edifact-doc] |
| [![Api-pictogram][file-system-icon]<br>**Bestandssysteem <br>**][file-system-doc] | [![API-pictogram][ftp-icon]<br>**FTP**][ftp-doc] | [![API-pictogram][ibm-3270-icon]<br>**IBM 3270**][ibm-3270-doc] | [![API-pictogram][ibm-db2-icon]<br>**IBM DB2**][ibm-db2-doc] | [![API-pictogram][ibm-mq-icon]<br>**IBM MQ**][ibm-mq-doc] |
| [![API-pictogram][sap-icon]<br>**SAP**][sap-connector-doc] | [![API-pictogram][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc] | [![API-pictogram][smtp-icon]<br>**SMTP**][smtp-doc] | [![API-pictogram][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc] | [![API-pictogram][x12-icon]<br>**X12**][x12-doc] |
||||||

Zie deze onderwerpen voor meer informatie:

* [Toegang tot virtuele Azure-netwerkbronnen vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Prijsmodel voor logische apps](../logic-apps/logic-apps-pricing.md)
* [Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Triggers en actietypen

Connectors kunnen *triggers,* *acties*of beide bieden. Een *trigger* is de eerste stap in een logische app, waarbij meestal de gebeurtenis wordt opgegeven die de trigger afvuurt en uw logische app start. De FTP-connector heeft bijvoorbeeld een trigger waarmee uw logische app wordt gestart "wanneer een bestand wordt toegevoegd of gewijzigd". Sommige triggers controleren regelmatig op de opgegeven gebeurtenis of gegevens en vuren vervolgens wanneer ze de opgegeven gebeurtenis of gegevens detecteren. Andere triggers wachten, maar vuren direct wanneer een specifieke gebeurtenis plaatsvindt of wanneer nieuwe gegevens beschikbaar zijn. Triggers geven ook alle vereiste gegevens door aan uw logische app. Uw logische app kan die gegevens lezen en gebruiken in de hele werkstroom. De Twitter-connector heeft bijvoorbeeld een trigger , "Wanneer een nieuwe tweet wordt geplaatst", die de inhoud van de tweet doorgeeft in de workflow van uw logische app.

Nadat een trigger is geactiveerd, maakt Azure Logic Apps een instantie van uw logische app en begint het uitvoeren van de *acties* in de werkstroom van uw logische app. Acties zijn de stappen die de trigger volgen en taken uitvoeren in de werkstroom van uw logische app. U bijvoorbeeld een logische app maken die klantgegevens uit een SQL-database haalt en die gegevens in latere acties verwerkt.

Dit zijn de algemene soorten triggers die Azure Logic Apps biedt:

* *Recidieftrigger:* deze trigger wordt uitgevoerd volgens een opgegeven schema en is niet nauw gekoppeld aan een bepaalde service of systeem.

* *Polling trigger*: Deze trigger peilt regelmatig een specifieke service of systeem op basis van het opgegeven schema, controleert op nieuwe gegevens of dat er een specifieke gebeurtenis is gebeurd. Als er nieuwe gegevens beschikbaar zijn of als de specifieke gebeurtenis is gebeurd, wordt een nieuw exemplaar van uw logische app gemaakt en uitgevoerd, waarmee u nu de gegevens kan gebruiken die als invoer worden doorgegeven.

* *Trigger:* Deze trigger wacht en luistert naar nieuwe gegevens of dat er een gebeurtenis plaatsvindt. Wanneer er nieuwe gegevens beschikbaar zijn of wanneer de gebeurtenis plaatsvindt, wordt de trigger gemaakt en wordt een nieuw exemplaar van uw logische app uitgevoerd, die nu de gegevens kan gebruiken die als invoer worden doorgegeven.

<a name="connections"></a>

## <a name="connector-configuration"></a>Connectorconfiguratie

De triggers en acties van elke connector bieden hun eigen eigenschappen die u configureren. Veel connectors vereisen ook dat u eerst een *verbinding maakt met* de doelservice of het doelsysteem en verificatiereferenties of andere configuratiegegevens verstrekt voordat u een trigger of actie in uw logische app gebruiken. U moet bijvoorbeeld een verbinding met een Twitter-account autoriseren voor toegang tot gegevens of om namens u berichten te plaatsen.

Voor connectors die Azure Active Directory (Azure AD) OAuth gebruiken, betekent het maken van een verbinding aanmelden bij de service, zoals Office 365, Salesforce of GitHub, waarbij uw toegangstoken is [versleuteld](../security/fundamentals/encryption-overview.md) en veilig is opgeslagen in een geheim Azure-archief. Andere connectors, zoals FTP en SQL, vereisen een verbinding met configuratiegegevens, zoals het serveradres, de gebruikersnaam en het wachtwoord. Deze verbindingsconfiguratiegegevens worden ook versleuteld en veilig opgeslagen. Meer informatie over [versleuteling in Azure](../security/fundamentals/encryption-overview.md).

Verbindingen hebben toegang tot de doelservice of het doelsysteem zolang die service of systeem dit toestaat. Voor services die Azure AD OAuth-verbindingen gebruiken, zoals Office 365 en Dynamics, vernieuwt Azure Logic Apps toegangstokens voor onbepaalde tijd. Andere services hebben mogelijk beperkingen voor hoe lang Azure Logic Apps een token kunnen gebruiken zonder te vernieuwen. Over het algemeen maken sommige acties alle toegangstokens ongeldig, zoals het wijzigen van uw wachtwoord.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Aangepaste API's en connectoren

Als u API's wilt aanroepen die aangepaste code uitvoeren of niet beschikbaar zijn als connectoren, u het Logic Apps-platform uitbreiden door [aangepaste API-apps te maken.](../logic-apps/logic-apps-create-api-app.md) U ook [aangepaste connectors maken](../logic-apps/custom-connector-overview.md) voor *eventuele* API's op basis van REST of SOAP, waardoor deze API's beschikbaar zijn voor elke logische app in uw Azure-abonnement. Als u aangepaste API-apps of connectors openbaar wilt maken voor iedereen die in Azure kan worden gebruikt, u [connectors voor Microsoft-certificering indienen.](../logic-apps/custom-connector-submit-certification.md)

> [!NOTE]
> Logische apps die u implementeert en uitvoert in een [ise-integratieserviceomgeving,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) hebben rechtstreeks toegang tot bronnen in een virtueel Azure-netwerk. Als u aangepaste connectors hebt waarvoor de on-premises gegevensgateway vereist is, en u deze connectors buiten een ISE hebt gemaakt, kunnen logische apps in een ISE deze connectors ook gebruiken.
>
> Aangepaste connectors die binnen een ISE zijn gemaakt, werken niet met de on-premises gegevensgateway. Deze connectors hebben echter rechtstreeks toegang tot on-premises gegevensbronnen die zijn verbonden met een virtueel Azure-netwerk dat het ISE host. Logische apps in een ISE hebben de gegevensgateway dus waarschijnlijk niet nodig wanneer ze met die bronnen communiceren.
>
> Zie [Verbinding maken met virtuele Azure-netwerken met Azure-netwerken vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)voor meer informatie over het maken van ISE's.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de [volledige connectorlijst](https://docs.microsoft.com/connectors)
* [Een logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Aangepaste connectors maken voor logische apps](https://docs.microsoft.com/connectors/custom-connectors/)
* [Aangepaste API's maken voor logische apps](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Verbinding maken met on-premises gegevensbronnen vanuit logische apps met on-premises gegevensgateway"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Een Azure API Management-serviceinstantie maken voor het beheren en publiceren van uw API's"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Logische apps integreren met App Service API Apps."
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Logische apps integreren met Azure-functies"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Berichten in groepen of als batches verwerken"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Een voorwaarde evalueren en verschillende acties uitvoeren op basis van de vraag of de voorwaarde waar of onwaar is"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Dezelfde acties uitvoeren op elk item in een array"
[http-doc]: ./connectors-native-http.md "HTTP- of HTTPS-eindpunten bellen vanuit uw logische apps"
[http-request-doc]: ./connectors-native-reqres.md "HTTP-aanvragen ontvangen in uw logische apps"
[http-response-doc]: ./connectors-native-reqres.md "Reageren op HTTP-verzoeken van uw logische apps"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Rest-eindpunten bellen vanuit uw logische apps"
[http-webhook-doc]: ./connectors-native-webhook.md "Wachten op specifieke gebeurtenissen van HTTP- of HTTPS-eindpunten"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Logische apps integreren met geneste werkstromen"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Matrices selecteren en filteren met behulp van de queryactie."
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Logische apps uitvoeren op basis van een planning"
[schedule-delay-doc]: ./connectors-native-delay.md "Vertraging bij het uitvoeren van de volgende actie"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Vertraging bij het uitvoeren van de volgende actie"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Logische apps uitvoeren volgens een terugkerend schema"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Logische apps uitvoeren die gegevens in aaneengesloten segmenten moeten verwerken"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Acties ordenen in groepen, die hun eigen status krijgen nadat de acties in groep zijn voltooid"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organiseer acties in aanvragen, waaraan unieke waarden zijn toegewezen. Voer alleen de aanvraag uit waarvan de waarde overeenkomt met het resultaat van een expressie, object of token. Als er geen overeenkomsten bestaan, voert u de standaardaanvraag uit"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Een actief actieve werkstroom voor uw logische app stoppen of annuleren"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Acties herhalen totdat de opgegeven voorwaarde is waar of een bepaalde status is gewijzigd"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Gegevensbewerkingen uitvoeren, zoals arrays filteren of CSV- en HTML-tabellen maken"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Bewerkingen uitvoeren met variabelen, zoals initialiseren, instellen, verhogen, decrement en toevoegen aan tekenreeks- of matrixvariabele"

<!--Managed connector doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Bestanden in uw blobcontainer beheren met Azure Blob Storage-container."
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "Verbinding maken met Azure Cosmos DB, zodat u toegang hebt tot documenten en opgeslagen procedures"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Gebeurtenissen bewaken die zijn gepubliceerd door een gebeurtenisraster, bijvoorbeeld wanneer Azure-resources of resources van derden veranderen"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Maak verbinding met Azure Event Hubs, zodat u gebeurtenissen tussen logische apps en gebeurtenishubs ontvangen en verzenden"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "Maak verbinding met uw Azure Storage-account, zodat u bestanden maken, bijwerken, opvragen en verwijderen"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "Maak verbinding met uw Azure Storage-account, zodat u wachtrijen en berichten maken en beheren"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Berichten verzenden vanuit wachtrijen en onderwerpen van servicebussen en berichten ontvangen van wachtrijen en abonnementen van servicebussen"
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "Maak verbinding met Azure SQL Data Warehouse, zodat u uw gegevens bekijken"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "Maak verbinding met uw Azure Storage-account, zodat u tabellen maken, bijwerken en query's maken en meer"
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "Maak verbinding met uw BizTalk-server, zodat u biztalk-gebaseerde toepassingen naast Azure Logic Apps uitvoeren"
[box-doc]: ./connectors-create-api-box.md "Maak verbinding met Box. Uploaden, downloaden, verwijderen, uw bestanden aanbieden en meer"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Maak verbinding met Dropbox. Uploaden, downloaden, verwijderen, uw bestanden aanbieden en meer"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Maak verbinding met Dynamics CRM Online, zodat u werken met CRM Online-gegevens"
[facebook-doc]: ./connectors-create-api-facebook.md "Maak verbinding met Facebook. Berichten plaatsen op een tijdlijn, een paginafeed krijgen en meer"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Verbinding maken met een on-premises bestandssysteem"
[ftp-doc]: ./connectors-create-api-ftp.md "Verbinding maken met een FTP-/FTPS-server voor FTP-taken, zoals het uploaden, ophalen en verwijderen van bestanden, en meer."
[github-doc]: ./connectors-create-api-github.md "Verbinding maken met GitHub en problemen bijhouden"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Maakt verbinding met Google Agenda en kan agenda beheren"
[google-drive-doc]: ./connectors-create-api-googledrive.md "Verbinding maken met GoogleDrive, zodat u met uw gegevens werken"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Verbinding maken met Google Sheets, zodat u uw bladen wijzigen"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Maakt verbinding met Google Tasks, zodat u uw taken beheren"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Verbinding maken met 3270 apps op IBM mainframes"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Maak verbinding met IBM DB2 in de cloud of on-premises. Een rij bijwerken, een tabel krijgen en meer"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Maak verbinding met Informix in de cloud of on-premises. Een rij lezen, de tabellen weergeven en meer"
[ibm-mq-doc]: ./connectors-create-api-mq.md "On-premises of in Azure verbinding maken met IBM MQ om berichten te verzenden en te ontvangen"
[instagram-doc]: ./connectors-create-api-instagram.md "Maak verbinding met Instagram. Gebeurtenissen activeren of reageren"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Maak verbinding met je MailChimp-account. E-mails beheren en automatiseren"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Verbinding maken met Mandrill voor communicatie"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "Maak verbinding met uw on-premises MySQL-database, zodat u gegevens lezen en schrijven"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Maak verbinding met uw Office 365-account, zodat u e-mails verzenden en ontvangen, uw agenda en contactpersonen beheren en meer"
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "Maak verbinding met uw persoonlijke Microsoft OneDrive, zodat u bestanden uploaden, verwijderen, aanbieden en meer"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Maak verbinding met uw bedrijf Microsoft OneDrive, zodat u uw bestanden uploaden, verwijderen, aanbieden en meer"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Maak verbinding met een Oracle-database, zodat u rijen en meer toevoegen, invoegen, verwijderen"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Maak verbinding met uw Outlook-postvak, zodat u uw e-mail, agenda's, contactpersonen en meer beheren"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "Maak verbinding met uw PostgreSQL-database, zodat u gegevens uit tabellen lezen"
[project-online-doc]: ./connectors-create-api-projectonline.md "Maak verbinding met Microsoft Project Online, zodat u uw projecten, taken, resources en meer beheren"
[rss-doc]: ./connectors-create-api-rss.md "Feeditems publiceren en ophalen, bewerkingen activeren wanneer een nieuw item wordt gepubliceerd in een RSS-feed"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Maak verbinding met je Salesforce-account. Accounts, leads, verkoopkansen en meer beheren"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Verbinding maken met een on-premises SAP-systeem"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Maak verbinding met SendGrid. E-mail verzenden en adressenlijsten beheren"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Maak verbinding met uw SFTP-account via SSH. Bestanden uploaden, downloaden, verwijderen en meer"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Maak verbinding met de on-premises server van SharePoint. Documenten, lijstitems en meer beheren"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Maak verbinding met SharePoint Online. Documenten, lijstitems en meer beheren"
[slack-doc]: ./connectors-create-api-slack.md "Verbinding maken met Slack en berichten plaatsen op Slack-kanalen"
[smtp-doc]: ./connectors-create-api-smtp.md "Verbinding maken met een SMTP-server en e-mail verzenden met bijlagen"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Maakt verbinding met SparkPost voor communicatie"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Maak verbinding met Azure SQL Database of SQL Server. Items in een SQL-databasetabel maken, bijwerken, oppakken en verwijderen"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "Verbinding maken met uw Teradata-database om gegevens uit tabellen te lezen"
[trello-doc]: ./connectors-create-api-trello.md "Maak verbinding met Trello. Beheer uw projecten en organiseer alles met iedereen"
[twilio-doc]: ./connectors-create-api-twilio.md "Maak verbinding met Twilio. Berichten verzenden en ontvangen, beschikbare nummers ontvangen, binnenkomende telefoonnummers beheren en meer"
[twitter-doc]: ./connectors-create-api-twitter.md "Maak verbinding met Twitter. Ontvang tijdlijnen, post tweets en meer"
[yammer-doc]: ./connectors-create-api-yammer.md "Maak verbinding met Yammer. Berichten plaatsen, nieuwe berichten ontvangen en meer"
[youtube-doc]: ./connectors-create-api-youtube.md "Maak verbinding met YouTube. Je video's en kanalen beheren"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Berichten coderen en decoderen die het AS2-protocol gebruiken"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Berichten coderen en decoderen die het EDIFACT-protocol gebruiken"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Berichten decoderen die het EDIFACT-protocol gebruiken"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Berichten coderen die het EDIFACT-protocol gebruiken"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Meer informatie over het platte bestand voor bedrijfsintegratie"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Meer informatie over het platte bestand voor bedrijfsintegratie"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Metagegevens beheren voor integratieaccountartefacten"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "JSON transformeren met Vloeibare sjablonen"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Berichten coderen en decoderen die het X12-protocol gebruiken"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Berichten decoderen die het X12-protocol gebruiken"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Berichten coderen die het X12-protocol gebruiken"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML-berichten transformeren"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML-berichten valideren"

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png
