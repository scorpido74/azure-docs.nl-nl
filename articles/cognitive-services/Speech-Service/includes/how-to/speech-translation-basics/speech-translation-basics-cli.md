---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: f28dd3e94db9d16645bf0d63841a17ee66defd7a
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776648"
---
Een van de belangrijkste functies van de speech-service is de mogelijkheid om menselijke spraak te herkennen en te vertalen naar andere talen. In deze Snelstartgids leert u hoe u de Speech SDK in uw apps en producten kunt gebruiken om spraak vertalingen van hoge kwaliteit uit te voeren. Deze Quick Start zet spraak van de microfoon om in tekst in een andere taal.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../get-started.md).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Bron-en doel taal instellen

Met deze opdracht wordt een spraak-CLI aangeroepen om spraak van de microfoon van Italiaans naar Frans te vertalen.

```shell
 spx translate --microphone --source it-IT --target fr
```
