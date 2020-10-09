---
title: .NET gebruiken voor Apache Spark
description: Meer informatie over het gebruik van .NET en Apache Spark voor batch verwerking, realtime streaming, machine learning en het schrijven van ad-hoc-query's in azure Synapse Analytics-notebooks.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 4b512d0817c56bc2daabff057c8bc4aa1afa1dee
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826504"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>.NET gebruiken voor Apache Spark met Azure Synapse Analytics

[.Net voor Apache Spark](https://dot.net/spark) biedt gratis, open-source en platformoverschrijdende .net-ondersteuning voor Spark. 

Het biedt .NET-bindingen voor Spark, waarmee u Spark-Api's kunt openen via C# en F #. Met .NET voor Apache Spark kunt u ook door de gebruiker gedefinieerde functies schrijven en uitvoeren voor Spark geschreven in .NET. Met de .NET-Api's voor Spark kunt u toegang krijgen tot alle aspecten van Spark-DataFrames die u helpen bij het analyseren van uw gegevens, waaronder Spark SQL, Delta Lake en Structured streaming.

U kunt gegevens met .NET voor Apache Spark analyseren met behulp van Spark batch-taak definities of met interactieve Azure Synapse Analytics-notebooks. In dit artikel leert u hoe u .NET kunt gebruiken voor Apache Spark met Azure Synapse met behulp van beide technieken.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Batch-taken verzenden met de Spark-taak definitie

Ga naar de zelf studie voor meer informatie over het gebruik van Azure Synapse Analytics voor het [maken van Apache Spark taak definities voor Synapse Spark-Pools](apache-spark-job-definitions.md). Als u uw app niet hebt ingepakt om naar Azure Synapse te verzenden, voert u de volgende stappen uit.

1. Voer de volgende opdrachten uit om uw app te publiceren. Zorg ervoor dat u *mySparkApp* vervangt door het pad naar uw app.

   **In Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r win-x64
   ```
   
   **Op Linux:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.16.04-x64
   ```

2. Geef de inhoud van de map Publish op, `publish.zip` bijvoorbeeld die is gemaakt als gevolg van stap 1. Alle assembly's moeten zich in de eerste laag van het ZIP-bestand bevinden en er mag geen tussenliggende laagmap zijn. Dit betekent dat wanneer u uitpakt `publish.zip` alle assembly's worden geëxtraheerd naar uw huidige werkmap.

    **In Windows:**

Gebruik een extractie programma, zoals [7-zip](https://www.7-zip.org/) of [WinZip](https://www.winzip.com/), om het bestand uit te pakken in de bin-map met alle gepubliceerde binaire bestanden.

     **On Linux:**

     1. Open a bash shell and cd into the bin directory with all the published binaries and run the following command.

       ```bash
       zip -r publish.zip
       ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET voor Apache Spark in azure Synapse Analytics-notebooks 

Notebooks zijn een uitstekende optie voor het maken van prototypen van uw .NET voor Apache Spark pijp lijnen en scenario's. U kunt aan de slag gaan met het werken met, het filteren, weer geven en visualiseren van uw gegevens. 

Data engineers, gegevens wetenschappers, bedrijfs analisten en machine learning engineers kunnen samen werken via een gedeeld, interactief document. U ziet direct resultaten van het verkennen van gegevens en kan uw gegevens in hetzelfde notitie blok visualiseren.

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>.NET gebruiken voor Apache Spark-notebooks

Wanneer u een nieuw notitie blok maakt, kiest u een taal-kernel waarmee u uw bedrijfs logica wilt uitdrukken. Kernel-ondersteuning is beschikbaar voor verschillende talen, waaronder C#.

Als u .NET wilt gebruiken voor Apache Spark in uw Azure Synapse Analytics-notebook, selecteert u **.net Spark (C#)** als uw kernel en koppelt u het notitie blok aan een bestaande Spark-groep.

De .NET Spark-notebook is gebaseerd op de interactieve .NET-ervaring en biedt interactieve C#-ervaringen met de mogelijkheid om .NET voor Spark uit het vak te gebruiken, waarbij de Spark-sessie variabele `spark` al vooraf is gedefinieerd.

### <a name="net-for-apache-spark-c-kernel-features"></a>.NET for Apache Spark C#-kernel-functies

De volgende functies zijn beschikbaar wanneer u .NET for Apache Spark gebruikt in de Azure Synapse Analytics-notebook:

* Declaratieve HTML: uitvoer van uw cellen genereren met behulp van HTML-syntaxis, zoals kopteksten, lijsten met opsommings tekens en zelfs afbeeldingen weer geven.
* Eenvoudige C#-instructies (zoals toewijzingen, afdrukken op console, uitzonde ringen genereren, enzovoort).
* Meerdere regels C# code blokken (zoals if-instructies, foreach-lussen, klassedefinities, enzovoort).
* Toegang tot de Standard C#-bibliotheek (zoals System, LINQ, overzichten, enzovoort).
* Ondersteuning voor [C# 8,0-taal functies](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Spark als een vooraf gedefinieerde variabele om u toegang te geven tot uw Apache Spark-sessie.
* Ondersteuning voor het definiëren van [door de gebruiker gedefinieerde .NET-functies die in Apache Spark kunnen worden uitgevoerd](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Ondersteuning voor het visualiseren van uitvoer van uw Spark-taken met behulp van verschillende grafieken (zoals een lijn, staaf of histogram) en indelingen (zoals één, overlappend, enzovoort) met behulp van de- `XPlot.Plotly` bibliotheek.
* De mogelijkheid om NuGet-pakketten toe te voegen aan uw C#-notebook.

## <a name="next-steps"></a>Volgende stappen

* [Documentatie voor .NET voor Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET interactief](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
