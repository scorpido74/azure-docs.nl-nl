---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75893977"
---
Wanneer u een Azure Machine Learning werk ruimte maakt of een resource die wordt gebruikt door de werk ruimte, wordt er mogelijk een fout bericht weer gegeven die vergelijkbaar is met de volgende berichten:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

De meeste resource providers worden automatisch geregistreerd, maar niet alle. Als dit bericht wordt weer gegeven, moet u de vermelde provider registreren.

Zie [fouten voor de registratie van de resource provider oplossen](../articles/azure-resource-manager/templates/error-register-resource-provider.md)voor meer informatie over het registreren van resource providers.