---
title: 'Power shell: een app-back-up verwijderen'
description: Meer informatie over het gebruik van Azure PowerShell voor het automatiseren van de implementatie en het beheer van App Service. In dit voor beeld ziet u hoe u een app-back-up verwijdert.
author: msangapu-msft
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 93b53bedef291b9aa740f5bbe9c492185f035f89
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80044841"
---
# <a name="delete-a-backup-for-a-web-using-azure-powershell"></a>Een back-up van een web-app verwijderen met Azure PowerShell

Met dit voorbeeldscript wordt er een web-app gemaakt in App Service, inclusief de bijbehorende resources, en wordt er vervolgens een eenmalige back-up van de app gemaakt. 

Als u dit script wilt uitvoeren, moet u een bestaande back-up voor een web-app hebben. Instructies voor het maken van een back-up vindt u in [Een back-up van een web-app maken ](powershell-backup-onetime.md) of [Een geplande back-up voor een web-app maken](powershell-backup-scheduled.md).

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het voorbeeldscript is uitgevoerd, kan de volgende opdracht worden gebruikt om de resourcegroep, web-app en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Hiermee vraagt u een lijst met back-ups op voor een web-app. |
| [Remove-AzWebAppBackup](/powershell/module/az.websites/remove-azwebappbackup) | Hiermee verwijdert u de opgegeven back-up van een web-app. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

Meer voorbeelden voor Azure Powershell voor Azure App Service Web Apps vindt u in de [voorbeelden van Azure PowerShell](../samples-powershell.md).
