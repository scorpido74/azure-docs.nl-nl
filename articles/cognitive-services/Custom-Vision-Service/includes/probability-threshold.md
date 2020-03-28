---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68423559"
---
Let op de schuifregelaar **Waarschijnlijkheidsdrempel** in het linkerdeelvenster van het tabblad **Prestaties.** Dit is het niveau van vertrouwen dat een voorspelling moet hebben om als correct te worden beschouwd (met het oog op de berekening van precisie en terugroeping). 

Wanneer u voorspellingsoproepen interpreteert met een hoge waarschijnlijkheidsdrempel, hebben&mdash;ze de neiging om resultaten met hoge precisie terug te sturen ten koste van het terugroepen van de gedetecteerde classificaties correct zijn, maar veel blijven onopgemerkt. Een lage waarschijnlijkheidsdrempel&mdash;doet het tegenovergestelde van de meeste daadwerkelijke classificaties worden ontdekt, maar er zijn meer valse positieven binnen die reeks. Met dit in gedachten moet u de waarschijnlijkheidsdrempel instellen op basis van de specifieke behoeften van uw project. Wanneer u later voorspellingsresultaten ontvangt aan de clientzijde, moet u dezelfde waarschijnlijkheidsdrempelwaarde gebruiken als u hier hebt gebruikt.