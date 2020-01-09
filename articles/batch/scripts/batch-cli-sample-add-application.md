---
title: Azure CLI-voorbeeldscript - een toepassing toevoegen in Batch
description: Dit voorbeeld script laat zien hoe u een toepassing kunt toevoegen voor gebruik met een Azure Batch groep of een taak.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: lahugh
ms.openlocfilehash: 9c580fa7ed4834ca516d373b3c7bb47f4cfd1b38
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75390470"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>CLI-voorbeeld: een toepassing toevoegen aan een Azure Batch-account

Dit script laat zien hoe u een toepassing kunt toevoegen voor gebruik met een Azure Batch-groep of -taak. Als u een toepassing wilt instellen om aan uw Batch-account toe te voegen, verpakt u uw uitvoerbare bestand samen met eventuele afhankelijkheden in een .zip-bestand. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0.20 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. 

## <a name="example-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt.
Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Hiermee maakt u een opslagaccount. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Hiermee wordt de Batch-account gemaakt. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Hiermee wordt authenticatie uitgevoerd met het opgegeven Batch-account voor verdere interactie met de CLI.  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | Hiermee wordt een toepassing gemaakt.  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | Hiermee voegt u een toepassingspakket toe aan de opgegeven toepassing.  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | Hiermee worden de eigenschappen van een toepassing bijgewerkt.  |
| [az group delete](/cli/azure/group#az-group-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.
