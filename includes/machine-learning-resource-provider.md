---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75893977"
---
Wanneer u een Azure Machine Learning werk ruimte maakt of een resource die wordt gebruikt door de werk ruimte, wordt er mogelijk een fout bericht weer gegeven die vergelijkbaar is met de volgende berichten:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

De meeste resource providers worden automatisch geregistreerd, maar niet alle. Als dit bericht wordt weer gegeven, moet u de vermelde provider registreren.

Zie [fouten voor de registratie van de resource provider oplossen](../articles/azure-resource-manager/templates/error-register-resource-provider.md)voor meer informatie over het registreren van resource providers.