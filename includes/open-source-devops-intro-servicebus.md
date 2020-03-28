---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67176725"
---
[Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) is een berichtenmakelaar voor [bedrijfsintegratie.](https://azure.microsoft.com/product-categories/integration/) De servicebus ondersteunt twee soorten communicatie: wachtrijen en onderwerpen. 

Wachtrijen ondersteunen asynchrone communicatie tussen toepassingen. Een app stuurt berichten naar een wachtrij, die de berichten opslaat. De ontvangende toepassing maakt vervolgens verbinding met en leest de berichten uit de wachtrij.

Onderwerpen ondersteunen het patroon van het publiceren abonneren, waardoor een één-op-veel relatie mogelijk is tussen de afzender van het bericht en de ontvanger(en) van de messager.