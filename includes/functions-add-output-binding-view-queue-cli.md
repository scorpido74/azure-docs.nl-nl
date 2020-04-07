---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 44823ce888e97b308f29403612f598c0eb585ae5
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673380"
---
U de wachtrij weergeven in de [Azure-portal](../articles/storage/queues/storage-quickstart-queues-portal.md) of in de [Microsoft Azure Storage Explorer.](https://storageexplorer.com/) U ook de wachtrij weergeven in de Azure CLI, zoals beschreven in de volgende stappen:

1. Open het bestand *local.setting.json* van het functieproject en kopieer de waarde van de verbindingstekenreeks. Voer in een terminal- of opdrachtvenster de volgende `AZURE_STORAGE_CONNECTION_STRING`opdracht uit om een omgevingsvariabele met de naam , waarbij u uw specifieke verbindingstekenreeks plakt in plaats van `<MY_CONNECTION_STRING>`. (Deze omgevingsvariabele betekent dat u de verbindingstekenreeks niet hoeft `--connection-string` te leveren aan elke volgende opdracht met behulp van het argument.)

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Optioneel) Gebruik [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) de opdracht om de opslagwachtrijen in uw account weer te geven. De uitvoer van deze opdracht `outqueue`moet een wachtrij met de naam bevatten, die is gemaakt toen de functie het eerste bericht naar die wachtrij schreef.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Gebruik [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) de opdracht om het bericht uit deze wachtrij te lezen, de eerste naam die u eerder hebt gebruikt bij het testen van de functie. De opdracht leest en verwijdert het eerste bericht uit de wachtrij. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Dit script maakt gebruik van certutil om de base64-gecodeerde berichtenverzameling uit een lokaal temp-bestand te decoderen. Als er geen uitvoer is, probeert u het script te verwijderen `> NUL` om de certutil-uitvoer te stoppen, voor het geval er een fout optreedt. 
    
    ---
    
    Omdat de berichttekst is opgeslagen [base64 gecodeerd,](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)moet het bericht worden gedecodeerd voordat het wordt weergegeven. Nadat u `az storage message get`bent uitgevoerd, wordt het bericht uit de wachtrij verwijderd. Als er slechts één `outqueue`bericht in is geweest, wordt een bericht niet opgehaald wanneer u deze opdracht een tweede keer uitvoert en in plaats daarvan een foutmelding krijgt.