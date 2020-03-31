---
title: Functietypen in duurzame azure-functies
description: Meer informatie over de typen functies en rollen die functie-naar-functiecommunicatie ondersteunen in een orkestratie met duurzame functies in Azure-functies.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277880"
---
# <a name="durable-functions-types-and-features"></a>Duurzame functies typen en functies

Duurzame functies is een uitbreiding van [Azure-functies.](../functions-overview.md) U Duurzame functies gebruiken voor stateful orchestration van functieuitvoering. Een duurzame functie-app is een oplossing die bestaat uit verschillende Azure-functies. Functies kunnen verschillende rollen spelen in een duurzame functieorkestratie. 

Er zijn momenteel vier duurzame functietypen in Azure-functies: activiteit, orchestrator, entiteit en client. De rest van deze sectie gaat in meer details over de soorten functies die betrokken zijn bij een orkestratie.

## <a name="orchestrator-functions"></a>Orchestrator-functies

Orchestrator-functies beschrijven hoe acties worden uitgevoerd en de volgorde waarin acties worden uitgevoerd. Orchestrator-functies beschrijven de orchestration in code (C# of JavaScript) zoals weergegeven in [toepassingspatronen voor duurzame functies.](durable-functions-overview.md#application-patterns) Een orkestratie kan veel verschillende soorten acties hebben, waaronder [activiteitsfuncties,](#activity-functions) [suborchestrations](durable-functions-orchestrations.md#sub-orchestrations), [wachten op externe gebeurtenissen](durable-functions-orchestrations.md#external-events), [HTTP](durable-functions-http-features.md)en [timers](durable-functions-orchestrations.md#durable-timers). Orchestrator-functies kunnen ook communiceren met [entiteitsfuncties.](#entity-functions)

> [!NOTE]
> Orchestrator functies zijn geschreven met behulp van gewone code, maar er zijn strenge eisen over hoe de code te schrijven. In het bijzonder moet orchestrator functiecode *deterministisch*zijn. Als u deze vereisten voor determinisme niet volgt, kunnen orchestrator-functies niet correct worden uitgevoerd. Gedetailleerde informatie over deze vereisten en hoe u er omheen werken, vindt u in het onderwerp [codebeperkingen.](durable-functions-code-constraints.md)

Zie het artikel [Duurzame orkestraties](durable-functions-orchestrations.md) voor meer gedetailleerde informatie over orchestrator-functies en hun functies.

## <a name="activity-functions"></a>Activiteitsfuncties

Activiteitsfuncties zijn de basiseenheid van het werk in een duurzame functieorkestratie. Activiteitsfuncties zijn de functies en taken die in het proces worden georkestreerd. U bijvoorbeeld een orchestrator-functie maken om een order te verwerken. De taken omvatten het controleren van de voorraad, het opladen van de klant en het maken van een zending. Elke taak zou een afzonderlijke activiteitsfunctie zijn. Deze activiteitsfuncties kunnen serieel worden uitgevoerd, parallel of een combinatie van beide.

In tegenstelling tot orchestrator-functies zijn activiteitsfuncties niet beperkt in het type werk dat u in deze functies doen. Activiteitsfuncties worden vaak gebruikt om netwerkgesprekken te voeren of cpu-intensieve bewerkingen uit te voeren. Een activiteitsfunctie kan gegevens ook terugsturen naar de orchestrator-functie. Het Framework Duurzame taak garandeert dat elke aangeroepen activiteitsfunctie *ten minste één keer* wordt uitgevoerd tijdens de uitvoering van een orkestratie.

> [!NOTE]
> Omdat activiteitsfuncties *slechts ten minste eenmaal de* uitvoering garanderen, raden we u aan uw activiteitsfunctie waar mogelijk *idempotent te* maken.

Gebruik een [activiteitstrigger](durable-functions-bindings.md#activity-trigger) om een activiteitsfunctie te definiëren. .NET-functies `DurableActivityContext` ontvangen een als parameter. U de trigger ook binden aan een ander JSON-serializeable object om in ingangen aan de functie door te geven. In JavaScript hebt u toegang `<activity trigger binding name>` tot een invoer via de eigenschap van het [ `context.bindings` object.](../functions-reference-node.md#bindings) Activiteitsfuncties kunnen slechts één waarde aan hen hebben doorgegeven. Als u meerdere waarden wilt doorgeven, moet u tuples, arrays of complexe typen gebruiken.

> [!NOTE]
> U een activiteitsfunctie alleen activeren vanuit een orchestrator-functie.

## <a name="entity-functions"></a>Entiteitsfuncties

Entiteitsfuncties definiëren bewerkingen voor het lezen en bijwerken van kleine stukjes status. We verwijzen vaak naar deze stateful entiteiten als *duurzame entiteiten*. Net als orchestrator-functies zijn entiteitsfuncties functies met een speciaal triggertype, *entiteitstrigger*. Ze kunnen ook worden aangeroepen vanuit clientfuncties of vanuit orchestrator-functies. In tegenstelling tot orchestrator-functies hebben entiteitsfuncties geen specifieke codebeperkingen. Entiteitsfuncties beheren ook expliciet status in plaats van impliciet status te vertegenwoordigen via controlestroom.

> [!NOTE]
> Entiteitsfuncties en gerelateerde functionaliteit zijn alleen beschikbaar in Duurzame functies 2.0 en hoger.

Zie het artikel Duurzame entiteiten voor meer informatie over [entiteitsfuncties.](durable-functions-entities.md)

## <a name="client-functions"></a>Clientfuncties

Orchestrator-functies worden geactiveerd door een [orkestratietriggerbinding](durable-functions-bindings.md#orchestration-trigger) en entiteitsfuncties worden geactiveerd door een [entiteittriggerbinding](durable-functions-bindings.md#entity-trigger). Beide triggers werken door te reageren op berichten die zijn geplaatst in een [taakhub.](durable-functions-task-hubs.md) De primaire manier om deze berichten af te leveren is door gebruik te maken van een [orchestrator client binding](durable-functions-bindings.md#orchestration-client) of een [entiteit client binding](durable-functions-bindings.md#entity-client) vanuit een client *functie*. Elke niet-orchestrator-functie kan een *clientfunctie*zijn. U bijvoorbeeld de orchestrator activeren vanuit een http-geactiveerde functie, een Azure Event Hub geactiveerde functie, enz. Wat maakt een functie een *client functie* is het gebruik van de duurzame client output binding.

> [!NOTE]
> In tegenstelling tot andere functietypen kunnen orchestrator- en entiteitsfuncties niet rechtstreeks worden geactiveerd met de knoppen in de Azure Portal. Als u een orchestrator- of entiteitsfunctie wilt testen in de Azure Portal, moet u in plaats daarvan een *clientfunctie* uitvoeren waarmee een orchestrator of entiteitsfunctie wordt gestart als onderdeel van de implementatie ervan. Voor de eenvoudigste testervaring wordt een *handmatige triggerfunctie* aanbevolen.

Naast het activeren van orchestrator- of entiteitsfuncties, kan de *duurzame clientbinding* worden gebruikt om te communiceren met lopende orkestraties en entiteiten. Orkestraties kunnen bijvoorbeeld worden opgevraagd, beëindigd en gebeurtenissen worden verhoogd. Zie het artikel [Instantiebeheer](durable-functions-instance-management.md) voor meer informatie over het beheren van orkestraties en entiteiten.

## <a name="next-steps"></a>Volgende stappen

Maak om aan de slag te gaan uw eerste duurzame functie in [C#](durable-functions-create-first-csharp.md) of [JavaScript.](quickstart-js-vscode.md)

> [!div class="nextstepaction"]
> [Lees meer over orkestraties voor duurzame functies](durable-functions-orchestrations.md)