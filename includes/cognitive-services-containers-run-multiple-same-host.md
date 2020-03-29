---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67704320"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Meerdere containers op dezelfde host uitvoeren

Als u van plan bent om meerdere containers met blootgestelde poorten uit te voeren, zorg ervoor dat elke container met een andere blootgestelde poort wordt uitgevoerd. Voer bijvoorbeeld de eerste container uit op poort 5000 en de tweede container op poort 5001.

U deze container en een andere Azure Cognitive Services-container samen laten uitvoeren op de HOST. U ook meerdere containers van dezelfde Cognitive Services-container laten uitvoeren.
