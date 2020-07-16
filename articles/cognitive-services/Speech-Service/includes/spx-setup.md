---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: b0f70fccf3f7f4a6856ae64d0946c2c473fed93c
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050253"
---
## <a name="prerequisites"></a>Vereisten

De enige vereiste is een Azure Speech-abonnement. Raadpleeg de [Handleiding](../get-started.md#new-resource) voor het maken van een nieuw abonnement als u er nog geen hebt.

## <a name="download-and-install"></a>Downloaden en installeren

#### <a name="windows-install"></a>[Windows installeren](#tab/windowsinstall)

Volg deze stappen om de Speech CLI te installeren voor Windows:

1. Installeer [.NET Framework 4.7](https://dotnet.microsoft.com/download/dotnet-framework/net471) of [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Download het Speech CLI-[zip-bestand](https://aka.ms/speech/spx-zips.zip) en pak het uit.
3. Ga naar de hoofdmap `spx-zips` van de download en pak de gewenste submap uit (`spx-net471` voor .NET Framework 4.7 of `spx-netcore-win-x64` voor .NET Core 3.0 op een x64-CPU) uit.

Wijzig in de opdrachtprompt de map naar deze locatie en typ `spx` om de Help voor de Speech CLI weer te geven.

> [!NOTE]
> De lokale map wordt niet door Powershell gecontroleerd tijdens het zoeken naar een opdracht. Wijzig in Powershell de map naar de locatie van `spx` en roep het hulpprogramma aan door `.\spx` in te voeren.
> Als u deze map aan het pad, Powershell en de opdrachtprompt van Windows toevoegt, vindt u `spx` vanuit een willekeurige map, zonder het `.\`-voorvoegsel.

#### <a name="linux-install"></a>[Linux-installatie](#tab/linuxinstall)

Volg deze stappen om de Speech CLI te installeren voor Linux op een x64 CPU:

1. Installeer [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Download het Speech CLI-[zip-bestand](https://aka.ms/speech/spx-zips.zip) en pak het uit.
3. Ga naar de hoofdmap `spx-zips` van het gedownloade bestand en pak `spx-netcore-30-linux-x64` uit naar een nieuwe `~/spx` map.
4. Typ in een terminal de volgende opdrachten:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Typ `spx` om Help weer te geven voor de Speech CLI.

***

## <a name="create-subscription-config"></a>Abonnementsconfiguratie maken

Als u de Speech CLI wilt gaan gebruiken, moet u eerst de sleutel voor het spraakabonnement en de regiogegevens invoeren. Zie de pagina [regio-ondersteuning](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) om uw regio-id te vinden. Zodra u uw abonnementssleutel en regio-id hebt (bijvoorbeeld `eastus`, `westus`), voert u de volgende opdrachten uit.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

Uw abonnementsverificatie wordt nu opgeslagen voor toekomstige SPX-aanvragen. Als u een van deze opgeslagen waarden wilt verwijderen, voert u `spx config @region --clear` of `spx config @key --clear` uit.
