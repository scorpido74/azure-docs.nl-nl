---
title: Richt lijnen voor het afhandelen van Azure Functions | Microsoft Docs
description: Bevat algemene richt lijnen voor het afhandelen van fouten die zich voordoen wanneer uw functies worden uitgevoerd, en koppelingen naar onderwerpen over binding-specifieke fouten.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: fdfee3442986322f242da730bb9ceccbc9f9e250
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097488"
---
# <a name="azure-functions-error-handling"></a>Fout afhandeling Azure Functions

Dit onderwerp bevat algemene richt lijnen voor het afhandelen van fouten die optreden wanneer uw functies worden uitgevoerd. Het bevat ook koppelingen naar de onderwerpen over het beschrijven van binding-specifieke fouten die zich kunnen voordoen. 

## <a name="handling-errors-in-functions"></a>Fouten in functies afhandelen
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Bindings fout codes

Wanneer u integreert met Azure-Services, treden er fouten op die afkomstig zijn van de Api's van de onderliggende services. Koppelingen naar de documentatie over de fout code voor deze services vindt u in de sectie **uitzonde ringen en retour codes** van de volgende onderwerpen over triggers en bindingen:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
