---
title: Wat is Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Met de Azure Cognitive Services-formulier herkenning kunt u sleutel-waardeparen en tabel gegevens uit formulier documenten identificeren en extra heren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: 86e0494ded84e9711b2283ae5fad77ff626cf792
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904485"
---
# <a name="what-is-form-recognizer"></a>Wat is Form Recognizer?

Azure Form Recognizer is een cognitieve service die gebruikmaakt van machine learning technologie om sleutel-waardeparen en tabel gegevens uit formulier documenten te identificeren en uit te pakken. Vervolgens voert het gestructureerde gegevens uit die ook de relaties uit het oorspronkelijke bestand omvatten. U kunt uw aangepaste formulier Recognizer-model aanroepen met behulp van een eenvoudige REST API om de complexiteit te reduceren en deze eenvoudig te integreren in uw werk stroom of toepassing. Als u aan de slag wilt gaan, hebt u slechts vijf gevulde formulier documenten of twee gevulde formulieren nodig, plus een lege vorm van hetzelfde type als uw invoer materiaal. U krijgt snel nauw keurige resultaten die zijn afgestemd op uw specifieke inhoud zonder zware hand matige interventie of expertise met uitgebreide gegevens wetenschap.

## <a name="custom-models"></a>Aangepaste modellen

De aangepaste model treinen van de formulier herkenning voor uw eigen gegevens en u hebt slechts vijf invoer formulieren voor het voor beeld nodig om te starten. Wanneer u de invoer gegevens verzendt, verdeelt de algoritme de formulieren op type, detecteert welke sleutels en tabellen aanwezig zijn en koppelt waarden aan sleutels en vermeldingen aan tabellen. Vervolgens voert het gestructureerde gegevens uit die ook de relaties uit het oorspronkelijke bestand omvatten. Nadat u het model hebt getraind, kunt u het testen en opnieuw trainen en uiteindelijk gebruiken om gegevens te extra heren uit meer formulieren, afhankelijk van uw behoeften.

Leerprocessen zonder supervisie stellen het model in staat om de lay-out en relaties tussen velden en items te begrijpen zonder handmatige labeling van gegevens of intensieve codering en onderhoud. Daarentegen hebben vooraf getrainde machine learning modellen gestandaardiseerde gegevens nodig. Ze zijn minder nauw keurig met invoer materiaal dat afwijkt van traditionele indelingen, zoals branchespecifieke formulieren.

## <a name="prebuilt-receipt-model"></a>Gebaseerd ontvangst model

Formulier herkenning bevat ook een model voor het lezen van verkoop ontvangsten. Dit model extraheert belang rijke informatie zoals de tijd en datum van de trans actie, zakelijke informatie, bedragen van belastingen en totalen en meer. Daarnaast wordt het vooraf gedefinieerde ontvangstbewijs model getraind om alle tekst op een kwitantie te herkennen en te retour neren.

## <a name="what-it-includes"></a>Samenstelling van Content Moderator

Formulier herkenner is beschikbaar als een REST API. U kunt een aangepast model maken, trainen en beoordelen of toegang krijgen tot het vooraf ontwikkelde model door deze Api's aan te roepen. Als u wilt, kunt u aangepaste modellen trainen en uitvoeren in een lokale docker-container.

## <a name="input-requirements"></a>Invoer vereisten
### <a name="custom-model"></a>Aangepast model

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Gebaseerd ontvangst model

De invoer vereisten voor het ontvangstbewijs model zijn iets anders.

* De indeling moet JPEG, PNG, BMP, PDF (tekst of gescand) of TIFF zijn.
* De bestands grootte moet minder dan 20 MB zijn.
* De afmetingen van afbeeldingen moeten tussen 50 x 50 pixels en 10000 x 10000 pixels liggen. 
* PDF-dimensies mogen Maxi maal 17 x 17 inch zijn, overeenkomen met de papier formaten Legal of a3 en kleiner.
* Voor PDF en TIFF worden alleen de eerste 200 pagina's verwerkt (met een abonnement met een gratis laag, worden alleen de eerste twee pagina's verwerkt).

## <a name="request-access"></a>Toegang aanvragen

Formulier herkenner is beschikbaar in een preview-versie van beperkte toegang. Als u toegang wilt krijgen tot de preview, vult u het formulier voor de [toegangs aanvraag voor de formulier herkenning](https://aka.ms/FormRecognizerRequestAccess) in en verzendt u dit. Het formulier vraagt informatie over u, uw bedrijf en het gebruikers scenario waarvoor u de formulier herkenner gaat gebruiken. Als uw aanvraag is goedgekeurd door het team van Azure Cognitive Services, ontvangt u een e-mail bericht met instructies voor toegang tot de service.

## <a name="where-do-i-start"></a>Waar moet ik beginnen?

**Stap 1:** Een resource voor een formulier herkenning maken in de Azure Portal.

**Stap 2:** Volg een Snelstartgids om de REST API te gebruiken:
* [Snelstartgids: een model voor een formulier herkenning trainen en formulier gegevens extra heren met behulp van de REST API met krul](quickstarts/curl-train-extract.md)
* [Quick Start: een model voor een formulier herkenning trainen en formulier gegevens extra heren met behulp van de REST API met python](quickstarts/python-train-extract.md)
* [Snelstartgids: ontvangst gegevens ophalen met behulp van krul](quickstarts/curl-receipts.md)
* [Snelstartgids: ontvangst gegevens ophalen met behulp van python](quickstarts/python-receipts.md)

U wordt aangeraden de gratis service te gebruiken wanneer u de technologie bekijkt. Houd er rekening mee dat het aantal beschik bare pagina's beperkt is tot 500 per maand.

**Stap 3:** De REST-Api's controleren

U gebruikt de volgende Api's om gestructureerde gegevens uit formulieren te trainen en uit te pakken.

|||
|---|---|
| Model trainen| Train een nieuw model om uw formulieren te analyseren met behulp van vijf soorten formulieren van hetzelfde type. Of, Train met een leeg formulier en twee ingevulde formulieren.  |
| Formulier analyseren |Analyseer één document dat is door gegeven als een stroom om sleutel-waardeparen en tabellen uit het formulier met uw aangepaste model te extra heren.  |
| Toename analyseren |Analyseer één ontvangst document voor het extra heren van belang rijke informatie en andere ontvangst tekst.|

Bekijk de [rest API referentie documentatie](https://aka.ms/form-recognizer/api) voor meer informatie. 

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Deze service wordt aangeboden als een [Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) van een Azure-service onder de [voor waarden van de online service](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Net als bij alle cognitieve services moeten ontwikkel aars die gebruikmaken van de Form Recognizer-service op de hoogte zijn van het micro soft-beleid voor klant gegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Voer een [Snelstartgids](quickstarts/curl-train-extract.md) uit om aan de slag te gaan met de [API voor formulier herkenning](https://aka.ms/form-recognizer/api).