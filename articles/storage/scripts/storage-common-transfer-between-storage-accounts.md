---
title: Blobs migreren tussen opslag accounts met AzCopy in Windows
description: 'Azure PowerShell script-voor beeld: Hiermee kopieert u de blob-inhoud van een Azure Storage account naar een ander.'
services: storage
documentationcenter: na
author: normesta
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: normesta
ms.openlocfilehash: 559b8b2875b789034ae07901f668f241505073b1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465062"
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Blobs migreren tussen opslagaccounts met behulp van AzCopy in Windows

In dit voorbeeld worden alle blob-objecten van een door de gebruiker opgegeven opslagaccount gemigreerd naar een door de gebruiker opgegeven doelopslagaccount. 

Dit gebeurt door gebruik te maken van de opdracht `Get-AzStorageContainer`, waarmee een lijst wordt gemaakt van alle containers in een opslagaccount. In het voorbeeld worden vervolgens AzCopy-opdrachten gegeven waarmee elke container van het bronaccount naar het doelaccount word gekopieerd. Als er fouten optreden, wordt de bewerking $retryTimes opnieuw geprobeerd (de standaardwaarde is 3, en de waarde kan worden gewijzigd met de parameter `-RetryTimes`). Als er bij elke nieuwe poging een fout optreedt, kan de gebruiker het script opnieuw uitvoeren door de laatste met succes gekopieerde container op te geven in het voorbeeldscript met behulp van de parameter `-LastSuccessContainerName`. Het voorbeeld gaat vervolgens verder met het kopiëren van containers vanaf dat punt.

Voor dit voorbeeld is versie **0.7** of hoger van de Azure PowerShell Storage-module vereist. U kunt de geïnstalleerde versie controleren met behulp van `Get-Module -ListAvailable Az.storage`. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Dit voorbeeld vereist ook de nieuwste versie van [AzCopy in Windows](https://aka.ms/downloadazcopy). De standaardinstallatiedirectory is `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`

In dit voorbeeld worden de naam en sleutel van een bronopslagaccount, de naam en sleutel van een doelopslagaccount, en het volledige bestandspad van AzCopy.exe (als het niet is geïnstalleerd in de standaarddirectory) ingevoerd.

Hier volgen enkele voorbeelden van de invoer voor dit voorbeeld:

Als AzCopy in de standaarddirectory is geïnstalleerd:
```powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

Als AzCopy niet in de standaarddirectory is geïnstalleerd:

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

Als er een fout optreedt en het voorbeeld opnieuw moet worden uitgevoerd vanaf een bepaalde container: 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om gegevens van het ene opslagaccount naar het andere te kopiëren. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Retourneert de containers die zijn gekoppeld aan dit Storage-account. |
| [New-AzStorageContext](/powershell/module/az.storage/New-AzStorageContext) | Hiermee wordt een Azure Storage-context gemaakt. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

Meer PowerShell-voorbeeldscripts voor Storage vindt u in de [PowerShell-voorbeelden voor Azure Blob Storage](../blobs/storage-samples-blobs-powershell.md).
