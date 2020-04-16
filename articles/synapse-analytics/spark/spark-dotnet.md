---
title: .NET voor Apache Spark gebruiken met Azure Synapse Analytics
description: Meer informatie over het gebruik van .NET en Apache Spark voor batchverwerking, realtime streaming, machine learning en het schrijven van ad-hocquery's in Azure Synapse Analytics-notitieblokken.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430511"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>.NET voor Apache Spark gebruiken met Azure Synapse Analytics

[.NET voor Apache Spark](https://dot.net/spark) is gratis, open-source en cross-platform .NET-ondersteuning voor Spark. .NET for Apache Spark biedt .NET-bindingen voor Spark waarmee u toegang hebt tot Spark-API's via C# en F#. Met .NET voor Apache Spark hebt u de mogelijkheid om door de gebruiker gedefinieerde functies voor Spark te schrijven en uit te voeren met behulp van .NET. Met de .NET API's voor Spark hebt u toegang tot alle aspecten van Spark waarmee u uw gegevens analyseren, waaronder Spark SQL en Structured Streaming.

U gegevens analyseren met .NET voor Apache Spark via sparkbatchtaakdefinities of met interactieve Azure Synapse Analytics-notitieblokken. In dit artikel leert u hoe u .NET voor Apache Spark met Azure Synapse gebruiken met behulp van beide technieken. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Batchtaken verzenden met de vacaturedefinitie Spark

Ga naar de zelfstudie voor meer informatie over het gebruik van Azure Synapse Analytics om [Apache Spark-taakdefinities voor Synapse Spark-groepen](apache-spark-job-definitions.md)te maken. Als u uw app niet hebt verpakt om te verzenden naar Azure Synapse, voert u de volgende stappen uit.

1. Voer de volgende opdrachten uit om uw app te publiceren. Zorg ervoor dat *u mySparkApp* vervangt door het pad naar uw app.

   **Op Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **Op Linux:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET voor Apache Spark in Azure Synapse Analytics-notitieblokken

Wanneer u een nieuw notitieblok maakt, kiest u een taalkernel die u uw bedrijfslogica wilt uitdrukken. Er is kernelondersteuning voor verschillende talen, waaronder C#.

Als u .NET voor Apache Spark wilt gebruiken in uw Azure Synapse Analytics-notitieblok, selecteert u **.NET Spark (C#)** als kernel en koppelt u het notitieblok aan een bestaande Spark-groep.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET voor Apache Spark in Azure Synapse Analytics-notitieblokken 

Notitieblokken zijn een geweldige optie voor het prototypen van uw .NET voor Apache Spark-pijplijnen en -scenario's. U snel en efficiënt aan de slag gaan met, begrijpen, filteren, weergeven en visualiseren van uw gegevens. Data engineers, data scientists, business analisten en machine learning engineers zijn allemaal in staat om samen te werken via een gedeeld, interactief document. U ziet direct resultaten van gegevensverkenning en uw gegevens visualiseren in hetzelfde notitieblok.

### <a name="how-to-use-notebooks"></a>Notitieblokken gebruiken

Wanneer u een nieuw notitieblok maakt, kiest u een taalkernel die u uw bedrijfslogica wilt uitdrukken. Er is kernelondersteuning voor verschillende talen, waaronder C#. 

Als u .NET voor Apache Spark wilt gebruiken in uw Azure Synapse Analytics-notitieblok, selecteert u **.NET Spark (C#)** als kernel en koppelt u het notitieblok aan een bestaande Spark-groep. 

De .NET Spark-notebook is gebaseerd op de interactieve .NET-ervaringen en biedt interactieve C#-ervaringen met `spark` de mogelijkheid om .NET voor Spark uit de doos te gebruiken met de reeds vooraf gedefinieerde Spark-sessievariabele.

### <a name="sparknet-c-kernel-features"></a>Spark.NET C#-kernelfuncties

De volgende functies zijn beschikbaar wanneer u .NET voor Apache Spark gebruikt in het Azure Synapse Analytics-notitieblok:

* Declaratieve HTML: Genereer uitvoer uit uw cellen met HTML-syntaxis, zoals kopteksten, lijsten met opsommingstekens en zelfs het weergeven van afbeeldingen.
* Eenvoudige C#-instructies (zoals toewijzingen, afdrukken naar console, uitzonderingen gooien, enzovoort).
* Codeblokken voor meerdere regels C# (zoals als instructies, voor elke lussen, klassedefinities, enzovoort).
* Toegang tot de standaard C#-bibliotheek (zoals Systeem, LINQ, Enumerables, enzovoort).
* Ondersteuning voor [C# 8.0-taalfuncties](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* 'spark' als een vooraf gedefinieerde variabele om u toegang te geven tot uw Apache Spark-sessie.
* Ondersteuning voor het definiëren van [.NET door de gebruiker gedefinieerde functies die kunnen worden uitgevoerd binnen Apache Spark](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Ondersteuning voor het visualiseren van de uitvoer van uw Spark-taken met behulp van verschillende grafieken (zoals lijn, balk `XPlot.Plotly` of histogram) en lay-outs (zoals single, bedekt, enzovoort) met behulp van de bibliotheek.
* Mogelijkheid om NuGet-pakketten op te nemen in uw C#-notitieblok.

## <a name="next-steps"></a>Volgende stappen

* [.NET voor Apache Spark-documentatie](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET Interactief](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)