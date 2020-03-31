---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.custom: fasttrack-edit
ms.openlocfilehash: 819eed1eb4e32469bf0572a6548f5a2a44091ef0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77279426"
---
In deze tabel worden de bindingen weergegeven die worden ondersteund in de belangrijkste versies van de runtime azure-functies:


| Type | 1.x | 2.x en hoger<sup>1</sup> | Trigger | Invoer | Uitvoer |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob-opslag](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|
| [Event Grid](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| |✔|
| [Gebeurtenishubs](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [HTTP-& webhooks](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [IoT-hub](../articles/azure-functions/functions-bindings-event-iot.md)             |✔|✔|✔| |✔|
| [Excel-tabellen in Microsoft Graph<br/>](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Microsoft<br/>Graph OneDrive-bestanden](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft<br/>Graph Outlook-e-mail](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Microsoft<br/>Graph-gebeurtenissen](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft<br/>Graph Auth-tokens](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Mobiele apps](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|
| [Notification Hubs](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Wachtrijopslag](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Servicebus](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|
| [SignalR](../articles/azure-functions/functions-bindings-signalr-service.md)             | |✔| |✔|✔|
| [Tabelopslag](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|
| [Timer](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|

<sup>1</sup> Vanaf versie 2.x runtime moeten alle bindingen behalve HTTP en Timer worden geregistreerd. Zie [Bindende extensies registreren](../articles/azure-functions/functions-bindings-register.md).
