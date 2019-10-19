---
title: bestand opnemen
description: bestand opnemen
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: da27f818bf368108568287f1ed1bbdae4c3902d4
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72560322"
---
## <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

U moet u eerst een Media Services-account maken. In deze sectie wordt beschreven wat u nodig hebt voor het maken van een account met behulp van de Azure CLI.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Gebruik de volgende opdracht om een resourcegroep te maken. Een Azure-resourcegroep is een logische container waarin resources, zoals Azure Media Services-accounts en bijbehorende Storage-accounts worden geïmplementeerd en beheerd.

U kunt `amsResourceGroup` vervangen door uw waarde.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Maak een opslagaccount

Als u een Media Services-account gaat maken, moet u de naam van een Azure Storage-accountresource opgeven. Het opgegeven opslagaccount wordt gekoppeld aan uw Media Services-account. Zie [Storage-accounts](../articles/media-services/latest/storage-account-concept.md) voor meer informatie over het gebruik van Storage-accounts in Media Services.

U kunt maar één **primaire** opslagaccount koppelen aan uw Media Services-account, maar een onbeperkt aantal **secundaire** opslagaccounts. Media Services ondersteunt **GPv2**-accounts (General-purpose v2) of **GPv1**-accounts (General-purpose v1). Blob-accounts kunt u niet instellen als **primaire** account. Zie [Opties voor Azure Storage-account](../articles/storage/common/storage-account-options.md) voor meer informatie over opslagaccounts. 

In dit voorbeeld maakt u een Standard LRS-account voor algemeen gebruik (v2). Als u wilt experimenteren met opslagaccounts, gebruikt u `--sku Standard_LRS`. Als u echter een SKU voor productie selecteert, kunt u overwegen om `--sku Standard_RAGRS` te gebruiken. Deze biedt geografische replicatie voor bedrijfscontinuïteit. Zie [Opslagaccounts](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest) voor meer informatie.
 
Met de volgende opdracht maakt u een Storage-account die wordt gekoppeld aan de Media Services-account. In het onderstaande script kunt u `storageaccountforams` door uw waarde vervangen. `amsResourceGroup` moet overeenkomen met de waarde die u in de vorige stap voor de resource groep hebt opgegeven. De naam van het opslag account moet minder dan 24 tekens lang zijn.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

Met de volgende Azure CLI-opdracht wordt een nieuwe Media Services-account gemaakt. U kunt de volgende waarden vervangen: `amsaccount`  `storageaccountforams` (moet overeenkomen met de waarde die u voor uw opslagaccount hebt opgegeven), en `amsResourceGroup` (moet overeenkomen met de waarde die u hebt opgegeven voor de resourcegroep).

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```
