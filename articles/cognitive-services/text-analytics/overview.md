---
title: Wat is Text Analytics-API? Bieden
titleSuffix: Azure Cognitive Services
description: Gebruik de Text Analytics-API van Azure Cognitive Services voor sentiment analyse, extractie van sleutel zinnen, taal detectie en entiteits herkenning.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: a9519be591581fa434825f1a1fb31749788a21a8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395727"
---
# <a name="what-is-the-text-analytics-api"></a>Wat is Text Analytics-API?

De Text Analytics-API is een Cloud service die geavanceerde verwerking van natuurlijke taal via onbewerkte tekst biedt en vier belang rijke functies bevat: sentiment analyse, extractie van sleutel woorden, taal detectie en benoemde entiteits herkenning.

De API maakt deel uit van [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), een verzameling van machine learning- en AI-algoritmen in de cloud, die kunnen worden gebruikt in uw ontwikkelprojecten.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Tekst analyse kan verschillende dingen betekenen, maar in Cognitive Services biedt de Text Analytics-API vier typen analyse zoals hieronder wordt beschreven. U kunt deze functies gebruiken met de [rest API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/), of de [client bibliotheek](quickstarts/text-analytics-sdk.md).

## <a name="sentiment-analysis"></a>Sentimentanalyse
Gebruik [sentiment Analysis](how-tos/text-analytics-how-to-sentiment-analysis.md) om erachter te komen welke klanten uw merk of onderwerp denken door onbewerkte tekst te analyseren op aanwijzingen over positieve of negatieve sentiment. Deze API retourneert een gevoelsscore tussen 0 en 1 voor elk document, waarbij 1 het meest positief is.<br /> De analysemodellen zijn vooraf getraind met behulp van een uitgebreide hoofdtekst en natuurlijke-taaltechnologieën van Microsoft. Voor [geselecteerde talen](text-analytics-supported-languages.md) kan de API elke onbewerkte tekst die u opgeeft analyseren en beoordelen en de resultaten direct doorgeven aan de aanroepende toepassing.

## <a name="key-phrase-extraction"></a>Sleuteltermextractie
[Sleutel zinnen automatisch extra heren](how-tos/text-analytics-how-to-keyword-extraction.md) om snel de belangrijkste punten te identificeren. Bijvoorbeeld, voor de invoertekst 'het eten was heerlijk en de bediening fantastisch' retourneert de API de belangrijkste gespreksonderwerpen: 'eten' en 'bediening fantastisch'.

## <a name="language-detection"></a>Taaldetectie
U kunt [detecteren in welke taal de invoer tekst is geschreven](how-tos/text-analytics-how-to-language-detection.md) en een enkele taal code rapporteren voor elk document dat op de aanvraag wordt verzonden in een breed scala aan talen, varianten, dialecten en enkele regionale/culturele talen. De taalcode is gekoppeld aan een score die de sterkte van de score aangeeft.

## <a name="named-entity-recognition"></a>Herkenning van benoemde entiteiten
Entiteiten in uw tekst [identificeren en categoriseren](how-tos/text-analytics-how-to-entity-linking.md) als mensen, plaatsen, organisaties, datum/tijd, hoeveel heden, percentages, valuta's en nog veel meer. Bekende entiteiten worden ook herkend en gekoppeld aan meer informatie op het web.

## <a name="use-containers"></a>Containers gebruiken

[Gebruik de Text Analytics-containers](how-tos/text-analytics-how-to-install-containers.md) om sleuteltermen te extraheren, taal te detecteren, en sentiment lokaal te analyseren, door gestandaardiseerde Docker-containers dichter bij uw gegevens te installeren.

## <a name="typical-workflow"></a>Standaardwerkstroom

De werkstroom is eenvoudig: u dient gegevens in die u wilt analyseren en verwerkt de uitvoer in uw code. Analyseprogramma's worden in de huidige staat gebruikt, zonder extra configuratie of aanpassing.

1. [Maak een Azure-resource](../cognitive-services-apis-create-account.md) voor Text Analytics. Vervolgens [haalt u de sleutel](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) op die u voor het verifiëren van uw aanvragen hebt gegenereerd.

2. [Formuleer een aanvraag](how-tos/text-analytics-how-to-call-api.md#json-schema) in JSON, die uw gegevens bevat als onbewerkte tekst.

3. Post de aanvraag voor het eind punt dat is gemaakt tijdens het aanmelden, het toevoegen van de gewenste resource: sentiment analyse, extractie van sleutel zinnen, taal detectie of benoemde entiteits herkenning.

4. U kunt het antwoord streamen of lokaal opslaan. Afhankelijk van de aanvraag bestaan de resultaten uit een gevoelsscore, een verzameling geëxtraheerde sleuteltermen of een taalcode.

Uitvoer wordt geretourneerd als één JSON-document met de resultaten van elk tekstdocument dat u hebt geplaatst, op basis van de id. U kunt de resultaten vervolgens analyseren, visualiseren of categoriseren in inzichten waarvoor een actie kan worden uitgevoerd.

Gegevens worden niet opgeslagen in uw account. Bewerkingen die door de Text Analytics-API worden uitgevoerd zijn staatloos. Dat betekent dat de tekst die u opgeeft, wordt verwerkt en de resultaten direct worden geretourneerd.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Text Analytics voor meerdere programmeer ervarings niveaus

U kunt de Text Analytics-API in uw processen gaan gebruiken, zelfs als u niet veel ervaring hebt met het Program meren. Gebruik deze zelf studies om te leren hoe u de API kunt gebruiken om tekst op verschillende manieren te analyseren, zodat deze past bij uw ervarings niveau. 

* Minimale programmering vereist:
    * [Informatie in Excel extra heren met behulp van Text Analytics en energie automatisering](tutorials/extract-excel-information.md)
    * [De Text Analytics-API en MS flow gebruiken om de sentiment van opmerkingen in een Yammer-groep te identificeren](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Power BI integreren met de Text Analytics-API voor het analyseren van feedback van klanten](tutorials/tutorial-power-bi-key-phrases.md)
* Aanbevolen programmeer ervaring:
    * [Sentimentanalyse over het streamen van gegevens met behulp van Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Bouw een kolf-app om tekst te vertalen, sentiment te analyseren en spraak te maken](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Ondersteunde talen

Deze sectie is voor betere zichtbaarheid verplaatst naar een afzonderlijk artikel. Raadpleeg [de ondersteunde talen in de Text Analytics-API](text-analytics-supported-languages.md) voor deze inhoud.

<a name="data-limits"></a>

## <a name="data-limits"></a>Gegevenslimieten

Alle eindpunten van de Text Analytics-API accepteren onbewerkte tekstgegevens. De huidige limiet is 5120 tekens per document. Als u grotere documenten moet analyseren, kunt u deze splitsen in een aantal kleinere documenten.

| Limiet | Waarde |
|------------------------|---------------|
| Maximale grootte van één document | 5120 tekens, zoals wordt gemeten door [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maximale grootte van de hele aanvraag | 1 MB |
| Maximum aantal documenten in een aanvraag | 1000 documenten |

De frequentie limiet is afhankelijk van uw prijs categorie.

| Laag          | Aanvragen per seconde | Aanvragen per minuut |
|---------------|---------------------|---------------------|
| S/meerdere services | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Aanvragen worden afzonderlijk gemeten voor elke Text Analytics-functie. U kunt bijvoorbeeld tegelijkertijd het maximum aantal aanvragen voor uw prijs categorie verzenden naar elke functie.      

## <a name="unicode-encoding"></a>Unicode-codering

De Text Analytics-API maakt gebruik van Unicode-codering voor tekstweergave en het berekenen van het aantal tekens. Aanvragen kunnen worden ingediend in UTF-8 en UTF-16, zonder meetbare verschillen in het aantal tekens. Unicode-codepunten worden gebruikt als de heuristiek voor tekenlengte en worden wat de gegevenslimieten voor tekstanalyse betreft als gelijkwaardig beschouwd. Als u [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) gebruikt om het aantal tekens te verkrijgen, gebruikt u dezelfde methode als voor het meten van de gegevensgrootte.

## <a name="next-steps"></a>Volgende stappen

+ [Maak een Azure-resource](../cognitive-services-apis-create-account.md) voor Text Analytics om een sleutel en eind punt voor uw toepassingen op te halen.

+ Gebruik de [Snelstartgids](quickstarts/text-analytics-sdk.md) om API-aanroepen te verzenden. Informatie over het indienen van tekst, het kiezen van een analyse en het bekijken van de resultaten met minimale code.

+ Bekijk [wat er nieuw is in de Text Analytics-API](whats-new.md) voor informatie over nieuwe releases en functies.

+ U leert in een beetje dieper met deze [zelf studie](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) over de sentiment-analyse met behulp van Azure Databricks.

+ Bekijk onze lijst met blog berichten en meer Video's over het gebruik van de Text Analytics-API met andere hulpprogram ma's en technologieën op onze [pagina met externe & Community-inhoud](text-analytics-resource-external-community.md).
