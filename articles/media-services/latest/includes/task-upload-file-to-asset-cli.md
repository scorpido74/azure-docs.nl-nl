---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: affd55d65ed8454ebfcdf14f697849badf8e5d3c
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88657042"
---
<!-- ### Upload files with the CLI -->

Met de volgende opdracht wordt één bestand geüpload.  

Wijzig de volgende waarden:

1. Wijzig `/path/to/file` in het lokale pad van het bestand.  
1. Wijzig `MyContainer` in een asset dat u eerder hebt gemaakt met behulp van de asset-id (niet de naam).
1. Wijzig `MyBlob` in de naam die u wilt gebruiken voor het geüploade bestand.
1. Wijzig `MyStorageAccountName` in de naam van het opslagaccount dat u gebruikt.
1. Wijzig `MyStorageAccountKey` in de toegangssleutel voor uw opslagaccount.

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob --acount-name MyStorageAccountName --account-key MyStorageAccountKey
    ```
