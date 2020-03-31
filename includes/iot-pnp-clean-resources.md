---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75453715"
---
## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met extra IoT Plug and Play-artikelen, u de resources die u in deze quickstart hebt gebruikt, bewaren en opnieuw gebruiken. Anders u de resources die u in deze quickstart hebt gemaakt verwijderen om extra kosten te voorkomen.

U zowel de hub als het geregistreerde apparaat tegelijk verwijderen door de hele brongroep te verwijderen met de volgende opdracht voor Azure CLI. (Gebruik dit echter niet als deze bronnen een resourcegroep delen met andere resources die u voor verschillende doeleinden hebt.)

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
Als u alleen de IoT-hub wilt verwijderen, voert u de volgende opdracht uit met Azure CLI:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Voer de volgende opdracht uit met Azure CLI als u alleen de apparaatidentiteit wilt verwijderen die u bij uw IoT-hub hebt geregistreerd:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

U ook de gekloonde voorbeeldbestanden uit uw ontwikkelingsmachine verwijderen.