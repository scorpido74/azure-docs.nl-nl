---
title: Foutopsporing en oplossen van machine learning-pijplijnen in Application Insights
titleSuffix: Azure Machine Learning
description: Voeg logboekregistratie toe aan uw trainings- en batchscoringpijplijnen en bekijk de geregistreerde resultaten in Application Insights.
services: machine-learning
author: aburek
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: 217a89f6ede4e4b1d2182eed79b088808432044f
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529365"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>Foutopsporing en oplossen van machine learning-pijplijnen in Application Insights
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

De [OpenCensus](https://opencensus.io/quickstart/python/) python-bibliotheek kan worden gebruikt om logboeken te routeren naar Application Insights vanuit uw scripts. Door logboeken van pijplijnuitvoeringen op één plaats te aggregeren, u query's opbouwen en problemen diagnosticeren. Met Application Insights u logboeken in de loop van de tijd bijhouden en pijplijnlogboeken vergelijken in verschillende uitvoeringen.

Als u uw logboeken eenmaal op de plaats hebt, wordt een geschiedenis van uitzonderingen en foutmeldingen weergegeven. Aangezien Application Insights integreert met Azure Alerts, u ook waarschuwingen maken op basis van query's met Application Insights.

## <a name="prerequisites"></a>Vereisten

* Volg de stappen om een [Azure Machine Learning-werkruimte](./how-to-manage-workspace.md) te maken en [uw eerste pijplijn te maken](./how-to-create-your-first-pipeline.md)
* [Configureer uw ontwikkelomgeving](./how-to-configure-environment.md) om de Azure Machine Learning SDK te installeren.
* Installeer het pakket [OpenCensus Azure Monitor Exporter](https://pypi.org/project/opencensus-ext-azure/) lokaal:
  ```python
  pip install opencensus-ext-azure
  ```
* Een [instantie Application Insights maken](../azure-monitor/app/opencensus-python.md) (dit document bevat ook informatie over het verkrijgen van de verbindingstekenreeks voor de bron)

## <a name="getting-started"></a>Aan de slag

Deze sectie is een inleiding die specifiek is voor het gebruik van OpenCensus uit een Azure Machine Learning-pijplijn. Zie [OpenCensus Azure Monitor-exporteurs](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure) voor een gedetailleerde zelfstudie

Voeg een PythonScriptStep toe aan uw Azure ML-pijplijn. Configureer [uw RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) met de afhankelijkheid van opencensus-ext-azure. Configureer `APPLICATIONINSIGHTS_CONNECTION_STRING` de omgevingsvariabele.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Maak een bestand met de naam `sample_step.py`. Importeer de klasse AzureLogHandler om logboeken te routeren naar Application Insights. U moet ook de Python Logging-bibliotheek importeren.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Voeg vervolgens de AzureLogHandler toe aan de python-logger.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Logboekregistratie met aangepaste afmetingen
 
Logopte voor logboeken die worden doorgestuurd naar Application Insights hebben standaard niet genoeg context om terug te leiden naar de run of het experiment. Om de logboeken bruikbaar te maken voor het diagnosticeren van problemen, zijn extra velden nodig. 

Als u deze velden wilt toevoegen, kunnen aangepaste dimensies worden toegevoegd om context te bieden aan een logboekbericht. Een voorbeeld is wanneer iemand logboeken in meerdere stappen in dezelfde pijplijnrun wil weergeven.

Aangepaste afmetingen vormen een woordenboek met sleutelwaarde (opgeslagen als tekenreeks, tekenreeks) paren. Het woordenboek wordt vervolgens naar Application Insights verzonden en weergegeven als kolom in de queryresultaten. De afzonderlijke afmetingen kunnen worden gebruikt als [queryparameters](#additional-helpful-queries).

### <a name="helpful-context-to-include"></a>Nuttige context om op te nemen

| Veld                          | Redeneren/Voorbeeld                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Kan logboeken opvragen voor personen met dezelfde parent_run_id om logboeken na verloop van tijd voor alle stappen te bekijken, in plaats van in elke afzonderlijke stap te moeten duiken                                        |
| step_id                        | Kan logboeken opvragen voor logboeken met dezelfde step_id om te zien waar een probleem is opgetreden met een beperkt bereik voor alleen de afzonderlijke stap                                                        |
| step_name                      | Kan logboeken opvragen om stapprestaties in de loop van de tijd te zien. Helpt ook om een step_id te vinden voor recente runs zonder in de portal UI te duiken                                          |
| experiment_name                | Kan in logboeken worden gequeryomd om de prestaties van het experiment in de loop van de tijd te bekijken. Helpt ook bij het vinden van een parent_run_id of step_id voor recente runs zonder te duiken in de portal UI                   |
| run_url                 | Kan een link direct terug naar de run voor onderzoek. |

**Andere nuttige velden**

Deze velden vereisen mogelijk extra code-instrumentatie en worden niet geleverd door de run-context.

| Veld                   | Redeneren/Voorbeeld                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Als u CI/CD gebruikt om te implementeren, kan dit veld logboeken correleren met de codeversie die de stap- en pijplijnlogica heeft opgegeven. Deze koppeling kan verder helpen bij het diagnosticeren van problemen of het identificeren van modellen met specifieke kenmerken (log/metrische waarden) |
| run_type                       | Kan onderscheid maken tussen verschillende modeltypen of training versus het scoren van runs |

### <a name="creating-a-custom-dimensions-dictionary"></a>Een woordenboek Aangepaste afmetingen maken

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", custom_dimensions)
```

## <a name="opencensus-python-logging-considerations"></a>OpenCensus Python-logboekoverwegingen

De OpenCensus AzureLogHandler wordt gebruikt om Python-logboeken door te sturen naar Application Insights. Als gevolg hiervan moet rekening worden gehouden met python-logboeknuances. Wanneer een logger wordt gemaakt, heeft het een standaard logboekniveau en toont logboeken groter dan of gelijk aan dat niveau. Een goede referentie voor het gebruik van Python logging functies is de [Logging Cookbook](https://docs.python.org/3/howto/logging-cookbook.html).

De `APPLICATIONINSIGHTS_CONNECTION_STRING` omgevingsvariabele is nodig voor de OpenCensus-bibliotheek. We raden u aan deze omgevingsvariabele in te stellen in plaats van deze in te stellen als parameter voor pijplijnverbindingen om te voorkomen dat u doorkoppelingstekenreeksen met plaintext wordt doorgegeven.

## <a name="querying-logs-in-application-insights"></a>Logboeken opvragen in toepassingsinzichten

De logboeken die naar Application Insights worden doorgestuurd, worden weergegeven onder 'traces' of 'exceptions'. Zorg ervoor dat u uw tijdvenster aanpast aan de pijplijnuitvoering.

![Resultaat van queryvan toepassingsinzichten](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Het resultaat in Application Insights toont het logboekbericht en -niveau, bestandspad en coderegelnummer. Het toont ook alle aangepaste afmetingen opgenomen. In deze afbeelding worden in het woordenboek aangepasteAfmetingen de sleutel-/waardeparen uit het vorige [codevoorbeeld weergegeven.](#creating-a-custom-dimensions-dictionary)

### <a name="additional-helpful-queries"></a>Aanvullende nuttige query's

Sommige van de onderstaande query's gebruiken 'customDimensions.Level'. Deze ernstniveaus komen overeen met het niveau waarmee het Python-logboek oorspronkelijk is verzonden. Zie Azure Monitor [Log Queries](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)voor aanvullende querygegevens .

| Gebruiksvoorbeeld                                                               | Query’s uitvoeren                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Logboekresultaten voor specifieke aangepaste dimensie, bijvoorbeeld 'parent_run_id' | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Log resultaten voor alle trainingen van de afgelopen 7 dagen                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Resultaten registreren met de fout van ernstNiveau van de afgelopen 7 dagen              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Aantal logboekresultaten met ernstNiveaufout in de afgelopen 7 dagen     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Volgende stappen

Zodra u zich hebt afmeldt in uw instantie Application Insights, kunnen deze worden gebruikt om [Azure Monitor-waarschuwingen](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) in te stellen op basis van queryresultaten.

U ook resultaten van query's toevoegen aan een [Azure-dashboard](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query) voor aanvullende inzichten.