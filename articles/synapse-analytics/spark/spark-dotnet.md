---
title: .NET gebruiken voor Apache Spark met Azure Synapse Analytics
description: Meer informatie over het gebruik van .NET en Apache Spark voor batch verwerking, realtime streaming, machine learning en het schrijven van ad-hoc-query's in azure Synapse Analytics-notebooks.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430511"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>.NET gebruiken voor Apache Spark met Azure Synapse Analytics

[.Net voor Apache Spark](https://dot.net/spark) is gratis, open-source en platformoverschrijdende .net-ondersteuning voor Spark. .NET voor Apache Spark biedt .NET-bindingen voor Spark, waarmee u toegang tot Spark-Api's kunt krijgen via C# en F #. Met .NET voor Apache Spark hebt u de mogelijkheid om door de gebruiker gedefinieerde functies te schrijven en uit te voeren voor Spark met behulp van .NET. Met de .NET-Api's voor Spark kunt u toegang krijgen tot alle aspecten van Spark waarmee u uw gegevens kunt analyseren, waaronder Spark SQL en Structured streaming.

U kunt gegevens met .NET voor Apache Spark analyseren met behulp van Spark batch-taak definities of met interactieve Azure Synapse Analytics-notebooks. In dit artikel leert u hoe u .NET kunt gebruiken voor Apache Spark met Azure Synapse met behulp van beide technieken. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Batch-taken verzenden met de Spark-taak definitie

Ga naar de zelf studie voor meer informatie over het gebruik van Azure Synapse Analytics voor het [maken van Apache Spark taak definities voor Synapse Spark-Pools](apache-spark-job-definitions.md). Als u uw app niet hebt ingepakt voor verzen ding naar Azure Synapse, voert u de volgende stappen uit.

1. Voer de volgende opdrachten uit om uw app te publiceren. Zorg ervoor dat u *mySparkApp* vervangt door het pad naar uw app.

   **In Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **Op Linux:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET voor Apache Spark in azure Synapse Analytics-notebooks

Wanneer u een nieuw notitie blok maakt, kiest u een taal-kernel waarmee u uw bedrijfs logica wilt uitdrukken. Er wordt kernel-ondersteuning geboden voor verschillende talen, waaronder C#.

Als u .NET wilt gebruiken voor Apache Spark in uw Azure Synapse Analytics-notebook, selecteert u **.net Spark (C#)** als uw kernel en koppelt u het notitie blok aan een bestaande Spark-groep.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET voor Apache Spark in azure Synapse Analytics-notebooks 

Notebooks zijn een uitstekende optie voor het maken van prototypen van uw .NET voor Apache Spark pijp lijnen en scenario's. U kunt aan de slag gaan met het werken met, het filteren, weer geven en visualiseren van uw gegevens. Data engineers, gegevens wetenschappers, bedrijfs analisten en machine learning engineers kunnen samen werken via een gedeeld, interactief document. U ziet direct resultaten van het verkennen van gegevens en kan uw gegevens in hetzelfde notitie blok visualiseren.

### <a name="how-to-use-notebooks"></a>Notitie blokken gebruiken

Wanneer u een nieuw notitie blok maakt, kiest u een taal-kernel waarmee u uw bedrijfs logica wilt uitdrukken. Er wordt kernel-ondersteuning geboden voor verschillende talen, waaronder C#. 

Als u .NET wilt gebruiken voor Apache Spark in uw Azure Synapse Analytics-notebook, selecteert u **.net Spark (C#)** als uw kernel en koppelt u het notitie blok aan een bestaande Spark-groep. 

De .NET Spark-notebook is gebaseerd op de interactieve .NET-ervaring en biedt interactieve C#-ervaringen met de mogelijkheid om .NET voor Spark uit het vak te gebruiken, waarbij `spark` de Spark-sessie variabele al vooraf is gedefinieerd.

### <a name="sparknet-c-kernel-features"></a>Spark.NET C#-kernel-functies

De volgende functies zijn beschikbaar wanneer u .NET for Apache Spark gebruikt in de Azure Synapse Analytics-notebook:

* Declaratieve HTML: uitvoer van uw cellen genereren met behulp van HTML-syntaxis, zoals kopteksten, lijsten met opsommings tekens en zelfs afbeeldingen weer geven.
* Eenvoudige C#-instructies (zoals toewijzingen, afdrukken op console, uitzonde ringen genereren, enzovoort).
* Meerdere regels C# code blokken (zoals if-instructies, foreach-lussen, klassedefinities, enzovoort).
* Toegang tot de Standard C#-bibliotheek (zoals System, LINQ, overzichten, enzovoort).
* Ondersteuning voor [C# 8,0-taal functies](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Spark als een vooraf gedefinieerde variabele om u toegang te geven tot uw Apache Spark-sessie.
* Ondersteuning voor het definiëren van [door de gebruiker gedefinieerde .NET-functies die in Apache Spark kunnen worden uitgevoerd](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Ondersteuning voor het visualiseren van uitvoer van uw Spark-taken met behulp van verschillende grafieken (zoals een lijn, staaf of histogram) en indelingen (zoals één, overlappend, enzovoort) `XPlot.Plotly` met behulp van de-bibliotheek.
* De mogelijkheid om NuGet-pakketten toe te voegen aan uw C#-notebook.

## <a name="next-steps"></a>Volgende stappen

* [Documentatie voor .NET for Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET interactief](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)