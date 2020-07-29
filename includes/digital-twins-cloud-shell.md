---
author: baanders
description: bestand opnemen voor Azure Digital Twins - Cloud Shell en de IoT-extensie instellen
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b7c91d648c06970d53799c6ff505919dea17b3c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032205"
---
Om aan de slag te gaan met Azure Digital Twins in een open [Azure Cloud Shell](https://shell.azure.com)-venster, moet u zich eerst aanmelden en de shellcontext voor deze sessie instellen op uw abonnement. Voer de volgende opdrachten uit in uw Cloud Shell:

```azurecli
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> U kunt ook de naam van uw abonnement in plaats van de ID gebruiken in de bovenstaande opdracht. 

Als dit de eerste keer is dat u dit abonnement met Azure Digital Twins gebruikt, voert u deze opdracht uit om u te registreren bij de naamruimte van Azure Digital Twins. (Als u het niet zeker weet, kunt u de opdracht opnieuw uitvoeren, zelfs als u dit in het verleden een keer hebt gedaan.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Vervolgens voegt u de [**Microsoft Azure IoT-extensie voor Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) aan uw Cloud Shell toe om opdrachten voor interactie met Azure Digital Twins en andere IoT-Services in te schakelen. 

Voer eerst deze opdracht uit om een lijst weer te geven met alle extensies die u al hebt geïnstalleerd.

```azurecli-interactive
az extension list
```

De uitvoer is een matrix van alle extensies die u momenteel hebt. Zoek naar het veld `"name"` voor elk vermelding in de lijst om de namen van de extensies weer te geven.

Gebruik de uitvoer om te bepalen welke van de volgende opdrachten moet worden uitgevoerd voor de extensie-instelling (u kunt er meer dan één uitvoeren).
* Als de lijst `azure-iot` bevat: U hebt de extensie al. Voer deze opdracht uit om te controleren of u over de meest recente update beschikt en er geen updates meer beschikbaar zijn:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Als de lijst `azure-iot` **niet** bevat: U moet de extensie installeren. Gebruik deze opdracht:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Als de lijst `azure-iot-cli-ext` bevat: Dit is de verouderde versie van de extensie. Er moet slechts één versie van de extensie tegelijkertijd zijn geïnstalleerd. U moet de oude extensie dus verwijderen. Gebruik deze opdracht:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

U bent nu klaar om aan de slag te gaan met Azure Digital Twins in de Cloud Shell.

U kunt dit controleren door `az dt -h` op elk gewenst moment uit te voeren om een lijst weer te geven van de Azure Digital Twins-opdrachten op het hoogste niveau die beschikbaar zijn.