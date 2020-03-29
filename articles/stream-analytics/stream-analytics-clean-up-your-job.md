---
title: Uw Azure Stream Analytics-taak opschonen
description: In dit artikel ziet u verschillende methoden voor het verwijderen van uw Azure Stream Analytics-taken.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d99920417f20034da1001a821c02376ac19274d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426491"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Uw Azure Stream Analytics-taak stoppen of verwijderen

Azure Stream Analytics-taken kunnen eenvoudig worden gestopt of verwijderd via de Azure-portal, Azure PowerShell, Azure SDK voor .Net of REST API. Een Stream Analytics-taak kan niet worden hersteld nadat deze is verwijderd.

>[!NOTE] 
>Wanneer u uw Stream Analytics-taak stopt, blijven de gegevens alleen bestaan in de invoer- en uitvoeropslag, zoals Gebeurtenishubs of Azure SQL Database. Als u gegevens uit Azure moet verwijderen, moet u het verwijderingsproces voor de invoer- en uitvoerbronnen van uw Stream Analytics-taak volgen.

## <a name="stop-a-job-in-azure-portal"></a>Een taak stoppen in Azure-portal

Wanneer u een taak stopt, worden de resources gedeprovisioneerd en worden gebeurtenissen niet meer verwerkt. Kosten in verband met deze taak worden ook gestopt. Maar al uw configuratie wordt bewaard en u de taak later opnieuw opstarten 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 

2. Zoek je lopende Stream Analytics-taak en selecteer deze.

3. Selecteer Op de vacaturepagina Stream Analytics de optie **Stoppen** om de taak te stoppen. 

   ![Azure Stream Analytics-taak stoppen](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Een taak in Azure-portal verwijderen

>[!WARNING] 
>Een Stream Analytics-taak kan niet worden hersteld nadat deze is verwijderd.

1. Meld u aan bij Azure Portal. 

2. Zoek uw bestaande Stream Analytics-taak en selecteer deze.

3. Selecteer **verwijderen** om de taak te verwijderen op de vacaturepagina Stream Analytics. 

   ![Azure Stream Analytics-taak verwijderen](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Een taak stoppen of verwijderen met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u een taak met PowerShell wilt stoppen, gebruikt u de cmdlet [Stop-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) Als u een taak wilt verwijderen met PowerShell, gebruikt u de cmdlet [Remove-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob)

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Een taak stoppen of verwijderen met Azure SDK voor .NET

Als u een taak wilt stoppen met Azure SDK voor .NET, gebruikt u de methode [StreamingJobsOperationsExtensions.BeginStop.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) Als u een taak wilt verwijderen met Azure SDK voor .NET, wordt de methode [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) verwijderd.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Een taak stoppen of verwijderen met rest-API

Als u een taak wilt stoppen met DE API VAN REST, raadpleegt u de methode [Stoppen.](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) Als u een taak wilt verwijderen met REST API, raadpleegt u de methode [Verwijderen.](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete)
