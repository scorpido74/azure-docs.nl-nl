---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: c57187ae15e5f15a601edbc74921b3c879abe715
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800670"
---
## <a name="prerequisites"></a>Vereisten

De enige vereiste is een Azure speech-abonnement. Raadpleeg de [hand leiding](../get-started.md#new-resource) voor het maken van een nieuw abonnement als u er nog geen hebt.

## <a name="download-and-install"></a>Downloaden en installeren

#### <a name="windows-install"></a>[Windows installeren](#tab/windowsinstall)

Volg deze stappen om de speech CLI te installeren in Windows:

1. Installeer [.NET Framework 4,7](https://dotnet.microsoft.com/download/dotnet-framework/net471) of [.net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Down load het speech CLI [zip-archief](https://aka.ms/speech/spx-zips.zip)en pak het uit.
3. Ga naar de hoofdmap `spx-zips` die u hebt geëxtraheerd uit de down load en pak de gewenste submap uit ( `spx-net471` voor .NET Framework 4,7, of `spx-netcore-win-x64` voor .net Core 3,0 op een x64-CPU).

In de opdracht prompt wijzigt u de map in deze locatie en typt `spx` u om de Help voor de spraak-cli weer te geven.

#### <a name="linux-install"></a>[Linux-installatie](#tab/linuxinstall)

Voer de volgende stappen uit om de speech CLI op Linux te installeren op een x64-CPU:

1. Installeer [.net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Down load het speech CLI [zip-archief](https://aka.ms/speech/spx-zips.zip)en pak het uit.
3. Ga naar de hoofdmap `spx-zips` die u hebt geëxtraheerd uit de down load en pak deze uit `spx-netcore-30-linux-x64` naar een nieuwe `~/spx` map.
4. Typ in een Terminal de volgende opdrachten:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Typ `spx` om Help voor de speech CLI te bekijken.

***

## <a name="create-subscription-config"></a>Abonnements configuratie maken

Als u de spraak-CLI wilt gaan gebruiken, moet u eerst de sleutel voor het spraak abonnement en de regio gegevens invoeren. Bekijk de [ondersteunings](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) pagina voor regio's om uw regio-id te vinden. Zodra u uw abonnements sleutel en regio-id hebt (bijvoorbeeld `eastus`, `westus` ) voert u de volgende opdrachten uit.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

Uw abonnements verificatie wordt nu opgeslagen voor toekomstige SPX-aanvragen. Als u een van deze opgeslagen waarden wilt verwijderen, voert u `spx config @region --clear` of uit `spx config @key --clear` .
