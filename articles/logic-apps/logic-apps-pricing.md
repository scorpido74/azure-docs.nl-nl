---
title: Prijzen & factureringsmodel
description: Overzicht over hoe het prijs- en factureringsmodel werkt voor Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 795acd67a8d4a9f8b8b7d78799a92134f249cf8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270457"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Prijsmodel voor Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) helpt u bij het maken en uitvoeren van geautomatiseerde integratieworkflows die kunnen worden geschaald in de cloud. In dit artikel wordt beschreven hoe facturering en prijzen werken voor Azure Logic Apps. Zie [Logic Apps-prijzen](https://azure.microsoft.com/pricing/details/logic-apps)voor prijstarieven.

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Prijsmodel voor consumptie

Voor nieuwe logische apps die worden uitgevoerd in de openbare of 'globale' Azure Logic Apps-service, betaalt u alleen voor wat u gebruikt. Deze logische apps maken gebruik van een op verbruik gebaseerd plan en prijsmodel. In uw logica-app is elke stap een actie en met Azure Logic Apps worden alle acties in uw logische app met elkaar verwijderd.

Acties zijn bijvoorbeeld:

* Triggers, dat zijn speciale acties. Alle logische apps vereisen een trigger als eerste stap.
* ['Ingebouwde' of native acties](../connectors/apis-list.md#built-in) zoals HTTP, calls to Azure Functions en API Management, enzovoort
* Oproepen naar [beheerde connectors](../connectors/apis-list.md#managed-connectors) zoals Outlook 365, Dropbox, enzovoort
* Regel stroomstappen, zoals lussen, voorwaardelijke instructies, enzovoort

[Standaardconnectoren](../connectors/apis-list.md#managed-connectors) worden in rekening gebracht tegen de [standaardconnectorprijs.](https://azure.microsoft.com/pricing/details/logic-apps) Algemeen beschikbare [Enterprise-connectors](../connectors/apis-list.md#managed-connectors) worden in rekening gebracht tegen de prijs van de [Enterprise-connector](https://azure.microsoft.com/pricing/details/logic-apps), terwijl openbare preview-Enterprise-connectors worden in rekening gebracht tegen de [standaardconnectorprijs](https://azure.microsoft.com/pricing/details/logic-apps).

Meer informatie over hoe facturering werkt voor [triggers](#triggers) en [acties](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Vast prijsmodel

Een [ *integratieserviceomgeving* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) biedt u een geïsoleerde manier om logische apps te maken en uit te voeren die toegang hebben tot bronnen in een virtueel Azure-netwerk. Voor nieuwe logische apps die in een ISE worden uitgevoerd, betaalt u een [vaste maandelijkse prijs](https://azure.microsoft.com/pricing/details/logic-apps) voor deze mogelijkheden:

* [Ingebouwde](../connectors/apis-list.md#built-in) triggers en acties

  Binnen een ISE geven ingebouwde triggers en acties het **Core-label** weer en worden ze uitgevoerd in dezelfde ISE als uw logische apps.

* [Standaardconnectors](../connectors/apis-list.md#managed-connectors) en [Enterprise-connectors](../connectors/apis-list.md#enterprise-connectors) (zoveel Enterprise-verbindingen als u wilt)

   Standaard- en Enterprise-connectors met het **ISE-label** worden uitgevoerd in dezelfde ISE als uw logische apps. Connectors die het ISE-label niet weergeven, worden uitgevoerd in de wereldwijde Logic Apps-service. Vaste maandelijkse prijzen zijn ook van toepassing op connectors die worden uitgevoerd in de wereldwijde service wanneer u ze gebruikt met logische apps die in een ISE worden uitgevoerd.

* [Integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) gebruik zonder extra kosten, op basis van uw [ISE SKU:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

  * **Premium SKU:** Eén [standaardintegratieaccount](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  * **Ontwikkelaar SKU:** Één [gratis integratieaccount voor laag](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Elke ISE SKU is beperkt tot 5 totale integratieaccounts. Tegen meerprijs u meer integratieaccounts hebben op basis van uw ISE SKU:

  * **Premium SKU:** Nog vier standaardaccounts. Geen gratis of basic-accounts.

  * **Ontwikkelaar SKU:** Maximaal 4 standaardaccounts of maximaal 5 standaardaccounts. Geen basisaccounts.

  Zie [Limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)van Logic Apps voor meer informatie over de limieten voor integratie-account. U later in dit onderwerp meer te weten komen over [integratieaccountlagen en hun prijsmodel.](#integration-accounts)

Als u kiest voor de Premium ISE SKU, heeft de basiseenheid een vaste capaciteit. Als u meer doorvoer nodig hebt, u [meer schaaleenheden toevoegen,](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)tijdens het maken of achteraf. De ontwikkelaar ISE SKU heeft niet de mogelijkheid om meer schaaleenheden toe te voegen. Logische apps die in een ISE worden uitgevoerd, maken geen kosten voor het bewaren van gegevens met zich mee.

Zie [Logic Apps-prijzen](https://azure.microsoft.com/pricing/details/logic-apps)voor prijstarieven.

<a name="connectors"></a>

## <a name="connectors"></a>Connectors

Azure Logic Apps-connectors helpen uw logische app toegang te krijgen tot apps, services en systemen in de cloud of on-premises door [triggers,](#triggers) [acties](#actions)of beide aan te bieden. Connectors worden geclassificeerd als Standaard of Enterprise. Zie Connectors voor Azure [Logic Apps voor](../connectors/apis-list.md)een overzicht over deze connectors. Als er geen vooraf gebouwde connectors beschikbaar zijn voor de REST API's die u wilt gebruiken in uw logische apps, u [aangepaste connectors](https://docs.microsoft.com/connectors/custom-connectors)maken, die slechts wikkels zijn rond die REST API's. Aangepaste connectors worden gefactureerd als standaardconnectoren. In de volgende secties vindt u meer informatie over hoe facturering voor triggers en acties werkt.

<a name="triggers"></a>

## <a name="triggers"></a>Triggers

Triggers zijn speciale acties die een logische app-instantie maken wanneer een specifieke gebeurtenis plaatsvindt. Triggers werken op verschillende manieren, die van invloed zijn op de manier waarop de logische app wordt gemeten. Dit zijn de verschillende soorten triggers die in Azure Logic Apps bestaan:

* **Polling trigger**: Deze trigger controleert voortdurend een eindpunt voor berichten die voldoen aan de criteria voor het maken van een logische app-instantie en het starten van de werkstroom. Zelfs wanneer er geen logische app-instantie wordt gemaakt, meet Logic Apps elk pollingverzoek als een uitvoering. Als u het polling-interval wilt opgeven, stelt u de trigger in via de Logic App Designer.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook trigger**: Deze trigger wacht tot een client een aanvraag naar een specifiek eindpunt stuurt. Elk verzoek dat naar het webhook-eindpunt wordt verzonden, telt als een actie-uitvoering. De trigger Van Aanvraag en HTTP Webhook zijn bijvoorbeeld beide webhook-triggers.

* **Recidieftrigger**: Met deze trigger wordt een logische app-instantie gemaakt op basis van het herhalingsinterval dat u in de trigger hebt ingesteld. U bijvoorbeeld een recidief-trigger instellen die elke drie dagen wordt uitgevoerd of volgens een complexer schema.

<a name="actions"></a>

## <a name="actions"></a>Acties

Azure Logic Apps meter "ingebouwde" acties, zoals HTTP, als native acties. Ingebouwde acties omvatten bijvoorbeeld HTTP-aanroepen, aanroepen van Azure-functies of API-beheer en regelstroomstappen zoals voorwaarden, lussen en switchinstructies. Elke actie heeft zijn eigen actietype. Acties die connectors [aanroepen,](https://docs.microsoft.com/connectors) hebben bijvoorbeeld het type 'ApiConnection'. Deze connectors worden geclassificeerd als Standaard- of Enterprise-connectors, die worden gemeten op basis van hun respectievelijke [prijzen](https://azure.microsoft.com/pricing/details/logic-apps). Enterprise-connectors in *Preview* worden opgeladen als standaardconnectoren.

Azure Logic Apps meet alle geslaagde en mislukte acties als uitvoeringen. Logic Apps meet deze acties echter niet:

* Acties die worden overgeslagen vanwege onvervulde voorwaarden
* Acties die niet worden uitgevoerd omdat de logische app is gestopt voordat deze is afgewerkt

Voor acties die binnen lussen worden uitgevoerd, telt Azure Logic Apps elke actie voor elke cyclus in de lus. Stel dat u een lus 'voor elke' hebt die een lijst verwerkt. Logic Apps meet een actie in die lus door het aantal lijstitems te vermenigvuldigen met het aantal acties in de lus en voegt de actie toe waarmee de lus wordt gestart. Dus, de berekening voor een lijst met 10 items is (10 * 1) + 1, wat resulteert in 11 actie-uitvoeringen.

## <a name="disabled-logic-apps"></a>Uitgeschakelde logische apps

Uitgeschakelde logische apps worden niet in rekening gebracht omdat ze geen nieuwe exemplaren kunnen maken terwijl ze zijn uitgeschakeld. Nadat u een logische app hebt uitgeschakeld, kan het enige tijd duren voordat deze volledig zijn gestopt voordat deze worden uitgevoerd.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integratieaccounts

Een [vast prijsmodel](https://azure.microsoft.com/pricing/details/logic-apps) is van toepassing op [integratieaccounts](logic-apps-enterprise-integration-create-integration-account.md) waar u de [B2B- en EDI-](logic-apps-enterprise-integration-b2b.md) en [XML-verwerkingsfuncties](logic-apps-enterprise-integration-xml.md) in Azure Logic Apps zonder extra kosten verkennen, ontwikkelen en testen. Elk Azure-abonnement kan tot een [specifieke limiet van integratieaccounts](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)hebben. Elk integratieaccount kan tot specifieke limiet van artefacten opslaan, waaronder [handelspartners,](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)overeenkomsten, kaarten, schema's, samenstellingen, certificaten, batchconfiguraties, enzovoort.

Azure Logic Apps biedt gratis, basic- en standaardintegratieaccounts. De laagBasis en Standaard worden ondersteund door de SLA (Logic Apps service-level agreement), terwijl de laag Gratis niet wordt ondersteund door een SLA en beperkingen heeft op doorvoer en gebruik. Behalve voor gratis laagintegratieaccounts u in elke Azure-regio meer dan één integratieaccount hebben. Zie [Logic Apps-prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijstarieven.

Als u een [ *integratieserviceomgeving* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)hebt, [Premium of Ontwikkelaar,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)kan uw ISE 5 totale integratieaccounts hebben. Zie de vorige sectie [Vaste prijzen](#fixed-pricing) in dit onderwerp voor meer informatie over hoe het vaste prijsmodel werkt voor een ISE. Zie [Logic Apps-prijzen](https://azure.microsoft.com/pricing/details/logic-apps)voor prijstarieven.

Als u wilt kiezen tussen een gratis, basic- of standaardintegratieaccount, raadpleegt u deze gebruikscasebeschrijvingen:

* **Gratis:** Voor wanneer u verkennende scenario's wilt proberen, niet productiescenario's

* **Basic:** Voor wanneer u alleen berichtverwerking wilt of wilt optreden als een kleine zakenpartner die een handelspartnerrelatie heeft met een grotere bedrijfsentiteit

* **Standaard:** voor wanneer u complexere B2B-relaties en meer entiteiten hebt die u moet beheren

<a name="data-retention"></a>

## <a name="data-retention"></a>Bewaartijd van gegevens

Behalve logische apps die worden uitgevoerd in een ise-integratieserviceomgeving, worden alle ingangen en uitvoer die zijn opgeslagen in de rungeschiedenis van uw logische app gefactureerd op basis van de [bewaarperiode van](logic-apps-limits-and-config.md#run-duration-retention-limits)een logische app. Logische apps die in een ISE worden uitgevoerd, maken geen kosten voor het bewaren van gegevens met zich mee. Zie [Logic Apps-prijzen](https://azure.microsoft.com/pricing/details/logic-apps)voor prijstarieven.

Als u het opslagverbruik van uw logische app wilt controleren, u het als volgt doen:

* Bekijk het aantal opslageenheden in GB dat uw logische app maandelijks gebruikt.
* Bekijk de grootte s voor de ingangen en uitvoer van een specifieke actie in de rungeschiedenis van uw logische app.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Opslagverbruik logische app-apps weergeven

1. Zoek en open uw logische app in de Azure-portal.

1. Selecteer In het menu van uw logische app onder **Controleren**de optie **Statistieken**.

1. Selecteer in het rechterdeelvenster onder **Grafiektitel**in de **lijst Metriek** de optie **Factureringsgebruik voor uitvoeringen van opslagverbruik**.

   Deze statistiek geeft u het aantal opslagverbruikeenheden in GB per maand dat wordt gefactureerd.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Actie-invoer- en uitvoerformaten weergeven

1. Zoek en open uw logische app in de Azure-portal.

1. Selecteer **Overzicht**in het menu van uw logische app .

1. Selecteer in het rechterdeelvenster onder **Uitvoeringen geschiedenis**de uitvoering met de ingangen en uitgangen die u wilt controleren.

1. Kies **Details uitvoeren**onder Logische **app uitvoeren**.

1. Selecteer in het deelvenster Details van de **logische app uitvoeren** in de actietabel, waarin de status en duur van elke actie worden weergegeven, de actie die u wilt weergeven.

1. Zoek in het actiedeelvenster van de **Logic-app** de groottes voor de ingangen en uitvoer van die actie die respectievelijk worden weergegeven onder **koppeling Ingangen** en **Uitvoerkoppeling**.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure Logic Apps](logic-apps-overview.md)
* [Een logische app maken](quickstart-create-first-logic-app-workflow.md)
