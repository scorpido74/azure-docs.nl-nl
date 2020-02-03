---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963649"
---
Gebruik een afzonderlijk opslag account voor elke functie-app om de prestaties te maximaliseren. Dit is vooral belang rijk wanneer u Durable Functions of event hub geactiveerde functies hebt, waarbij beide een groot aantal opslag transacties genereren. Wanneer uw toepassings logica communiceert met Azure Storage, direct (met behulp van de opslag-SDK) of via een van de opslag bindingen, moet u een speciaal opslag account gebruiken. Als u bijvoorbeeld een event hub-functie hebt geactiveerd om bepaalde gegevens te schrijven naar Blob Storage, gebruikt u twee opslag accounts&mdash;een voor de functie-app en een andere voor de blobs die door de functie worden opgeslagen.