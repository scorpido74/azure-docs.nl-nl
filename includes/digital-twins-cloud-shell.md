---
author: baanders
description: bestand insluiten voor Azure Digital Apparaatdubbels-Cloud Shell en de IoT-extensie instellen
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 4aa016294f0ef3bd26f7f3ef6fa374e9367b672d
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296964"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell sessie instellen

Nadat u een Cloud Shell venster hebt geopend, moet u zich aanmelden en de shell context voor deze sessie instellen op uw abonnement. Voer deze opdrachten uit in uw Cloud Shell:

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Als dit de eerste keer is dat u dit abonnement met Azure Digital Apparaatdubbels hebt gebruikt, voert u deze opdracht uit om u te registreren bij de naam ruimte van Azure Digital Apparaatdubbels. (Als u het niet zeker weet, kunt u het nu opnieuw uitvoeren, zelfs als u deze in het verleden ergens in de tijd hebt gedaan.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Vervolgens voegt u de [**Microsoft Azure IOT-extensie voor Azure cli**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) toe aan uw Cloud shell om opdrachten in te scha kelen voor interactie met Azure Digital apparaatdubbels en andere IOT-Services. Gebruik deze opdracht om de extensie toe te voegen:

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

Als u de uitbrei ding in het verleden hebt geïnstalleerd, kan de uitvoer zeggen dat "extensie" Azure-IOT "al is geïnstalleerd." Als dit het geval is, voert u de volgende handelingen uit om ervoor te zorgen dat u over de meest recente update beschikt: 

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

U bent nu klaar om te gaan werken met Azure Digital Apparaatdubbels in de Cloud Shell.

U kunt dit controleren door uit `az dt -h` te voeren op elk gewenst moment om een lijst weer te geven van de Azure Digital apparaatdubbels-opdrachten op het hoogste niveau die beschikbaar zijn.