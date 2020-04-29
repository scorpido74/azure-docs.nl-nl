---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 44823ce888e97b308f29403612f598c0eb585ae5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673380"
---
U kunt de wachtrij weer geven in de [Azure Portal](../articles/storage/queues/storage-quickstart-queues-portal.md) of in de [Microsoft Azure Storage Explorer](https://storageexplorer.com/). U kunt de wachtrij ook weer geven in de Azure CLI, zoals wordt beschreven in de volgende stappen:

1. Open het bestand *Local. setting. json* van het functie project en kopieer de Connection String waarde. Voer in een Terminal-of opdracht venster de volgende opdracht uit om een omgevings variabele `AZURE_STORAGE_CONNECTION_STRING`met de naam te maken, waarbij u `<MY_CONNECTION_STRING>`uw specifieke Connection String plakt in plaats van. (Deze omgevings variabele houdt in dat u de connection string niet hoeft op te geven bij elke `--connection-string` volgende opdracht met behulp van het argument.)

    # <a name="bash"></a>[bash](#tab/bash)
    
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
    
1. Beschrijving Gebruik de [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) opdracht om de opslag wachtrijen in uw account weer te geven. De uitvoer van deze opdracht moet een wachtrij bevatten met `outqueue`de naam, die is gemaakt toen de functie het eerste bericht aan die wachtrij heeft geschreven.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Gebruik de [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) opdracht om het bericht uit deze wachtrij te lezen. dit moet de voor naam zijn die u hebt gebruikt bij het eerder testen van de functie. Met de opdracht leest en verwijdert u het eerste bericht uit de wachtrij. 

    # <a name="bash"></a>[bash](#tab/bash)
    
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

    Dit script maakt gebruik van Certutil voor het decoderen van de met base64 gecodeerde berichten verzameling vanuit een lokaal tijdelijk bestand. Als er geen uitvoer is, Verwijder `> NUL` dan uit het script om het onderdrukken van Certutil-uitvoer te stoppen, als er een fout is opgetreden. 
    
    ---
    
    Omdat de bericht tekst base64- [gecodeerd](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)is opgeslagen, moet het bericht worden gedecodeerd voordat het wordt weer gegeven. Nadat u het `az storage message get`script hebt uitgevoerd, wordt het bericht uit de wachtrij verwijderd. Als er slechts één bericht in wordt `outqueue`weer gegeven, haalt u een bericht niet op wanneer u deze opdracht een tweede keer uitvoert en wordt in plaats daarvan een fout opgetreden.