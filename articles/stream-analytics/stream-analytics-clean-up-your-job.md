---
title: Opschonen van uw Azure Stream Analytics-taak
description: In dit artikel ziet u verschillende methoden voor het verwijderen van uw Azure Stream Analytics-taken.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d99920417f20034da1001a821c02376ac19274d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426491"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Uw Azure Stream Analytics-taak stoppen of verwijderen

Azure Stream Analytics-taken kunnen eenvoudig worden gestopt of verwijderd via de Azure Portal, Azure PowerShell, Azure SDK voor .net of REST API. Een Stream Analytics taak kan niet worden hersteld als deze eenmaal is verwijderd.

>[!NOTE] 
>Wanneer u de Stream Analytics-taak stopt, de gegevens zich blijft voordoen alleen in de invoer- en opslag, zoals Event Hubs of Azure SQL Database. Als u gegevens uit Azure verwijdert moet, moet u de verwijdering processen voor de invoer- en bronnen van uw Stream Analytics-taak volgen.

## <a name="stop-a-job-in-azure-portal"></a>Een taak in Azure portal stoppen

Wanneer u een taak stopt, wordt de inrichting van de resources ongedaan gemaakt en stopt het verwerken van gebeurtenissen. Kosten die betrekking hebben op deze taak worden ook gestopt. Al uw configuratie wordt echter opgeslagen en u kunt de taak later opnieuw starten 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 

2. Zoek uw actieve Stream Analytics-taak en selecteert u deze.

3. Selecteer op de pagina van de Stream Analytics-taak **stoppen** om de taak te stoppen. 

   ![Azure Stream Analytics-taak stoppen](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Een taak in Azure portal verwijderen

>[!WARNING] 
>Een Stream Analytics taak kan niet worden hersteld als deze eenmaal is verwijderd.

1. Meld u aan bij Azure Portal. 

2. Zoek uw bestaande Stream Analytics-taak en selecteert u deze.

3. Selecteer op de pagina van de Stream Analytics-taak **verwijderen** om de taak te verwijderen. 

   ![Azure Stream Analytics-taak verwijderen](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Beëindigt of verwijdert u een taak met behulp van PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u een taak wilt stoppen met behulp van Power shell, gebruikt u de cmdlet [Stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) . Als u een taak wilt verwijderen met behulp van Power shell, gebruikt u de cmdlet [Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) .

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Beëindigt of verwijdert u een taak met Azure SDK voor .NET

Als u wilt stoppen van een taak met Azure SDK voor .NET, gebruiken de [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) methode. Verwijderen van een taak met Azure SDK voor .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) methode.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Beëindigt of verwijdert u een taak met behulp van REST-API

Als u wilt stoppen van een taak met behulp van REST-API, raadpleegt u de [stoppen](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) methode. Als u wilt verwijderen van een taak met behulp van REST-API, raadpleegt u de [verwijderen](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) methode.
