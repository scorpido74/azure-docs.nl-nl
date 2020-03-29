---
title: Een Azure Stream Analytics-taak starten
description: In dit artikel wordt beschreven hoe u een Stream Analytics-taak start vanuit Azure-portal, PowerShell en Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c393eb782c2ff16eb5b3e5967b39938dfe2f1534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426469"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Een Azure Stream Analytics-taak starten

U uw Azure Stream Analytics-taak starten met de Azure-portal, Visual Studio en PowerShell. Wanneer u een taak start, selecteert u een tijd voor de taak om te beginnen met het maken van uitvoer. Azure portal, Visual Studio en PowerShell hebben elk verschillende methoden voor het instellen van de begintijd. Deze methoden worden hieronder beschreven.

## <a name="start-options"></a>Startopties
De drie volgende opties zijn beschikbaar om een taak te starten. Houd er rekening mee dat alle hieronder genoemde tijden zijn die welke zijn opgegeven in [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Als TIMESTAMP BY niet is opgegeven, wordt de aankomsttijd gebruikt.
* **Nu:** Hiermee wordt het beginpunt van de uitvoergebeurtenisstream hetzelfde als wanneer de taak wordt gestart. Als een tijdelijke operator wordt gebruikt (bijvoorbeeld tijdvenster, LAG of JOIN), kijkt Azure Stream Analytics automatisch terug op de gegevens in de invoerbron. Als u bijvoorbeeld een taak 'Nu' start en als uw query een tumbling-venster van 5 minuten gebruikt, zoekt Azure Stream Analytics gegevens van 5 minuten geleden in de invoer.
De eerste mogelijke uitvoergebeurtenis zou een tijdstempel hebben dat gelijk is aan of groter is dan de huidige tijd, en ASA garandeert dat alle invoergebeurtenissen die logischerwijs kunnen bijdragen aan de uitvoer, zijn verantwoord. Er worden bijvoorbeeld geen gedeeltelijke samengevoegde aggregaten gegenereerd. Het is altijd de volledige geaggregeerde waarde.

* **Aangepast:** U het beginpunt van de uitvoer kiezen. Net als bij de optie **Nu** leest Azure Stream Analytics de gegevens voor deze tijd automatisch als een tijdelijke operator wordt gebruikt 

* **Toen voor het laatst gestopt**. Deze optie is beschikbaar wanneer de taak eerder is gestart, maar handmatig is gestopt of is mislukt. Bij het kiezen van deze optie gebruikt Azure Stream Analytics de laatste uitvoertijd om de taak opnieuw op te starten, zodat er geen gegevens verloren gaan. Net als bij eerdere opties leest Azure Stream Analytics de gegevens voor deze tijd automatisch als een tijdelijke operator wordt gebruikt. Aangezien verschillende invoerpartities verschillende tijd kunnen hebben, wordt de vroegste stoptijd van alle partities gebruikt, waardoor sommige duplicaten in de uitvoer kunnen worden gezien. Meer informatie over precies-eenmaal verwerking zijn beschikbaar op de pagina [Event Delivery Garanties](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Azure Portal

Navigeer naar uw taak in de Azure-portal en selecteer **Start** op de overzichtspagina. Selecteer de **begintijd van** de taakuitvoer en selecteer **Start**.

Kies een van de opties voor de begintijd van **de taakuitvoer**. De opties zijn *Nu,* *Aangepast*en, als de taak eerder is uitgevoerd, *wanneer de taak voor het laatst is gestopt*. Zie hierboven voor meer informatie over deze opties.

## <a name="visual-studio"></a>Visual Studio

Selecteer in de taakweergave de groene pijlknop om de taak te starten. Stel de **startmodus voor taakuitvoer** in en selecteer **Start**. De taakstatus wordt gewijzigd in **Uitvoeren**.

Er zijn drie opties voor **de startmodus voor taakuitvoer:** *JobStartTime,* *CustomTime*en *LastOutputEventTime*. Als deze eigenschap afwezig is, is de standaardinstelling *JobStartTime*. Zie hierboven voor meer informatie over deze opties.


## <a name="powershell"></a>PowerShell

Gebruik de volgende cmdlet om uw taak te starten met PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Er zijn drie opties voor **OutputStartMode:** *JobStartTime,* *CustomTime*en *LastOutputEventTime*. Als deze eigenschap afwezig is, is de standaardinstelling *JobStartTime*. Zie hierboven voor meer informatie over deze opties.

Bekijk de referentie `Start-AzStreamAnalyitcsJob` [Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)voor meer informatie over de cmdlet.

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
* [Snelstart: een streamanalytics-taak maken met Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Snelstart: Een Stream Analytics-taak maken met behulp van de Azure Stream Analytics-tools voor Visual Studio](stream-analytics-quick-create-vs.md)
