---
title: Apps migreren van BizTalk Services naar Azure Logic Apps
description: Uw apps en oplossingen verplaatsen van Microsoft Azure BizTalk Services (MABS) naar Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: 97399635399c12022006ac95e60c5828bf2a9dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905431"
---
# <a name="migrate-your-apps-and-solutions-from-biztalk-services-to-azure-logic-apps"></a>Migreer uw apps en oplossingen van BizTalk Services naar Azure Logic Apps

Microsoft Azure BizTalk Services (MABS) gaat met pensioen. Als u uw MABS-integratieoplossingen wilt verplaatsen naar [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)volgt u de richtlijnen in dit artikel. 

## <a name="introduction"></a>Inleiding

BizTalk Services bestaat uit twee subdiensten:

* Hybride verbindingen van Microsoft BizTalk Services
* EAI en EDI bridge-based integratie

[Hybride verbindingen van Azure App Service](../app-service/app-service-hybrid-connections.md) vervangt hybride verbindingen van BizTalk Services. Azure Hybrid Connections is beschikbaar met Azure App Service via de Azure-portal. Deze service biedt een Hybrid Connection Manager, zodat u bestaande hybride verbindingen van BizTalk Services en ook nieuwe hybride verbindingen beheren die u in de portal maakt. 

[Logic Apps](../logic-apps/logic-apps-overview.md) vervangt EAI- en EDI-bridge-gebaseerde integratie met dezelfde mogelijkheden in BizTalk-services en meer. Deze service biedt op cloudschaal gebaseerde workflow- en orchestration-functies waarmee u snel en eenvoudig complexe integratieoplossingen bouwen via een browser of met Visual Studio.

In deze tabel worden de mogelijkheden van BizTalk Services toegewezen aan Logic Apps.

| BizTalk-services   | Logic Apps            | Doel                      |
| ------------------ | --------------------- | ---------------------------- |
| Connector          | Connector             | Gegevens verzenden en ontvangen   |
| Bridge             | Logische apps             | Pijplijnprocessor           |
| Fase valideren     | XML-validatie, actie | Een XML-document valideren op basis van een schema | 
| Podium verrijken       | Gegevenstokens           | Eigenschappen promoten in berichten of voor routeringsbeslissingen |
| Transformatiefase    | Actie transformeren      | XML-berichten van de ene indeling naar de andere converteren |
| Decodefase       | Decode van platte bestanden | Converteren van plat bestand naar XML |
| Fase coderen       | Plat bestand coderen actie | Converteren van XML naar vlak bestand |
| Berichtcontrole  | Azure-functies of API-apps | Aangepaste code uitvoeren in uw integraties |
| Routeactie       | Conditie of switch | Berichten doorsturen naar een van de opgegeven connectors |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk Services-artefacten

BizTalk Services heeft verschillende soorten artefacten. 

## <a name="connectors"></a>Connectors

BizTalk Services-connectors helpen bruggen gegevens te verzenden en te ontvangen, waaronder tweerichtingsbruggen die http-gebaseerde vraag-/antwoordinteracties mogelijk maken. Logic Apps gebruikt dezelfde terminologie en heeft honderden connectoren die hetzelfde doel dienen door verbinding te maken met een breed scala aan technologieën en services. Connectors zijn bijvoorbeeld beschikbaar voor cloud SaaS- en PaaS-services, zoals OneDrive, Office365, Dynamics CRM en meer, plus on-premises systemen via de On-Premises Data Gateway, die de BizTalk-adapterservice vervangt voor BizTalk-services. Bronnen in BizTalk-services zijn beperkt tot FTP-, SFTP- en ServiceBus-wachtrij- of onderwerpabonnement.

![](media/logic-apps-move-from-mabs/sources.png)

Standaard heeft elke brug een HTTP-eindpunt, dat is geconfigureerd met het runtimeadres en de eigenschappen Relatief adres voor de brug. Als u dezelfde resultaten wilt bereiken met Logic Apps, gebruikt u de acties [Opverzoek en Antwoord.](../connectors/connectors-native-reqres.md)

## <a name="xml-processing-and-bridges"></a>XML-verwerking en bruggen

In BizTalk Services is een brug analoog aan een verwerkingspijplijn. Een brug kan gegevens die van een connector zijn ontvangen, wat werk met de gegevens doen en de resultaten naar een ander systeem verzenden. Logic Apps doet hetzelfde door dezelfde op pijplijngebaseerde interactiepatronen te ondersteunen als BizTalk Services en ook andere integratiepatronen te bieden. De [XML Request-Reply Bridge](https://msdn.microsoft.com/library/azure/hh689781.aspx) in BizTalk Services staat bekend als een VETER-pijplijn, die bestaat uit fasen die deze taken uitvoeren:

* (V) Valideren
* (E) Verrijken
* (T) Transformeren
* (E) Verrijken
* (R) Route

Deze afbeelding laat zien hoe de verwerking wordt verdeeld tussen aanvraag en antwoord, die controle biedt over de aanvraag en de antwoordpaden afzonderlijk, bijvoorbeeld door verschillende kaarten voor elk pad te gebruiken:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Ook voegt een XML One-Way bridge decode- en Code-fasen toe aan het begin en einde van de verwerking. De Pass-Through bridge bevat één Enrich-fase.

### <a name="message-processing-decoding-and-encoding"></a>Berichtverwerking, decodering en codering

In BizTalk Services u verschillende typen XML-berichten ontvangen en het overeenkomende schema voor het ontvangen bericht bepalen. Dit werk wordt uitgevoerd in de fase *Berichttypen* van de pijplijn voor ontvangen verwerking. In de fase Decoderen wordt vervolgens het gedetecteerde berichttype gebruikt om het bericht te decoderen met behulp van het opgegeven schema. Als het schema een vlak bestandsschema is, wordt het binnenkomende platte bestand in deze fase geconverteerd naar XML. 

Logic Apps biedt vergelijkbare mogelijkheden. U ontvangt een plat bestand over verschillende protocollen met behulp van de verschillende connectortriggers (Bestandssysteem, FTP, HTTP, enzovoort) en gebruikt de actie [Flat File Decode](../logic-apps/logic-apps-enterprise-integration-flatfile.md) om de binnenkomende gegevens om te zetten in XML. U uw bestaande schema's met platte bestanden zonder wijzigingen rechtstreeks naar Logic Apps verplaatsen en vervolgens schema's uploaden naar uw integratieaccount.

### <a name="validation"></a>Validatie

Nadat de binnenkomende gegevens zijn geconverteerd naar XML (of als XML de ontvangen berichtindeling was), wordt de validatie uitgevoerd om te bepalen of het bericht aan uw XSD-schema voldoet. Als u deze taak wilt uitvoeren in Logische apps, gebruikt u de actie [XML-validatie.](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) U dezelfde schema's van BizTalk Services gebruiken zonder wijzigingen.

### <a name="transform-messages"></a>Berichten transformeren

In BizTalk Services wordt in de fase Transform de ene op XML gebaseerde berichtindeling geconverteerd naar de andere. Dit werk wordt gedaan door het toepassen van een kaart, met behulp van de TRFM-gebaseerde mapper. In Logic Apps is het proces vergelijkbaar. Met de actie Transformeren wordt een kaart uitgevoerd vanuit uw integratieaccount. Het belangrijkste verschil is dat kaarten in Logic Apps in XSLT-indeling zijn. XSLT bevat de mogelijkheid om bestaande XSLT die u al hebt opnieuw te gebruiken, inclusief kaarten die zijn gemaakt voor BizTalk Server die functoids bevatten. 

### <a name="routing-rules"></a>Regels voor doorsturen

BizTalk Services maakt een routeringsbeslissing over welk eindpunt of connector u inkomende berichten of gegevens wilt verzenden. De mogelijkheid om te kiezen uit vooraf geconfigureerde eindpunten is mogelijk met de optie routeringsfilter:

![](media/logic-apps-move-from-mabs/route-filter.png)

In BizTalk Services, als er slechts twee opties, met behulp van een *voorwaarde* is de beste manier voor het omzetten van routing filters in BizTalk Services. Als er meer dan twee zijn, gebruik dan een **schakelaar**.

Logic Apps biedt geavanceerde logische mogelijkheden plus geavanceerde besturingsstroom en routering met [voorwaardelijke instructies](../logic-apps/logic-apps-control-flow-conditional-statement.md) en [switchstatements.](../logic-apps/logic-apps-control-flow-switch-statement.md)

### <a name="enrich"></a>Verrijken

In de verwerking van BizTalk Services voegt de fase Verrijken eigenschappen toe aan de berichtcontext die is gekoppeld aan de ontvangen gegevens. Bijvoorbeeld het promoten van een eigenschap die kan worden gebruikt voor routering vanuit een databaseopzoeken of door een waarde te extraheren met behulp van een XPath-expressie. Logic Apps biedt toegang tot alle contextuele gegevensuitvoer van eerdere acties, waardoor het eenvoudig is om hetzelfde gedrag te repliceren. Met de `Get Row` SQL-verbindingsactie retourneert u bijvoorbeeld gegevens uit een SQL Server-database en gebruikt u de gegevens in een beslissingsactie voor routering. Evenzo zijn eigenschappen op binnenkomende servicebus berichten in de wachtrij door een trigger adresseerbaar, evenals XPath met behulp van de taalexpressie van de xpath-werkstroomdefinitie.

### <a name="run-custom-code"></a>Aangepaste code uitvoeren

Met BizTalk Services u [aangepaste code uitvoeren](https://msdn.microsoft.com/library/azure/dn232389.aspx) die in uw eigen samenstellingen wordt geüpload. Deze functionaliteit wordt geïmplementeerd door de [IMessageInspector-interface.](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) Elke fase in de brug bevat twee eigenschappen (Enter Inspector en Op Exit Inspector) die het .NET-type bieden dat u hebt gemaakt en dat deze interface implementeert. Met aangepaste code u complexere verwerkingen uitvoeren op gegevens en u bestaande code hergebruiken in verzamelingen die algemene bedrijfslogica uitvoeren. 

Logic Apps biedt twee primaire manieren om aangepaste code uit te voeren: Azure-functies en API-apps. Azure-functies kunnen worden gemaakt en aanroepen vanuit logische apps. Zie [Aangepaste code voor logische apps toevoegen en uitvoeren via Azure-functies](../logic-apps/logic-apps-azure-functions.md). Gebruik API Apps, onderdeel van Azure App Service, om uw eigen triggers en acties te maken. Meer informatie over [het maken van een aangepaste API die u gebruiken met Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Als u aangepaste code hebt in verzamelingen die u belt vanuit BizTalk Services, u deze code verplaatsen naar Azure-functies of aangepaste API's maken met API-apps, afhankelijk van wat u implementeert. Als u bijvoorbeeld code hebt die een andere service omsluit waarvoor Logic Apps geen connector heeft, maakt u een API-app en gebruikt u de acties die uw API-app biedt in uw logische app. Als u helperfuncties of -bibliotheken hebt, past Azure Functions waarschijnlijk het beste.

### <a name="edi-processing-and-trading-partner-management"></a>EDI-verwerking en partnerbeheer

BizTalk Services en Logic Apps omvatten EDI- en B2B-verwerking met ondersteuning voor AS2 (Applicability Statement 2), X12 en EDIFACT. In BizTalk Services maakt u EDI-bruggen en creëert of beheert u handelspartners en -overeenkomsten in de speciale tracking- en beheerportal.
In Logic Apps krijgt u deze functionaliteit via het [Enterprise Integration Pack (EIP).](../logic-apps/logic-apps-enterprise-integration-overview.md) Het EIP biedt [Integratieaccount-](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) en B2B-acties voor EDI- en B2B-verwerking. U gebruikt ook een integratieaccount om [handelspartners](../logic-apps/logic-apps-enterprise-integration-partners.md) en -overeenkomsten te maken en te [beheren.](../logic-apps/logic-apps-enterprise-integration-agreements.md) Nadat u een integratieaccount hebt gemaakt, u een of meer logische apps aan het account koppelen. U vervolgens B2B-acties gebruiken om toegang te krijgen tot informatie over handelspartners vanuit uw logische app. De volgende acties worden aangeboden:

* AS2 Coderen
* AS2 decoderen
* X12 Coderen
* X12 Decoderen
* EDIFACT Coderen
* EDIFACT Decoderen

In tegenstelling tot BizTalk Services worden deze acties losgekoppeld van de transportprotocollen. Dus wanneer u uw logische apps maakt, hebt u meer flexibiliteit in welke connectors u gebruikt om gegevens te verzenden en te ontvangen. U bijvoorbeeld X12-bestanden ontvangen als bijlagen via e-mail en deze bestanden vervolgens verwerken in een logische app. 

## <a name="manage-and-monitor"></a>Beheren en bewaken

In BizTalk Services biedt een speciale portal trackingmogelijkheden om problemen te monitoren en op te lossen. Logic Apps biedt uitgebreidere tracking- en monitoringmogelijkheden om [logische apps in de Azure-portal](../logic-apps/monitor-logic-apps.md)te controleren en bevat een mobiele app om onderweg een oogje in het zeil te houden.

## <a name="high-availability"></a>Hoge beschikbaarheid

Voor hoge beschikbaarheid (HA) in BizTalk Services u de verwerkingsbelasting delen door meer dan één instantie in een bepaalde regio te gebruiken. Logic Apps biedt in-region HA zonder extra kosten. 

In BizTalk Services vereist disaster recovery buiten de regio voor B2B-verwerking een back-up- en herstelproces. Voor bedrijfscontinuïteit biedt Logic Apps actieve/passieve [DR-mogelijkheden,](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)waarmee u B2B-gegevens synchroniseren tussen integratieaccounts in verschillende regio's.

## <a name="next-steps"></a>Volgende stappen

* [Wat is Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md), of snel aan de slag met behulp van een [vooraf gemaakte sjabloon](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Alle beschikbare connectors weergeven](../connectors/apis-list.md) die u gebruiken in logische apps