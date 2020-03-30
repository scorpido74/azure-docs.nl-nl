---
title: Vergelijking van azure Stream Analytics-functies
description: In dit artikel worden de functies die worden ondersteund voor Azure Stream Analytics-cloud- en IoT Edge-taken in de Azure-portal, Visual Studio en Visual Studio Code vergeleken.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: cdae6a3b7319aefa9d4f19b5d613d1afb8b6804a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235312"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Vergelijking van azure Stream Analytics-functies

Met Azure Stream Analytics u streamingoplossingen maken in de cloud en op de IoT Edge met [Azure portal,](stream-analytics-quick-create-portal.md) [Visual Studio](stream-analytics-quick-create-vs.md)en Visual [Studio Code.](quick-create-vs-code.md) De tabellen in dit artikel laten zien welke functies worden ondersteund door elk platform voor beide taaktypen.

## <a name="cloud-job-features"></a>Functies voor cloudfuncties


|Functie  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Cross-platform     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Scriptauthoring     |Ja         |Ja         |Ja         |
|Script Intellisense     |Syntaxismarkering         |Syntaxismarkering</br>Code voltooiing</br>Foutmarkering         |Syntaxismarkering</br>Code voltooiing</br>Foutmarkering         |
|Alle typen ingangen, uitvoer en taakconfiguraties definiëren     |Ja         |Ja         |Ja         |
|Broncodebeheer     |Nee         |Ja         |Ja         |
|ONDERSTEUNING voor CI/CD     |Gedeeltelijk         |Ja         |Ja         |
|Invoer en uitvoer delen voor meerdere query's     |Nee         |Ja         |Ja         |
|Querytesten met een voorbeeldbestand     |Ja         |Ja        |Ja         |
|Live data lokale testen     |Nee         |Ja       |Ja      |
|Taken weergeven en taakentiteiten weergeven     |Ja         |Ja        |Ja         |
|Een taak exporteren naar een lokaal project     |Nee         |Ja         |Ja         |
|Taken verzenden, starten en stoppen     |Ja         |Ja         |Ja         |
|Jobstatistieken en -diagram weergeven     |Ja         |Ja         |Openen in portal         |
|Fouten in taakruntime weergeven     |Ja         |Ja         |Nee         |
|Diagnostische logboeken     |Ja         |Nee         |Nee         |
|Aangepaste berichteigenschappen     |Ja         |Ja         |Nee       |
|C# aangepaste codefunctie en Deserializer|Alleen-lezen modus|Ja|Nee|
|JavaScript UDF en UDA     |Ja         |Ja         |Alleen op Windows         |
|Machine Learning-service     |Ja        |Ja         |Nee         |
|Machine Learning Studio     |Ja, maar de query kan niet worden getest        |Ja |Nee         |
|Compatibiliteitsniveau     |1.0</br>1.1</br>1.2 (standaard)         |1.0</br>1.1</br>1.2 (standaard)           |1.0</br>1.1</br>1.2 (standaard)           |
|Ingebouwde ML-gebaseerde anomalydetectiefuncties     |Ja         |Ja         |Ja         |
|Ingebouwde GeoSpatial-functies     |Ja         |Ja         |Ja         |



## <a name="iot-edge-job-features"></a>Functies voor IoT Edge-functies

|Functie  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Taakcreatie     |Ja         |Ja         |Nee         |
|Broncodebeheer     |Nee         |Ja         |Nee         |
|Een taak exporteren naar een lokaal project     |Nee         |Ja         |Nee         |
|Querytesten met een voorbeeldbestand     |Ja         |Ja         |Nee         |
|Invoer en uitvoer delen voor meerdere query's     |Nee         |Ja         |Nee         |
|C# UDF     |Nee         |Ja         |Nee         |
|Vacatures indienen     |Ja         |Ja         |Nee         |
|Taken weergeven en taakentiteiten weergeven     |Ja         |Ja         |Nee         |
|Jobstatistieken en -diagram weergeven     |Ja         |Gedeeltelijk         |Nee         |
|Fouten in taakruntime weergeven     |Ja         |Gedeeltelijk         |Nee         |
|ONDERSTEUNING voor CI/CD     |Nee         |Nee         |Nee         |


## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics op IoT Edge](stream-analytics-edge.md)
* [Zelfstudie: Een door de gebruiker gedefinieerde functie schrijven voor de functie IoT Edge van Azure Stream Analytics (Voorbeeld)](stream-analytics-edge-csharp-udf.md)
* [Stream Analytics IoT Edge-taken ontwikkelen met behulp van Visual Studio-hulpprogramma's](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)
* [Azure Stream Analytics verkennen met Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)


