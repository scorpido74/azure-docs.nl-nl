---
author: baanders
description: bestand insluiten voor Azure Digital Apparaatdubbels-Cloud Shell en de IoT-extensie instellen
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 76480959d94b9e2a2e04857c85b7359105e73e4f
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84611471"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell sessie instellen

Nadat u een Cloud Shell venster hebt geopend, moet u zich aanmelden en de shell context voor deze sessie instellen op uw abonnement. 

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Als dit de eerste keer is dat u dit abonnement met Azure Digital Apparaatdubbels hebt gebruikt, voert u deze opdracht uit om u te registreren bij de naam ruimte van Azure Digital Apparaatdubbels. (Als u het niet zeker weet, kunt u het nu opnieuw uitvoeren, zelfs als u deze in het verleden ergens in de tijd hebt gedaan.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Voer vervolgens de volgende opdracht in uw Cloud Shell-exemplaar uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

> [!NOTE]
> In dit artikel wordt gebruikgemaakt van de nieuwste versie van de Azure IoT-extensie, die wordt genoemd `azure-iot` . De verouderde versie wordt aangeroepen `azure-iot-cli-ext` . Er mag slechts één versie tegelijk zijn geïnstalleerd. U kunt de opdracht gebruiken `az extension list` om de geïnstalleerde uitbrei dingen te valideren.
> Gebruik `az extension remove --name azure-cli-iot-ext` om de verouderde versie van de uitbrei ding te verwijderen.
> Gebruiken `az extension add --name azure-iot` om de nieuwe versie van de uitbrei ding toe te voegen. Gebruik voor meer informatie over de uitbrei dingen die u hebt geïnstalleerd `az extension list` .

> [!TIP]
> U kunt uitvoeren `az dt -h` om de Azure Digital apparaatdubbels-opdrachten op het hoogste niveau weer te geven.