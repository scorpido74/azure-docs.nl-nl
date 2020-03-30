---
title: Verbinding maken met Azure-gebeurtenishubs
description: Geautomatiseerde taken en werkstromen maken die gebeurtenissen bewaken en beheren met Azure Event Hubs en Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 32fa54ef0d8eccaf8745ee37cb028d4f3c6d73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247291"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Gebeurtenissen bewaken, ontvangen en verzenden met Azure Event Hubs en Azure Logic Apps

In dit artikel ziet u hoe u gebeurtenissen controleren en beheren die vanuit een logische app naar [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) worden verzonden met de Azure Event Hubs-connector. Op die manier kunt u logische apps maken waarmee taken en werkstromen worden geautomatiseerd voor het controleren, verzenden en ontvangen van gebeurtenissen vanuit uw Event Hub. Zie de</a> [naslagverwijzing naar azure event hubs-connector](https://docs.microsoft.com/connectors/eventhubs/)voor technische gegevens met een connector .

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Een [naamruimte voor Azure Event Hubs en gebeurtenishub](../event-hubs/event-hubs-create.md)

* De logische app waar u toegang wilt krijgen tot uw gebeurtenishub. Als u uw logische app wilt starten met een Trigger voor Azure Event Hubs, hebt u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)nodig.
Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps](../logic-apps/logic-apps-overview.md) en [Quickstart: Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Machtigingen controleren en verbindingstekenreeks en verbindingstekenreeks

Als u wilt controleren of uw logica-app toegang heeft tot uw gebeurtenishub, controleert u uw machtigingen en krijgt u de verbindingstekenreeks voor de naamruimte van uw gebeurtenishubs.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Ga naar de *naamruimte*van je Event Hubs, geen specifieke gebeurtenishub. 

1. Selecteer in het menu naamruimte onder **Instellingen**de optie **Beleid voor gedeelde toegang**. Controleer **onder Claims**of u machtigingen voor **beheren** voor die naamruimte hebt.

   ![Machtigingen beheren voor de naamruimte van uw gebeurtenishub](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Als u later handmatig uw verbindingsgegevens wilt invoeren, krijgt u de verbindingstekenreeks voor de naamruimte van uw gebeurtenishubs.

   1. Kies **onder Beleid**de optie **RootManageSharedAccessKey**.

   1. Zoek de verbindingstekenreeks van uw primaire sleutel. Kies de kopieerknop en sla de verbindingstekenreeks op voor later gebruik.

      ![Verbindingstekenreeks gebeurtenishubs kopiëren](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Als u wilt controleren of uw verbindingstekenreeks is gekoppeld aan de naamruimte van uw gebeurtenishubs of aan een specifieke gebeurtenishub, controleert u of de verbindingstekenreeks niet over de `EntityPath`  parameter beschikt. Als u deze parameter vindt, is de verbindingstekenreeks voor een specifieke gebeurtenishub 'entiteit' en is deze niet de juiste tekenreeks die u met uw logische app gebruiken.

1. Ga nu verder met [Een trigger voor gebeurtenishubs toevoegen](#add-trigger) of een actie [Gebeurtenishubs toevoegen](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Trigger gebeurtenishubs toevoegen

In Azure Logic Apps moet elke logische app beginnen met een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), die wordt geactiveerd wanneer een specifieke gebeurtenis plaatsvindt of wanneer aan een specifieke voorwaarde is voldaan. Elke keer dat de trigger wordt geactiveerd, maakt de Logic Apps-engine een logische app-instantie en wordt de werkstroom van uw app gestart.

In dit voorbeeld ziet u hoe u een werkstroom voor logische apps starten wanneer nieuwe gebeurtenissen naar uw gebeurtenishub worden verzonden. 

1. Maak in de Azure-portal of Visual Studio een lege logische app, waarmee Logic Apps Designer wordt geopend. In dit voorbeeld wordt de Azure-portal gebruikt.

1. Voer in het zoekvak 'gebeurtenishubs' in als filter. Selecteer deze trigger in de lijst triggers: **Wanneer gebeurtenissen beschikbaar zijn in Event Hub - Event Hubs**

   ![Trigger selecteren](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Als u wordt gevraagd om verbindingsgegevens, [maakt u nu uw verbinding met gebeurtenishubs](#create-connection). 

1. Geef in de trigger informatie over de gebeurtenishub die u wilt controleren. Open de lijst **Nieuwe parameter toevoegen voor** meer eigenschappen. Als u een parameter selecteert, voegt u die eigenschap toe aan de triggerkaart.

   ![Trigger-eigenschappen](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam gebeurtenishub** | Ja | De naam voor de gebeurtenishub die u wilt controleren |
   | **Inhoudstype** | Nee | Het inhoudstype van de gebeurtenis. De standaardwaarde is `application/octet-stream`. |
   | **Naam van consumentengroep** | Nee | De [naam voor de Event Hub-consumentengroep](../event-hubs/event-hubs-features.md#consumer-groups) die u gebruiken voor het lezen van evenementen. Als dit niet is opgegeven, wordt de standaardconsumentengroep gebruikt. |
   | **Maximaal aantal gebeurtenissen** | Nee | Het maximum aantal evenementen. De trigger wordt geretourneerd tussen één en het aantal gebeurtenissen dat door deze eigenschap is opgegeven. |
   | **Interval** | Ja | Een positief geheel getal dat beschrijft hoe vaak de werkstroom wordt uitgevoerd op basis van de frequentie |
   | **Frequentie** | Ja | De tijdseenheid voor de herhaling |
   ||||

   **Aanvullende eigenschappen**

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Inhoudsschema** | Nee | Het JSON-inhoudsschema voor de gebeurtenissen die moeten worden gelezen vanuit de gebeurtenishub. Als u bijvoorbeeld het inhoudsschema opgeeft, u de logische app activeren voor alleen die gebeurtenissen die overeenkomen met het schema. |
   | **Minimale partitiesleutel** | Nee | Voer de minimale [partitie-id](../event-hubs/event-hubs-features.md#partitions) in die u wilt lezen. Standaard worden alle partities gelezen. |
   | **Maximale partitiesleutel** | Nee | Voer de maximale [partitie-id](../event-hubs/event-hubs-features.md#partitions) in die u wilt lezen. Standaard worden alle partities gelezen. |
   | **Tijdzone** | Nee | Geldt alleen wanneer u een begintijd opgeeft omdat deze trigger geen UTC-verschuiving accepteert. Selecteer de tijdzone die u wilt toepassen. <p>Zie [Terugkerende taken en werkstromen maken en uitvoeren met Azure Logic Apps](../connectors/connectors-native-recurrence.md)voor meer informatie. |
   | **Begintijd** | Nee | Geef een begintijd op in deze indeling: <p>YYYY-MM-DDThh:mm:ss als u een tijdzone selecteert<p>-of-<p>YYYY-MM-DDThh:mm:ssZ als u geen tijdzone selecteert<p>Zie [Terugkerende taken en werkstromen maken en uitvoeren met Azure Logic Apps](../connectors/connectors-native-recurrence.md)voor meer informatie. |
   ||||

1. Wanneer u klaar bent, kiest u op de werkbalk van de ontwerper de optie **Opslaan**.

1. Ga nu verder met het toevoegen van een of meer acties aan uw logische app voor de taken die u wilt uitvoeren met de triggerresultaten. 

   Als u bijvoorbeeld gebeurtenissen wilt filteren op basis van een specifieke waarde, zoals een categorie, u een voorwaarde toevoegen zodat de actie **Gebeurtenis verzenden** alleen de gebeurtenissen verzendt die aan uw voorwaarde voldoen. 

> [!NOTE]
> Alle Gebeurtenishub-triggers zijn *triggers voor lange polling,* wat betekent dat wanneer een trigger wordt geactiveerd, de trigger alle gebeurtenissen verwerkt en vervolgens 30 seconden wacht tot er meer gebeurtenissen worden weergegeven in uw Gebeurtenishub.
> Als er binnen 30 seconden geen gebeurtenissen worden ontvangen, wordt de triggerrun overgeslagen. Anders blijft de trigger gebeurtenissen lezen totdat de gebeurtenishub leeg is.
> De volgende triggerpoll gebeurt op basis van het herhalingsinterval dat u opgeeft in de eigenschappen van de trigger.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Actie Gebeurtenishubs toevoegen

In Azure Logic Apps is een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) een stap in uw werkstroom die een trigger of een andere actie volgt. In dit voorbeeld begint de logische app met een trigger voor gebeurtenishubs die controleert op nieuwe gebeurtenissen in uw gebeurtenishub.

1. Open uw logische app in de Azure-portal of Visual Studio in Logic Apps Designer. In dit voorbeeld wordt de Azure-portal gebruikt.

1. Kies Onder de trigger of actie de optie **Nieuwe stap**.

   Als u een actie tussen bestaande stappen wilt toevoegen, verplaatst u de muis over de verbindingspijl. 
   Kies het plusteken (**+**) dat wordt weergegeven en selecteer Vervolgens Een actie **toevoegen**.

1. Voer in het zoekvak 'gebeurtenishubs' in als filter.
Selecteer deze actie in de lijst met acties: **Gebeurtenis verzenden - Gebeurtenishubs**

   ![Selecteer actie 'Gebeurtenis verzenden'](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Als u wordt gevraagd om verbindingsgegevens, [maakt u nu uw verbinding met gebeurtenishubs](#create-connection). 

1. Geef in de actie informatie over de gebeurtenissen die u wilt verzenden. Open de lijst **Nieuwe parameter toevoegen voor** meer eigenschappen. Als u een parameter selecteert, voegt u die eigenschap toe aan de actiekaart.

   ![Selecteer de naam van de gebeurtenishub en geef gebeurtenisinhoud op](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam gebeurtenishub** | Ja | De Event Hub waar u het evenement wilt verzenden |
   | **Inhoud** | Nee | De inhoud voor het evenement dat u wilt verzenden |
   | **Eigenschappen** | Nee | De app-eigenschappen en -waarden die moeten worden verzonden |
   | **Partitiesleutel** | Nee | De [partitie-id](../event-hubs/event-hubs-features.md#partitions) voor waar de gebeurtenis moet worden verzonden |
   ||||

   U de uitvoer van uw gebeurtenishubs bijvoorbeeld naar een andere gebeurtenishub verzenden:

   ![Voorbeeld van gebeurtenis verzenden](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Wanneer u klaar bent, kiest u op de werkbalk van de ontwerper de optie **Opslaan**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Verbinding maken met uw gebeurtenishub

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Wanneer u om verbindingsgegevens wordt gevraagd, geeft u de volgende gegevens op:

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Verbindingsnaam** | Ja | <*verbindingsnaam*> | De naam die u wilt maken voor uw verbinding |
   | **Naamruimte gebeurtenishubs** | Ja | <*gebeurtenishubs-naamruimte*> | Selecteer de naamruimte van gebeurtenishubs die u wilt gebruiken. |
   |||||  

   Bijvoorbeeld:

   ![Verbinding met gebeurtenishub maken](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Als u de verbindingstekenreeks handmatig wilt invoeren, selecteert u **Handmatig verbindingsgegevens invoeren**. 
   Meer informatie over [het vinden van uw verbindingstekenreeks](#permissions-connection-string).

2. Selecteer het beleid Gebeurtenishubs dat u wilt gebruiken, als dit nog niet is geselecteerd. Kies **Maken**.

   ![Event Hub-verbinding maken, deel 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Nadat u uw verbinding hebt gemaakt, gaat u verder met [de trigger van Gebeurtenishubs toevoegen](#add-trigger) of [Gebeurtenishubs toevoegen](#add-action).

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](https://docs.microsoft.com/connectors/eventhubs/)de connector voor technische details, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector.

> [!NOTE]
> Voor logische apps in een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt de ISE-versie met HET LABEL ISE in plaats daarvan de [ISE-berichtlimieten.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)