---
author: baanders
description: bestand insluiten voor Azure Digital Apparaatdubbels-Cloud Shell en de IoT-extensie instellen
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 8be070826de0334483f4150925c05cb4dfb73f2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805527"
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

Vervolgens voegt u de [**Microsoft Azure IOT-extensie voor Azure cli**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) toe aan uw Cloud shell om opdrachten in te scha kelen voor interactie met Azure Digital apparaatdubbels en andere IOT-Services. 

Voer eerst deze opdracht uit om een lijst weer te geven van alle uitbrei dingen die u al hebt geïnstalleerd.

```azurecli-interactive
az extension list
```

Zoek in de uitvoer naar het `"name"` veld voor elk vermelding in de lijst om de namen van de extensies weer te geven.

Gebruik de uitvoer om te bepalen welke van de volgende opdrachten moet worden uitgevoerd voor de extensie-instelling (u kunt meer dan één uitvoeren).
* Als de lijst bevat `azure-iot` : u hebt de extensie al. Voer deze opdracht uit om te controleren of u de meest recente update hebt:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Als de lijst **niet** bevat `azure-iot` : moet u de extensie installeren. Gebruik deze opdracht:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Als de lijst bevat `azure-iot-cli-ext` : dit is de verouderde versie van de uitbrei ding. Er moet slechts één versie van de uitbrei ding tegelijk worden geïnstalleerd, dus u moet de oude uitbrei ding verwijderen. Gebruik deze opdracht:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

U bent nu klaar om te gaan werken met Azure Digital Apparaatdubbels in de Cloud Shell.

U kunt dit controleren door uit `az dt -h` te voeren op elk gewenst moment om een lijst weer te geven van de Azure Digital apparaatdubbels-opdrachten op het hoogste niveau die beschikbaar zijn.