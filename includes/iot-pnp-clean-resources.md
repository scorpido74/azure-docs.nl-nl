---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453715"
---
## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om door te gaan met aanvullende IoT Plug en Play-artikelen, kunt u de resources die u in deze Quick Start hebt gebruikt, behouden en opnieuw gebruiken. Anders kunt u de resources die u in deze Quick Start hebt gemaakt, verwijderen om te voor komen dat er extra kosten in rekening worden gebracht.

U kunt zowel de hub als het geregistreerde apparaat tegelijk verwijderen door de hele resource groep te verwijderen met de volgende opdracht voor Azure CLI. (Gebruik dit niet, maar als deze resources een resource groep delen met andere resources die u voor verschillende doel einden hebt.)

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
Als u alleen de IoT-hub wilt verwijderen, voert u de volgende opdracht uit met behulp van Azure CLI:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Als u alleen de apparaat-id wilt verwijderen die u hebt geregistreerd bij de IoT-hub, voert u de volgende opdracht uit met behulp van Azure CLI:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Mogelijk wilt u ook de gekloonde voorbeeld bestanden verwijderen van uw ontwikkel computer.