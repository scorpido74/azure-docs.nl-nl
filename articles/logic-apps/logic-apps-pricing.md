---
title: Prijs & facturering-Azure Logic Apps | Microsoft Docs
description: Meer informatie over het werken met prijzen en facturering voor Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 62b96fa8722408aed81e87cb31218140fa4f5da8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099414"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Prijs model voor Azure Logic Apps

[Azure Logic apps](../logic-apps/logic-apps-overview.md) helpt u om geautomatiseerde integratie werk stromen te maken en uit te voeren die in de Cloud kunnen worden geschaald. In dit artikel wordt beschreven hoe facturering en prijzen werken voor Azure Logic Apps. Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps)voor prijs tarieven.

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Prijs model verbruik

Voor nieuwe logische apps die worden uitgevoerd in de open bare of algemene Azure Logic Apps-service, betaalt u alleen voor wat u gebruikt. Deze Logic apps gebruiken een op verbruik gebaseerd abonnement en prijs model. In uw logische app is elke stap een actie en Azure Logic Apps meter alle acties die worden uitgevoerd in uw logische app.

Acties omvatten bijvoorbeeld:

* Triggers, die speciale acties zijn. Voor alle Logic apps is een trigger als de eerste stap vereist.
* [Ingebouwde of systeem eigen acties](../connectors/apis-list.md#built-in) , zoals http, aanroepen naar Azure Functions en API Management, enzovoort
* Aanroepen naar [beheerde connectors](../connectors/apis-list.md##managed-connectors) , zoals Outlook 365, Dropbox, enzovoort
* Stappen voor controle stroom, zoals lussen, voorwaardelijke instructies, enzovoort

[Standaard](../connectors/apis-list.md#managed-connectors) connectors worden in rekening gebracht tegen de [standaard prijs](https://azure.microsoft.com/pricing/details/logic-apps)van de verbinding. Algemeen beschik bare [Enter prise](../connectors/apis-list.md#managed-connectors) -connectors worden in rekening gebracht tegen de [prijs voor Enter prise connector](https://azure.microsoft.com/pricing/details/logic-apps), terwijl open bare preview Enter prise-connectors in rekening worden gebracht tegen de [standaard prijs](https://azure.microsoft.com/pricing/details/logic-apps)voor

Meer informatie over hoe facturering werkt voor [Triggers](#triggers) en [acties](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Model met vaste prijzen

Een [ *Integration service Environment* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) biedt een persoonlijke, geïsoleerde en speciale manier om logische apps te maken en uit te voeren die toegang hebben tot bronnen in een virtueel Azure-netwerk. Voor nieuwe Logic apps die in een ISE worden uitgevoerd, betaalt u een [vaste maandelijkse prijs](https://azure.microsoft.com/pricing/details/logic-apps) voor deze mogelijkheden:

* [Ingebouwde triggers en acties](../connectors/apis-list.md#built-in)

* [Standard-connectors](../connectors/apis-list.md#managed-connectors)

* [Enter prise](../connectors/apis-list.md#enterprise-connectors) -connectors met zoveel verbindingen als u wilt

* Gebruik van het [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) zonder extra kosten, op basis van uw [ISE-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

  * **Premium SKU**: Eén [standaard-laag](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integratie account

  * **Developer SKU**: Eén integratie account voor de [gratis laag](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Elke ISE-SKU is beperkt tot 5 totale integratie accounts. Voor extra kosten kunt u meer integratie accounts hebben op basis van uw ISE-SKU:

  * **Premium SKU**: Maxi maal vier standaard accounts. Geen vrije of basis accounts.

  * **Developer SKU**: Maxi maal vier standaard accounts of Maxi maal vijf standaard accounts. Geen basis accounts.

Zie [Logic apps limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)voor meer informatie over de limieten van het integratie account. Verderop in dit onderwerp vindt u meer informatie over [de integratie-account lagen en hun prijs model](#integration-accounts) .

Voor de Premium ISE SKU heeft de basis eenheid vaste capaciteit, dus als u meer door voer wilt, kunt u tijdens het maken of later [meer schaal eenheden toevoegen](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity). De Developer ISE SKU beschikt niet over de mogelijkheid om meer schaal eenheden toe te voegen. Logic apps die in een ISE worden uitgevoerd, zijn geen kosten voor het bewaren van gegevens.

> [!NOTE]
> In een ISE worden met ingebouwde triggers en acties het **kern** label weer gegeven en uitgevoerd in dezelfde ISE als uw logische apps. Standard-en Enter prise-connectors die het label **ISE** weer geven, worden uitgevoerd in dezelfde ISE als uw logische apps. Connectors die het label ISE niet weer geven, worden uitgevoerd in de service globale Logic Apps.

Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps)voor prijs tarieven.

<a name="connectors"></a>

## <a name="connectors"></a>Connectors

Azure Logic Apps-connectors helpen uw logische app toegang te bieden tot apps, services en systemen in de Cloud of on-premises met behulp van [Triggers](#triggers), [acties](#actions)of beide. Connectors worden geclassificeerd als Standard of ENTER prise. Zie [connectors for Azure Logic apps](../connectors/apis-list.md)voor een overzicht van deze connectors. Als er geen vooraf gemaakte connectors beschikbaar zijn voor de REST-Api's die u wilt gebruiken in uw Logic apps, kunt u [aangepaste connectors](https://docs.microsoft.com/connectors/custom-connectors)maken. Dit zijn alleen wrappers rond die rest-api's. Aangepaste connectors worden gefactureerd als standaard connectors. De volgende secties bevatten meer informatie over hoe de facturering voor triggers en acties werkt.

<a name="triggers"></a>

## <a name="triggers"></a>Triggers

Triggers zijn speciale acties waarmee een logische app-instantie wordt gemaakt wanneer er een specifieke gebeurtenis plaatsvindt. Triggers handelen op verschillende manieren, wat van invloed is op hoe de logische app wordt gemeten. Hier volgen de verschillende soorten triggers die in Azure Logic Apps bestaan:

* **Polling trigger**: Deze trigger controleert voortdurend een eind punt op berichten die voldoen aan de criteria voor het maken van een logische app-instantie en het starten van de werk stroom. Zelfs wanneer er geen exemplaar van een logische app wordt gemaakt, Logic Apps meter elke polling-aanvraag als een uitvoering. Als u het polling-interval wilt opgeven, stelt u de trigger in via de ontwerp functie voor logische apps.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook-trigger**: Deze trigger wacht totdat een client een aanvraag naar een bepaald eind punt verzendt. Elke aanvraag die wordt verzonden naar het eind punt van de webhook, telt als een actie-uitvoering. De trigger Request en HTTP-webhook zijn bijvoorbeeld beide webhook-triggers.

* **Trigger voor terugkeer patroon**: Deze trigger maakt een exemplaar van een logische app op basis van het terugkeer interval dat u in de trigger hebt ingesteld. U kunt bijvoorbeeld een terugkeer patroon trigger instellen die elke drie dagen of op een complexere planning wordt uitgevoerd.

<a name="actions"></a>

## <a name="actions"></a>Acties

Azure Logic Apps meters ' ingebouwde ' acties, zoals HTTP, als systeem eigen acties. Ingebouwde acties omvatten bijvoorbeeld HTTP-aanroepen, aanroepen van Azure Functions of API Management, en controle stroom stappen zoals voor waarden, lussen en switch instructies. Elke actie heeft een eigen actie type. Acties die connectors aanroepen [](https://docs.microsoft.com/connectors) , hebben bijvoorbeeld het type ' ApiConnection '. Deze connectors worden geclassificeerd als Standard-of ENTER prise-connectors, die worden gemeten op basis van hun respectieve [prijzen](https://azure.microsoft.com/pricing/details/logic-apps). Enter prise-connectors in *Preview* worden in rekening gebracht als standaard connectors.

Azure Logic Apps meter alle geslaagde en mislukte acties als uitvoeringen. Logic Apps deze acties echter niet meten:

* Acties die worden overgeslagen vanwege unmet-voor waarden
* Acties die niet worden uitgevoerd omdat de logische app is gestopt voordat deze wordt voltooid

Voor acties die in lussen worden uitgevoerd, telt Azure Logic Apps elke actie voor elke cyclus in de lus. Stel dat u een lus ' voor elke ' hebt waarmee een lijst wordt verwerkt. Logic Apps meter een actie in die lus door het aantal lijst items te vermenigvuldigen met het aantal acties in de lus en de actie toe te voegen waarmee de lus wordt gestart. De berekening voor een lijst met tien items is dus (10 * 1) + 1, wat resulteert in elf actie-uitvoeringen.

## <a name="disabled-logic-apps"></a>Uitgeschakelde Logic apps

Uitgeschakelde Logic apps worden niet in rekening gebracht omdat ze geen nieuwe instanties kunnen maken wanneer ze zijn uitgeschakeld. Nadat u een logische app hebt uitgeschakeld, kan het enige tijd duren voordat de actieve instanties worden gestopt.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integratieaccounts

Een [vast prijs model](https://azure.microsoft.com/pricing/details/logic-apps) is van toepassing op [integratie accounts](logic-apps-enterprise-integration-create-integration-account.md) waar u de [B2B-en EDI](logic-apps-enterprise-integration-b2b.md) [-en XML-](logic-apps-enterprise-integration-xml.md) verwerkings functies in azure Logic apps zonder extra kosten kunt verkennen, ontwikkelen en testen. Elk Azure-abonnement kan Maxi maal een [specifieke limiet van integratie accounts](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)hebben. Elk integratie account kan tot een specifieke [limiet van artefacten](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)worden opgeslagen, waaronder handels partners, overeenkomsten, kaarten, schema's, assembly's, certificaten, batch configuraties, enzovoort.

Azure Logic Apps biedt gratis, Basic-en Standard-integratie accounts. De lagen basis en standaard worden ondersteund door de Logic Apps Service Level Agreement (SLA), terwijl de laag gratis niet wordt ondersteund door een SLA en limieten heeft voor door Voer en gebruik. Met uitzonde ring van de integratie accounts voor de gratis laag kunt u in elke Azure-regio meer dan één integratie account hebben. Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijs tarieven.

Als u een [ *Integration service Environment* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), [Premium of Developer](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)hebt, kan uw ISE 5 totale integratie accounts hebben. Zie de sectie vorig [vast prijs model](#fixed-pricing) in dit onderwerp voor meer informatie over hoe het vaste prijs model werkt voor een ISE. Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps)voor prijs tarieven.

Als u wilt kiezen tussen een gratis, basis of standaard integratie account, raadpleegt u deze use-case-beschrijvingen:

* **Gratis**: Als u wilt proberen om verkennende scenario's, niet productie scenario's

* **Basis**: Als u alleen bericht verwerking wilt of als een kleine zakelijke partner met een relatie van een handels partner met een grotere bedrijfs entiteit

* **Standaard**: Als u complexere B2B-relaties hebt en een groter aantal entiteiten dat u moet beheren

<a name="data-retention"></a>

## <a name="data-retention"></a>Bewaartijd van gegevens

Met uitzonde ring van Logic apps die worden uitgevoerd in een Integration service Environment (ISE), worden alle invoer en uitvoer die zijn opgeslagen in de uitvoerings geschiedenis van de logische app, gefactureerd op basis van de [uitvoerings periode](logic-apps-limits-and-config.md#run-duration-retention-limits)van een logische app. Logic apps die in een ISE worden uitgevoerd, zijn geen kosten voor het bewaren van gegevens. Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps)voor prijs tarieven.

Om u te helpen bij het bewaken van het opslag verbruik van uw logische app, kunt u het volgende doen:

* Bekijk het aantal opslag eenheden in GB dat maandelijks door uw logische app wordt gebruikt.
* Bekijk de grootten voor de invoer en uitvoer van een specifieke actie in de uitvoerings geschiedenis van de logische app.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Gebruik van logische app-opslag weer geven

1. Zoek en open uw logische app in de Azure Portal.

1. Selecteer in het menu van de logische app, onder **bewaking**, **metrische gegevens**.

1. Selecteer in het rechterdeel venster onder **grafiek titel**in de lijst **metriek** het **facturerings gebruik voor uitvoeringen van opslag verbruik**.

   Met deze metriek krijgt u het aantal verbruiks eenheden voor opslag in GB per maand dat wordt gefactureerd.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>De invoer-en uitvoer grootte van de actie weer geven

1. Zoek en open uw logische app in de Azure Portal.

1. Selecteer **overzicht**in het menu van de logische app.

1. Selecteer in het rechterdeel venster onder **uitvoerings geschiedenis**de uitvoering met de invoer en uitvoer die u wilt controleren.

1. Kies onder **uitvoering van logische app**de optie **Details uitvoeren**.

1. Selecteer de actie die u wilt weer geven in het detail venster van de **logische app-uitvoering** in de tabel acties, waarin de status en duur van elke actie worden vermeld.

1. In het actie deel venster van de **logische app** vindt u de grootten voor de invoer van die actie en de uitvoer die respectievelijk worden weer gegeven onder de koppeling **invoer** en **uitvoer**.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure Logic Apps](logic-apps-overview.md)
* [Een logische app maken](quickstart-create-first-logic-app-workflow.md)
