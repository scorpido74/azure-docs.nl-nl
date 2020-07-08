---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 195caa6f8d7c00c741741fbf80a77bd7fe5579b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84464985"
---
Met de volgende CLI-opdracht wordt al het verkeer geweigerd naar het openbare eindpunt van het opslagaccount. In deze opdracht is de parameter `-bypass` ingesteld op `AzureServices`. Hierdoor kunnen vertrouwde Microsoft-services, zoals Azure File Sync, via het openbare eindpunt toegang krijgen tot het opslagaccount.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```