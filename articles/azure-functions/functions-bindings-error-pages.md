---
title: Richtlijnen voor foutafhandeling azure-functies
description: Leer fouten in Azure-functies verwerken met koppelingen naar specifieke bindingsfouten.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586186"
---
# <a name="azure-functions-error-handling"></a>Foutafhandeling azure-functies

Het afhandelen van fouten in Azure-functies is belangrijk om verloren gegevens, gemiste gebeurtenissen te voorkomen en de status van uw toepassing te controleren.

In dit artikel worden algemene strategieën voor foutafhandeling beschreven, samen met koppelingen naar bindingsspecifieke fouten.

## <a name="handling-errors"></a>Afhandeling van fouten

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Bindende foutcodes

Bij de integratie met Azure-services kunnen fouten afkomstig zijn van de API's van de onderliggende services. Informatie met betrekking tot bindingsspecifieke fouten is beschikbaar in de sectie **Uitzonderingen en retourcodes** van de volgende artikelen:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob-opslag](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Gebeurtenishubs](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT-hubs](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Wachtrijopslag](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Servicebus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Tabelopslag](functions-bindings-storage-table.md#exceptions-and-return-codes)
