---
title: Wat is Text Analytics-API? - Mogelijkheden -
titleSuffix: Azure Cognitive Services
description: Gebruik de Text Analytics API van Azure Cognitive Services voor sentimentanalyse, sleutelzinextractie, taaldetectie en entiteitsherkenning.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: a9519be591581fa434825f1a1fb31749788a21a8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78395727"
---
# <a name="what-is-the-text-analytics-api"></a>Wat is Text Analytics-API?

De Text Analytics API is een cloudservice die geavanceerde natuurlijke taalverwerking biedt via ruwe tekst en vier belangrijke functies bevat: sentimentanalyse, sleutelzinextractie, taaldetectie en benoemde entiteitsherkenning.

De API maakt deel uit van [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), een verzameling van machine learning- en AI-algoritmen in de cloud, die kunnen worden gebruikt in uw ontwikkelprojecten.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Tekstanalyse kan verschillende dingen betekenen, maar in Cognitive Services biedt de Text Analytics API vier soorten analyses zoals hieronder beschreven. U deze functies gebruiken met de [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)of de [clientbibliotheek.](quickstarts/text-analytics-sdk.md)

## <a name="sentiment-analysis"></a>Sentimentanalyse
Gebruik [sentimentanalyse](how-tos/text-analytics-how-to-sentiment-analysis.md) om erachter te komen wat klanten van uw merk of onderwerp vinden door ruwe tekst te analyseren voor aanwijzingen over positief of negatief sentiment. Deze API retourneert een gevoelsscore tussen 0 en 1 voor elk document, waarbij 1 het meest positief is.<br /> De analysemodellen zijn vooraf getraind met behulp van een uitgebreide hoofdtekst en natuurlijke-taaltechnologieën van Microsoft. Voor [geselecteerde talen](text-analytics-supported-languages.md) kan de API elke onbewerkte tekst die u opgeeft analyseren en beoordelen en de resultaten direct doorgeven aan de aanroepende toepassing.

## <a name="key-phrase-extraction"></a>Sleuteltermextractie
Haal automatisch [sleutelzinnen](how-tos/text-analytics-how-to-keyword-extraction.md) uit om snel de belangrijkste punten te identificeren. Bijvoorbeeld, voor de invoertekst 'het eten was heerlijk en de bediening fantastisch' retourneert de API de belangrijkste gespreksonderwerpen: 'eten' en 'bediening fantastisch'.

## <a name="language-detection"></a>Taaldetectie
U [detecteren in welke taal de invoertekst is geschreven](how-tos/text-analytics-how-to-language-detection.md) en één taalcode rapporteren voor elk document dat op de aanvraag wordt ingediend in een breed scala aan talen, varianten, dialecten en sommige regionale/culturele talen. De taalcode is gekoppeld aan een score die de sterkte van de score aangeeft.

## <a name="named-entity-recognition"></a>Herkenning van benoemde entiteiten
[Identificeer en categoriseer entiteiten](how-tos/text-analytics-how-to-entity-linking.md) in uw tekst als personen, plaatsen, organisaties, datum/tijd, hoeveelheden, percentages, valuta's en meer. Bekende entiteiten worden ook herkend en gekoppeld aan meer informatie op het web.

## <a name="use-containers"></a>Containers gebruiken

[Gebruik de Text Analytics-containers](how-tos/text-analytics-how-to-install-containers.md) om sleuteltermen te extraheren, taal te detecteren, en sentiment lokaal te analyseren, door gestandaardiseerde Docker-containers dichter bij uw gegevens te installeren.

## <a name="typical-workflow"></a>Standaardwerkstroom

De werkstroom is eenvoudig: u dient gegevens in die u wilt analyseren en verwerkt de uitvoer in uw code. Analyseprogramma's worden in de huidige staat gebruikt, zonder extra configuratie of aanpassing.

1. [Maak een Azure-bron](../cognitive-services-apis-create-account.md) voor Tekstanalyse. Zorg er vervolgens [voor dat de sleutel](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) wordt gegenereerd om uw aanvragen te verifiëren.

2. [Formuleer een aanvraag](how-tos/text-analytics-how-to-call-api.md#json-schema) in JSON, die uw gegevens bevat als onbewerkte tekst.

3. Plaats het verzoek op het eindpunt dat is vastgesteld tijdens het aanmelden en voeg de gewenste resource toe: sentimentanalyse, sleutelzinextractie, taaldetectie of benoemde entiteitsherkenning.

4. U kunt het antwoord streamen of lokaal opslaan. Afhankelijk van de aanvraag bestaan de resultaten uit een gevoelsscore, een verzameling geëxtraheerde sleuteltermen of een taalcode.

Uitvoer wordt geretourneerd als één JSON-document met de resultaten van elk tekstdocument dat u hebt geplaatst, op basis van de id. U kunt de resultaten vervolgens analyseren, visualiseren of categoriseren in inzichten waarvoor een actie kan worden uitgevoerd.

Gegevens worden niet opgeslagen in uw account. Bewerkingen die door de Text Analytics-API worden uitgevoerd zijn staatloos. Dat betekent dat de tekst die u opgeeft, wordt verwerkt en de resultaten direct worden geretourneerd.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Text Analytics voor meerdere programmeerervaringsniveaus

U de Text Analytics API in uw processen gaan gebruiken, zelfs als u niet veel ervaring hebt met programmeren. Gebruik deze zelfstudies om te leren hoe u de API gebruiken om tekst op verschillende manieren te analyseren om aan uw ervaringsniveau te voldoen. 

* Minimale programmering vereist:
    * [Informatie extraheren in Excel met text analytics en Power Automate](tutorials/extract-excel-information.md)
    * [De TEXT Analytics API en MS Flow gebruiken om het sentiment van opmerkingen in een Yammer-groep te identificeren](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integreer Power BI met de Text Analytics API om feedback van klanten te analyseren](tutorials/tutorial-power-bi-key-phrases.md)
* Programmeerervaring aanbevolen:
    * [Sentimentanalyse voor streaminggegevens met behulp van Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Een Flask-app bouwen om tekst te vertalen, sentiment te analyseren en spraak te synthetiseren](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Ondersteunde talen

Deze sectie is voor betere zichtbaarheid verplaatst naar een afzonderlijk artikel. Raadpleeg [ondersteunde talen in de Text Analytics API](text-analytics-supported-languages.md) voor deze inhoud.

<a name="data-limits"></a>

## <a name="data-limits"></a>Gegevenslimieten

Alle eindpunten van de Text Analytics-API accepteren onbewerkte tekstgegevens. De huidige limiet is 5120 tekens per document. Als u grotere documenten moet analyseren, kunt u deze splitsen in een aantal kleinere documenten.

| Limiet | Waarde |
|------------------------|---------------|
| Maximale grootte van één document | 5.120 tekens zoals [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements)gemeten door . |
| Maximale grootte van de hele aanvraag | 1 MB |
| Maximum aantal documenten in een aanvraag | 1000 documenten |

Uw tarieflimiet is afhankelijk van uw prijscategorie.

| Laag          | Aanvragen per seconde | Aanvragen per minuut |
|---------------|---------------------|---------------------|
| S / Multi-service | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Aanvragen worden voor elke Text Analytics-functie afzonderlijk gemeten. U bijvoorbeeld het maximum aantal aanvragen voor uw prijscategorie tegelijkertijd naar elke functie verzenden.      

## <a name="unicode-encoding"></a>Unicode-codering

De Text Analytics-API maakt gebruik van Unicode-codering voor tekstweergave en het berekenen van het aantal tekens. Aanvragen kunnen worden ingediend in UTF-8 en UTF-16, zonder meetbare verschillen in het aantal tekens. Unicode-codepunten worden gebruikt als de heuristiek voor tekenlengte en worden wat de gegevenslimieten voor tekstanalyse betreft als gelijkwaardig beschouwd. Als u [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) het aantal tekens gebruikt om het aantal tekens te krijgen, gebruikt u dezelfde methode die we gebruiken om de grootte van de gegevens te meten.

## <a name="next-steps"></a>Volgende stappen

+ [Maak een Azure-bron](../cognitive-services-apis-create-account.md) voor Text Analytics om een sleutel en eindpunt voor uw toepassingen te krijgen.

+ Gebruik de [snelstart](quickstarts/text-analytics-sdk.md) om API-aanroepen te verzenden. Informatie over het indienen van tekst, het kiezen van een analyse en het bekijken van de resultaten met minimale code.

+ Bekijk [wat er nieuw is in de Text Analytics API](whats-new.md) voor informatie over nieuwe releases en functies.

+ Graaf in een beetje dieper met deze [sentiment analyse tutorial](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) met behulp van Azure Databricks.

+ Bekijk onze lijst met blogberichten en meer video's over het gebruik van de Text Analytics API met andere tools en technologieën op onze [pagina Externe &-community-inhoud.](text-analytics-resource-external-community.md)
