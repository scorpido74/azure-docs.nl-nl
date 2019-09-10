---
title: Bekende problemen bij het oplossen van problemen &
titleSuffix: Azure Machine Learning service
description: Een lijst van de bekende problemen, oplossingen, en probleemoplossing voor Azure Machine Learning-service.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 96af61089f2b7b85d58a8a2ab61936459cef158b
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858690"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Bekende problemen en oplossen van problemen met Azure Machine Learning-service

Dit artikel helpt u bij het zoeken en corrigeer de fouten of fouten opgetreden bij het gebruik van de Azure Machine Learning-service.

## <a name="visual-interface-issues"></a>Problemen met de Visual-Interface

Visuele interface voor problemen met de machine learning-service.

### <a name="long-compute-preparation-time"></a>Tijd van lange reken voorbereiding

Het maken van een nieuwe Compute-of aanroepen duurt een tijd, kan een paar minuten of zelfs langer duren. Het team werkt voor Optima Lise ring.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Kan geen experiment uitvoeren alleen gegevensset bevat 

Mogelijk wilt u een experiment uitvoeren met alleen gegevensset voor het visualiseren van de gegevensset. Het is echter niet toegestaan om een experiment uit te voeren, maar nog geen gegevensset bevat. Dit probleem wordt momenteel opgelost.
 
Vóór de oplossing kunt u de gegevensset koppelen aan een module voor gegevens transformatie (Selecteer kolommen in gegevensset, bewerk de meta gegevens, splits gegevens, enzovoort) en voer het experiment uit. Vervolgens kunt u de gegevensset visualiseren. 

Onder afbeelding ziet u hoe ![: visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Problemen met de SDK-installatie

**Foutbericht: Kan PyYAML niet verwijderen**

Azure Machine Learning SDK voor python: PyYAML is een distutils-geïnstalleerd project. Daarom kunnen we niet nauw keurig bepalen welke bestanden er bij gedeeltelijk verwijderen horen. Gebruik het volgende om door te gaan met het installeren van de SDK tijdens deze fout negeren:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Foutbericht: `ERROR: No matching distribution found for azureml-dataprep-native`**

De python 3.7.4-distributie van Anaconda heeft een fout die de installatie van azureml-SDK verbreekt. Dit probleem wordt besproken in dit [github-probleem](https://github.com/ContinuumIO/anaconda-issues/issues/11195) dat kan worden omzeild door een nieuwe Conda-omgeving te maken met behulp van de volgende opdracht:
```bash
conda create -n <env-name> python=3.7.3
```
Hiermee maakt u een Conda-omgeving met behulp van python 3.7.3, waarvoor het installatie probleem niet aanwezig is in 3.7.4.

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemen bij het maken van Azure Machine Learning-Computing

Er wordt een zeldzaam kans dat sommige gebruikers die hun Azure Machine Learning-werkruimte hebt gemaakt vanuit de Azure-portal voordat u de GA-versie niet mogelijk te maken van Azure Machine Learning-Computing in de werkruimte. U kunt een ondersteuningsaanvraag indient voor de service te verhogen of een nieuwe werkruimte via de Portal of de SDK om de blokkering zelf onmiddellijk te maken.

## <a name="image-building-failure"></a>Installatiekopie samenstellen fout

De installatiekopie van het bouwen van fout bij het implementeren van web-service. Tijdelijke oplossing is om toe te voegen ' pynacl 1.2.1 == "als een afhankelijkheid pip Conda-bestand voor de configuratie van installatiekopie.

## <a name="deployment-failure"></a>Implementatie fout

Als u ziet `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, wijzigt u de SKU voor virtuele machines die in uw implementatie worden gebruikt naar een met meer geheugen.

## <a name="fpgas"></a>FPGA 's

Niet mogelijk om te implementeren van modellen op FPGA's totdat u hebt aangevraagd en goedgekeurd voor FPGA quotum. Vul het aanvraagformulier voor de quota voor het aanvragen van toegang: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Geautomatiseerde Machine Learning

Automatische machine learning flow tensor biedt momenteel geen ondersteuning voor tensor flow-versie 1,13. Als u deze versie installeert, werken de pakket afhankelijkheden niet meer. We werken in een toekomstige release aan het oplossen van dit probleem. 

### <a name="experiment-charts"></a>Grafieken experimenteren

Binaire classificatie grafieken (Precision-intrekken, ROC, winst curve enz.) die worden weer gegeven in automatische ML-experimenten, worden sinds 4/12 niet correct gerenderd in de gebruikers interface. In grafiek grafieken worden momenteel inverse resultaten weer gegeven, waarbij betere modellen worden weer gegeven met lagere resultaten. Een oplossing wordt onderzocht.

## <a name="databricks"></a>Databricks

Problemen met Databricks en Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Fout bij het installeren van pakketten

Azure Machine Learning SDK-installatie mislukt op Azure Databricks wanneer er meer pakketten zijn geïnstalleerd. Sommige pakketten, zoals `psutil`, kan leiden tot conflicten. Installeer pakketten door de bibliotheek versie te blok keren om installatie fouten te voor komen. Dit probleem heeft betrekking op Databricks en niet op de Azure Machine Learning Service-SDK. Dit probleem kan ook optreden met andere bibliotheken. Voorbeeld:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

U kunt ook init-scripts gebruiken als u problemen ondervindt bij de installatie van python-bibliotheken. Deze aanpak wordt niet officieel ondersteund. Zie voor meer informatie [cluster-scoped init-scripts](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Een automatische machine learning uitvoering annuleren

Wanneer u gebruikmaakt van geautomatiseerde machine learning mogelijkheden op Azure Databricks, kunt u een uitvoering annuleren en een nieuwe uitvoering van het experiment starten door het Azure Databricks cluster opnieuw op te starten.

### <a name="10-iterations-for-automated-machine-learning"></a>10 herhalingen > voor automatische machine learning

Als u in automatische machine learning-instellingen meer dan 10 iteraties hebt, moet `show_output` u `False` deze instellen op wanneer u de uitvoering verzendt.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget voor de Azure Machine Learning SDK/automatische machine learning

De Azure Machine Learning SDK-widget wordt niet ondersteund in een Databricks-notebook omdat de notebooks geen HTML-widgets kunnen parseren. U kunt de widget in de portal weer geven met behulp van deze python-code in uw Azure Databricks notebook-cel:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Fout bij importeren: Geen module met de naam ' Pandas. core. indices '

Als u deze fout ziet wanneer u automatische machine learning gebruikt:

1. Voer deze opdracht uit om twee pakketten te installeren in uw Azure Databricks cluster: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Ontkoppel het cluster en koppel het vervolgens opnieuw aan uw notitie blok. 

Als u met deze stappen het probleem niet kunt oplossen, probeert u het cluster opnieuw op te starten.

### <a name="failtosendfeather"></a>FailToSendFeather

Als er een `FailToSendFeather` fout wordt weer gegeven bij het lezen van gegevens op Azure Databricks cluster, raadpleegt u de volgende oplossingen:

* Upgrade `azureml-sdk[automl_databricks]` pakket naar de nieuwste versie.
* Voeg `azure-dataprep` versie 1.1.8 of hoger toe.
* Voeg `pyarrow` versie 0,11 of hoger toe.

## <a name="azure-portal"></a>Azure Portal

Als u rechtstreeks naar het weergeven van uw werkruimte van een koppeling voor het delen van de SDK of de portal gaat, wordt het niet mogelijk om de normale overzichtspagina met abonnementsgegevens in de extensie weer te geven. U wordt ook niet mogelijk om over te schakelen naar een andere werkruimte. Als u een andere werk ruimte wilt weer geven, kunt u de tijdelijke oplossing rechtstreeks naar de [Azure Portal](https://portal.azure.com) gaan en zoeken naar de naam van de werk ruimte.

## <a name="diagnostic-logs"></a>Diagnostische logboeken

Soms kan het handig zijn als u diagnostische gegevens opgeven kunt wanneer u hulp vragen. Als u bepaalde logboeken wilt zien, gaat u naar [Azure Portal](https://portal.azure.com) en gaat u naar uw werk ruimte en selecteert u **werk ruimte > experiment > > Logboeken uit te voeren**.  U kunt deze informatie ook vinden in de sectie **experimenten** van de [landings pagina van de werk ruimte (preview)](https://ml.azure.com).

> [!NOTE]
> Azure Machine Learning-service registreert gegevens uit verschillende bronnen tijdens de training, zoals AutoML of de docker-container die de trainings taak uitvoert. Veel van deze logboeken zijn niet gedocumenteerd. Als u problemen ondervindt en contact opneemt met micro soft ondersteuning, kunnen ze deze logboeken gebruiken tijdens het oplossen van problemen.

## <a name="activity-logs"></a>Activiteitenlogboeken

Sommige acties in de Azure Machine Learning-werk ruimte registreren geen gegevens in het __activiteiten logboek__. U kunt bijvoorbeeld een training starten of een model registreren.

Sommige van deze acties worden weer gegeven in het gebied __activiteiten__ van uw werk ruimte, maar ze geven niet aan wie de activiteit heeft gestart.

## <a name="resource-quotas"></a>Resourcequota

Meer informatie over de [resourcequota](how-to-manage-quotas.md) optreden tijdens het werken met Azure Machine Learning.

## <a name="authentication-errors"></a>Verificatiefouten

Als u een beheer bewerking uitvoert op een reken doel van een externe taak, wordt een van de volgende fouten weer gegeven:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

U ontvangt bijvoorbeeld een fout melding als u een reken doel probeert te maken of koppelen van een ML-pijp lijn die wordt ingediend voor externe uitvoering.

## <a name="overloaded-azurefile-storage"></a>Overbelaste AzureFile-opslag

Als er een fout `Unable to upload project files to working directory in AzureFile because the storage is overloaded`bericht wordt weer gegeven, moet u de volgende tijdelijke oplossingen Toep assen.

Als u bestands share gebruikt voor andere werk belastingen, zoals gegevens overdracht, is de aanbeveling om blobs te gebruiken zodat de bestands share gratis kan worden gebruikt voor het verzenden van uitvoeringen. U kunt de werk belasting ook splitsen tussen twee verschillende werk ruimten.
