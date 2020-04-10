---
title: Azure Stream Analytics-taken bewaken en beheren met PowerShell
description: In dit artikel wordt beschreven hoe u Azure PowerShell en cmdlets gebruiken om Azure Stream Analytics-taken te controleren en te beheren.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 295141dfd9b84428e2ee69354ab0c249fa46d1b6
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998890"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Stream Analytics-taken bewaken en beheren met Azure PowerShell-cmdlets
Meer informatie over het bewaken en beheren van Stream Analytics-bronnen met Azure PowerShell-cmdlets en powershell-scripting waarmee basistaken van Stream Analytics worden uitgevoerd.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Voorwaarden voor het uitvoeren van Azure PowerShell-cmdlets voor Stream Analytics
* Maak een Azure Resource Group in uw abonnement. Het volgende is een voorbeeld van Azure PowerShell-script. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview)voor Azure PowerShell-informatie ;  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1.0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription -SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Stream Analytics-taken die programmatisch zijn gemaakt, hebben geen standaard bewaking ingeschakeld.  U de bewaking in de Azure Portal handmatig inschakelen door naar de monitorpagina van de taak te navigeren en op de knop Inschakelen te klikken of u dit programmatisch doen door de stappen te volgen die zich bij [Azure Stream Analytics bevinden - Stream Analytics-taken programmatisch controleren.](stream-analytics-monitor-jobs.md)
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell-cmdlets voor Stream Analytics
De volgende Azure PowerShell-cmdlets kunnen worden gebruikt om Azure Stream Analytics-taken te controleren en te beheren. Houd er rekening mee dat Azure PowerShell verschillende versies heeft. 
**In de voorbeelden die de eerste opdracht wordt vermeld, is voor Azure PowerShell 0.9.8 de tweede opdracht voor Azure PowerShell 1.0.** De Azure PowerShell 1.0-opdrachten hebben altijd 'Az' in de opdracht.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzstreamAnalyticsJob
Hiermee worden alle Stream Analytics-taken weergegeven die zijn gedefinieerd in het Azure-abonnement of de opgegeven brongroep, of wordt taakinformatie over een specifieke taak binnen een resourcegroep opgevraagd.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

Met deze opdracht PowerShell retourneert u informatie over alle Stream Analytics-taken in het Azure-abonnement.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Met deze opdracht PowerShell retourneert u informatie over alle Stream Analytics-taken in de brongroep StreamAnalytics-Default-Central-US.

**Voorbeeld 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Met deze PowerShell-opdracht retourneert u informatie over de stream analytics-taak StreamingJob in de brongroep StreamAnalytics-Default-Central--US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Bevat alle ingangen die zijn gedefinieerd in een opgegeven Stream Analytics-taak of informatie over een specifieke invoer.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Met deze Opdracht PowerShell retourneert u informatie over alle ingangen die zijn gedefinieerd in de taak StreamingJob.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Met deze opdracht PowerShell retourneert u informatie over de invoer met de naam EntryStream die is gedefinieerd in de taak StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-azstreamAnalyticsoutput
Geeft een overzicht van alle uitvoer die zijn gedefinieerd in een opgegeven Stream Analytics-taak of informatie over een specifieke uitvoer.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Met deze PowerShell-opdracht retourneert u informatie over de uitvoer die is gedefinieerd in de taak StreamingJob.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Met deze opdracht PowerShell retourneert u informatie over de uitvoer met de naam Uitvoer die is gedefinieerd in de taak StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Hier vindt u informatie over het quotum van streaming-eenheden in een bepaald gebied.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

Met deze PowerShell-opdracht wordt informatie geretourneerd over het quotum en het gebruik van streaming-eenheden in de regio Centraal-VS.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-azstreamAnalyticsTransformatie
Hier vindt u informatie over een specifieke transformatie die is gedefinieerd in een Stream Analytics-taak.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Met deze opdracht PowerShell retourneert u informatie over de transformatie met de naam StreamingJob in de taak StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>Nieuw-AzureStreamAnalyticsInput | Nieuw-azstreamAnalyticsInput
Hiermee maakt u een nieuwe invoer binnen een Stream Analytics-taak of wordt een bestaande opgegeven invoer bijgewerkt.

De naam van de invoer kan worden opgegeven in het .json-bestand of op de opdrachtregel. Als beide zijn opgegeven, moet de naam op de opdrachtregel dezelfde zijn als die in het bestand.

Als u een invoer opgeeft die al bestaat en de parameter -Force niet opgeeft, vraagt de cmdlet of de bestaande invoer al dan niet moet worden vervangen.

Als u de parameter -Force opgeeft en een bestaande invoernaam opgeeft, wordt de invoer zonder bevestiging vervangen.

Raadpleeg de sectie [Input (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] van de STREAM [Analytics Management REST API-naslagbibliotheek][stream.analytics.rest.api.reference]voor gedetailleerde informatie over de JSON-bestandsstructuur en -inhoud.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Met deze powershell-opdracht wordt een nieuwe invoer gemaakt van het bestand Input.json. Als een bestaande invoer met de naam die is opgegeven in het invoerdefinitiebestand al is gedefinieerd, vraagt de cmdlet of deze al dan niet moet worden vervangen.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Met deze powershell-opdracht wordt een nieuwe invoer gemaakt in de taak EntryStream. Als een bestaande invoer met deze naam al is gedefinieerd, zal de cmdlet vragen of deze al dan niet moet worden vervangen.

**Voorbeeld 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Met deze opdracht PowerShell wordt de definitie van de bestaande invoerbron genaamd EntryStream vervangen door de definitie uit het bestand.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>Nieuwe AzureStreamAnalyticsJob | Nieuwe-AzstreamAnalyticsJob
Hiermee maakt u een nieuwe Stream Analytics-taak in Microsoft Azure of wordt de definitie van een bestaande opgegeven taak bijgewerkt.

De naam van de taak kan worden opgegeven in het .json-bestand of op de opdrachtregel. Als beide zijn opgegeven, moet de naam op de opdrachtregel dezelfde zijn als die in het bestand.

Als u een taaknaam opgeeft die al bestaat en de parameter -Force niet opgeeft, vraagt de cmdlet of de bestaande taak al dan niet moet worden vervangen.

Als u de parameter -Force opgeeft en een bestaande taaknaam opgeeft, wordt de taakdefinitie zonder bevestiging vervangen.

Raadpleeg de sectie [Stream Analytics-taak maken][msdn-rest-api-create-stream-analytics-job] van de [API-naslagbibliotheek voor Stream Analytics-beheer.][stream.analytics.rest.api.reference]

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Met deze opdracht PowerShell wordt een nieuwe taak gemaakt vanuit de definitie in JobDefinition.json. Als een bestaande taak met de naam die is opgegeven in het taakdefinitiebestand al is gedefinieerd, wordt in de cmdlet gevraagd of deze al dan niet moet worden vervangen.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Deze PowerShell-opdracht vervangt de taakdefinitie voor StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>Nieuwe AzureStreamAnalyticsOutput | Nieuwe azstreamAnalyticsoutput
Hiermee maakt u een nieuwe uitvoer binnen een Stream Analytics-taak of wordt een bestaande uitvoer bijgewerkt.  

De naam van de uitvoer kan worden opgegeven in het .json-bestand of op de opdrachtregel. Als beide zijn opgegeven, moet de naam op de opdrachtregel dezelfde zijn als die in het bestand.

Als u een uitvoer opgeeft die al bestaat en de parameter -Force niet opgeeft, vraagt de cmdlet of de bestaande uitvoer al dan niet moet worden vervangen.

Als u de parameter -Force opgeeft en een bestaande uitvoernaam opgeeft, wordt de uitvoer zonder bevestiging vervangen.

Raadpleeg de sectie [Uitvoer (Azure Stream Analytics) maken][msdn-rest-api-create-stream-analytics-output] van de [API-naslagbibliotheek voor Stream Analytics Management.][stream.analytics.rest.api.reference]

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Met deze PowerShell-opdracht wordt een nieuwe uitvoer gemaakt met de naam 'uitvoer' in de taak StreamingJob. Als een bestaande uitvoer met deze naam al is gedefinieerd, zal de cmdlet vragen of deze al dan niet moet worden vervangen.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Deze PowerShell-opdracht vervangt de definitie voor 'uitvoer' in de taak StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>Nieuwe AzureStreamAnalyticsTransformatie | Nieuwe azstreamAnalyticsTransformatie
Hiermee maakt u een nieuwe transformatie binnen een Stream Analytics-taak of wordt de bestaande transformatie bijgewerkt.

De naam van de transformatie kan worden opgegeven in het .json-bestand of op de opdrachtregel. Als beide zijn opgegeven, moet de naam op de opdrachtregel dezelfde zijn als die in het bestand.

Als u een transformatie opgeeft die al bestaat en de parameter -Force niet opgeeft, vraagt de cmdlet of de bestaande transformatie al dan niet moet worden vervangen.

Als u de parameter -Force opgeeft en een bestaande transformatienaam opgeeft, wordt de transformatie zonder bevestiging vervangen.

Raadpleeg de sectie [Transformatie (Azure Stream Analytics) maken][msdn-rest-api-create-stream-analytics-transformation] van de [API-naslagbibliotheek voor Stream Analytics Management.][stream.analytics.rest.api.reference]

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Met deze Opdracht PowerShell wordt een nieuwe transformatie gemaakt genaamd StreamingJobTransform in de taak StreamingJob. Als een bestaande transformatie al met deze naam is gedefinieerd, zal de cmdlet vragen of deze al dan niet moet worden vervangen.

**Voorbeeld 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 Deze PowerShell-opdracht vervangt de definitie van StreamingJobTransform in de taak StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Verwijderen-AzureStreamAnalyticsInput | Bron van azstreamanalyticsverwijderen
Asynchroon verwijdert een specifieke invoer uit een Stream Analytics-taak in Microsoft Azure.  
Als u de parameter -Force opgeeft, wordt de invoer zonder bevestiging verwijderd.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Met deze PowerShell-opdracht wordt de invoer EventStream in de taak StreamingJob verwijderd.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Verwijderen-AzureStreamAnalyticsJob | Verwijderen-azstreamAnalyticsJob
Asynchroon verwijdert een specifieke Stream Analytics-taak in Microsoft Azure.  
Als u de parameter -Force opgeeft, wordt de taak zonder bevestiging verwijderd.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Met deze PowerShell-opdracht wordt de taak StreamingJob verwijderd.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Verwijderen-AzureStreamAnalyticsOutput | Verwijderen-azstreamAnalyticsoutput
Asynchroon verwijdert een specifieke uitvoer uit een Stream Analytics-taak in Microsoft Azure.  
Als u de parameter -Force opgeeft, wordt de uitvoer zonder bevestiging verwijderd.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Met deze PowerShell-opdracht wordt de uitvoeruitvoer in de taak StreamingJob verwijderd.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-azstreamAnalyticsJob
Asynchroon implementeert en start een Stream Analytics-taak in Microsoft Azure.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Met deze PowerShell-opdracht wordt de taak StreamingJob gestart met een aangepaste begintijd van de uitvoer ingesteld op 12 december 2012, 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzstreamAnalyticsJob
Asynchrone stopt een Stream Analytics-taak in Microsoft Azure en de-wijst resources die werden gebruikt. De taakdefinitie en metagegevens blijven beschikbaar binnen uw abonnement via zowel de Azure-portal als de beheer-API's, zodat de taak kan worden bewerkt en opnieuw kan worden gestart. Er worden geen kosten in rekening gebracht voor een taak in de gestopte status.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Met deze PowerShell-opdracht wordt de taak StreamingJob gestopt.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-azstreamAnalyticsInput
Test de mogelijkheid van Stream Analytics om verbinding te maken met een bepaalde invoer.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Met deze PowerShell-opdracht wordt de verbindingsstatus van de invoerEntryStream in StreamingJob getest.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-azstreamAnalyticsoutput
Test de mogelijkheid van Stream Analytics om verbinding te maken met een bepaalde uitvoer.

**Voorbeeld 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Met deze PowerShell-opdracht wordt de verbindingsstatus van de uitvoeruitvoer in StreamingJob getest.  

## <a name="get-support"></a>Ondersteuning krijgen
Probeer ons Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp. 

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: https://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

