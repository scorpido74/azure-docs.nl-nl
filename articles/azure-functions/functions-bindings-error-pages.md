---
title: Richt lijnen voor het afhandelen van Azure Functions
description: Meer informatie over het afhandelen van fouten in Azure Functions met koppelingen naar specifieke bindings fouten.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 920b887382ca55d84c66fabb8f1464fd05041094
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198447"
---
# <a name="azure-functions-error-handling"></a>Fout afhandeling Azure Functions

Het afhandelen van fouten in Azure Functions is belang rijk om te voor komen dat gegevens verloren gaan, gemiste gebeurtenissen en de status van uw toepassing te controleren.

In dit artikel worden algemene strategieën beschreven voor het afhandelen van fouten, samen met koppelingen naar binding-specifieke fouten.

## <a name="handling-errors"></a>Fouten afhandelen

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Bindings fout codes

Wanneer u integreert met Azure-Services, kunnen fouten afkomstig zijn van de Api's van de onderliggende services. Informatie met betrekking tot binding-specifieke fouten is beschikbaar in de sectie **uitzonde ringen en retour codes** van de volgende artikelen:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
