---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75893977"
---
Wanneer u een Azure Machine Learning-werkruimte maakt of een resource die door de werkruimte wordt gebruikt, ontvangt u mogelijk een fout die vergelijkbaar is met de volgende berichten:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

De meeste resourceproviders worden automatisch geregistreerd, maar niet alle. Als u dit bericht ontvangt, moet u de genoemde provider registreren.

Zie Fouten oplossen voor [de registratie van resourceproviders](../articles/azure-resource-manager/templates/error-register-resource-provider.md)voor informatie over het registreren van resourceproviders.