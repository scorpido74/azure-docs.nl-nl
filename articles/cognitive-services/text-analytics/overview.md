---
title: Wat is Text Analytics-API? - Mogelijkheden -
titleSuffix: Azure Cognitive Services
description: Gebruik de Text Analytics-API in Azure Cognitive Services voor sentimentanalyse, sleuteltermextractie, taaldetectie en entiteitsherkenning.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 08/27/2020
ms.author: aahi
ms.openlocfilehash: a3c538f3a9e7a2d8d71fff38fb927dbcdf725732
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89000954"
---
# <a name="what-is-the-text-analytics-api"></a>Wat is Text Analytics-API?

De Text Analytics-API is een op de cloud gebaseerde service die geavanceerde verwerking van natuurlijke taal biedt voor onbewerkte tekst. De service bevat vier hoofdfuncties: sentimentanalyse, sleuteltermextractie, taaldetectie en herkenning van benoemde entiteiten.

De API maakt deel uit van [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), een verzameling van machine learning- en AI-algoritmen in de cloud, die kunnen worden gebruikt in uw ontwikkelprojecten.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Tekstanalyse kan verschillende dingen betekenen, maar in Cognitive Services biedt de Text Analytics-API vier soorten analyse, zoals hierna wordt beschreven. U kunt deze functies gebruiken met de [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/) of met de [clientbibliotheek](quickstarts/text-analytics-sdk.md).

## <a name="sentiment-analysis"></a>Sentimentanalyse
Gebruik [sentimentanalyse](how-tos/text-analytics-how-to-sentiment-analysis.md) om erachter te komen wat klanten van uw merk of onderwerp vinden door onbewerkte tekst te analyseren op aanwijzingen over positieve of negatieve indrukken. Deze API retourneert een gevoelsscore tussen 0 en 1 voor elk document, waarbij 1 het meest positief is.<br /> De analysemodellen zijn vooraf getraind met behulp van een uitgebreide hoofdtekst en natuurlijke-taaltechnologieën van Microsoft. Voor [geselecteerde talen](text-analytics-supported-languages.md) kan de API elke onbewerkte tekst die u opgeeft analyseren en beoordelen en de resultaten direct doorgeven aan de aanroepende toepassing.

## <a name="key-phrase-extraction"></a>Sleuteltermextractie
U kunt automatisch [sleuteltermen extraheren](how-tos/text-analytics-how-to-keyword-extraction.md) om snel de hoofdpunten te identificeren. Bijvoorbeeld, voor de invoertekst 'het eten was heerlijk en de bediening fantastisch' retourneert de API de belangrijkste gespreksonderwerpen: 'eten' en 'bediening fantastisch'.

## <a name="language-detection"></a>Taaldetectie
U kunt [detecteren in welke taal de ingevoerde tekst is geschreven](how-tos/text-analytics-how-to-language-detection.md) en één taalcode rapporteren voor elk document dat is ingediend bij de aanvraag in veel verschillende talen, varianten, dialecten en enkele regionale/culturele talen. De taalcode is gekoppeld aan een score die de sterkte van de score aangeeft.

## <a name="named-entity-recognition"></a>Herkenning van benoemde entiteiten
U kunt [entiteiten identificeren en categoriseren](how-tos/text-analytics-how-to-entity-linking.md) in uw tekst als mensen, plaatsen, organisaties, datum/tijd, hoeveelheden, percentages, valuta's en meer. Bekende entiteiten worden ook herkend en gekoppeld aan meer informatie op het web.

## <a name="use-containers"></a>Containers gebruiken

[Gebruik de Text Analytics-containers](how-tos/text-analytics-how-to-install-containers.md) om sleuteltermen te extraheren, taal te detecteren, en sentiment lokaal te analyseren, door gestandaardiseerde Docker-containers dichter bij uw gegevens te installeren.

## <a name="typical-workflow"></a>Standaardwerkstroom

De werkstroom is eenvoudig: u dient gegevens in die u wilt analyseren en verwerkt de uitvoer in uw code. Analyseprogramma's worden in de huidige staat gebruikt, zonder extra configuratie of aanpassing.

1. [Maak een Azure-resource](../cognitive-services-apis-create-account.md) voor Text Analytics. Daarna kunt u [de sleutel ophalen](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) die voor u is gegenereerd om uw aanvragen te verifiëren.

2. [Formuleer een aanvraag](how-tos/text-analytics-how-to-call-api.md#json-schema) in JSON, die uw gegevens bevat als onbewerkte tekst.

3. Publiceer de aanvraag aan het eindpunt dat is gemaakt tijdens de registratie, en voeg de gewenste resource toe: sentimentanalyse, sleuteltermextractie, taaldetectie of herkenning van benoemde entiteiten.

4. U kunt het antwoord streamen of lokaal opslaan. Afhankelijk van de aanvraag bestaan de resultaten uit een gevoelsscore, een verzameling geëxtraheerde sleuteltermen of een taalcode.

Uitvoer wordt geretourneerd als één JSON-document met de resultaten van elk tekstdocument dat u hebt geplaatst, op basis van de id. U kunt de resultaten vervolgens analyseren, visualiseren of categoriseren in inzichten waarvoor een actie kan worden uitgevoerd.

Gegevens worden niet opgeslagen in uw account. Bewerkingen die door de Text Analytics-API worden uitgevoerd zijn staatloos. Dat betekent dat de tekst die u opgeeft, wordt verwerkt en de resultaten direct worden geretourneerd.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Text Analytics voor meerdere niveaus van programmeerervaring

U kunt de Text Analytics-API in uw processen gaan gebruiken, ook als u niet veel ervaring hebt met programmeren. Gebruik deze zelfstudies om te leren hoe u de API kunt gebruiken om tekst op verschillende manieren te analyseren, zodat deze past bij uw ervaringsniveau. 

* Minimaal programmeren vereist:
    * [Informatie extraheren in Excel met behulp van Text Analytics en Power Automate](tutorials/extract-excel-information.md)
    * [De Text Analytics-API en MS Flow gebruiken om het sentiment van opmerkingen in een Yammer-groep te identificeren](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Power BI integreren met de Text Analytics-API voor het analyseren van feedback van klanten](tutorials/tutorial-power-bi-key-phrases.md)
* Aanbevolen programmeerervaring:
    * [Sentimentanalyse over het streamen van gegevens met behulp van Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Een Flask-app maken om tekst te vertalen, gevoel te analyseren en spraak na te bootsen](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Ondersteunde talen

Deze sectie is voor betere zichtbaarheid verplaatst naar een afzonderlijk artikel. Raadpleeg [Ondersteunde talen in de Text Analytics-API](text-analytics-supported-languages.md) voor deze inhoud.

<a name="data-limits"></a>

## <a name="data-limits"></a>Gegevenslimieten

Alle eindpunten van de Text Analytics-API accepteren onbewerkte tekstgegevens. Zie het artikel [Gegevenslimieten](concepts/data-limits.md) voor meer informatie.

## <a name="unicode-encoding"></a>Unicode-codering

De Text Analytics-API maakt gebruik van Unicode-codering voor tekstweergave en het berekenen van het aantal tekens. Aanvragen kunnen worden ingediend in UTF-8 en UTF-16, zonder meetbare verschillen in het aantal tekens. Unicode-codepunten worden gebruikt als de heuristiek voor tekenlengte en worden wat de gegevenslimieten voor tekstanalyse betreft als gelijkwaardig beschouwd. Als u [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) gebruikt om het aantal tekens te verkrijgen, gebruikt u dezelfde methode als voor het meten van de gegevensgrootte.

## <a name="next-steps"></a>Volgende stappen

+ [Maak een Azure-resource](../cognitive-services-apis-create-account.md) voor Text Analytics om een sleutel en eindpunt voor uw toepassingen te krijgen.

+ Gebruik de [quickstart](quickstarts/text-analytics-sdk.md) om API-aanroepen te verzenden. Informatie over het indienen van tekst, het kiezen van een analyse en het bekijken van de resultaten met minimale code.

+ Zie [wat er nieuw is in de Text Analytics-API](whats-new.md) voor meer informatie over nieuwe releases en functies.

+ U kunt meer leren met deze [zelfstudie over sentimentanalyse](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) met behulp van Azure Databricks.

+ Bekijk onze lijst met blogposts en meer video's over hoe u de Text Analytics-API gebruikt met andere hulpprogramma's en technologieën in onze [pagina Externe en community-inhoud](text-analytics-resource-external-community.md).
