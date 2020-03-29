---
title: Azure Machine Learning Anomaly Detection API - Team Data Science Process
description: Anomaly Detection API is een voorbeeld dat is gebouwd met Microsoft Azure Machine Learning dat afwijkingen in tijdreeksgegevens detecteert met numerieke waarden die gelijkmatig op afstand zijn in de tijd.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: a09094cf0d1bd3c2e299e968d7de8410dcd9c3cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721877"
---
# <a name="machine-learning-anomaly-detection-api"></a>Api voor anomaliedetectie van machine learning

> [!NOTE]
> Dit artikel is in onderhoud. We raden u aan om de [Api-service Anomaly Detector](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) te gebruiken die wordt aangedreven door een galerij met Machine Learning-algoritmen onder Azure Cognitive Services om afwijkingen van zakelijke, operationele en IoT-statistieken te detecteren.

## <a name="overview"></a>Overzicht
[Anomaly Detection API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) is een voorbeeld dat is gebouwd met Azure Machine Learning dat afwijkingen in tijdreeksgegevens detecteert met numerieke waarden die gelijkmatig op afstand zijn in de tijd.

Deze API kan de volgende soorten afwijkende patronen in tijdreeksgegevens detecteren:

* **Positieve en negatieve trends**: Bijvoorbeeld wanneer het monitoren van het geheugengebruik bij het berekenen van een opwaartse trend van belang kan zijn, omdat deze een indicatie kan zijn van een geheugenlek,
* **Wijzigingen in het dynamisch waardenbereik:** Wanneer bijvoorbeeld de uitzonderingen die door een cloudservice worden gegenereerd, worden gecontroleerd, kunnen eventuele wijzigingen in het dynamisch bereik van waarden duiden op instabiliteit in de status van de service en
* **Pieken en dalen:** Bijvoorbeeld bij het controleren van het aantal inlogfouten in een service of het aantal kassa's op een e-commercesite, kunnen pieken of dips abnormaal gedrag aangeven.

Deze machine learning detectoren volgen dergelijke veranderingen in waarden in de tijd en rapporteren voortdurende veranderingen in hun waarden als anomalie scores. Ze vereisen geen adhoc drempeltuning en hun scores kunnen worden gebruikt om vals-positieve snelheid te controleren. De api voor anomaliedetectie is nuttig in verschillende scenario's, zoals servicebewaking door KPI's na verloop van tijd bij te houden, gebruikscontrole via statistieken zoals aantal zoekopdrachten, aantal klikken, prestatiebewaking via tellers zoals geheugen, CPU, bestandsreads, enz. na verloop van tijd.

Het Anomaly Detection-aanbod wordt geleverd met handige tools om u op weg te helpen.

* De [webapplicatie](https://anomalydetection-aml.azurewebsites.net/) helpt u bij het evalueren en visualiseren van de resultaten van afwijkende detectie API's op uw gegevens.

> [!NOTE]
> Probeer **it-anomalie Insights-oplossing** aangedreven door [deze API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>API-implementatie
Als u de API wilt gebruiken, moet u deze implementeren in uw Azure-abonnement, waar deze wordt gehost als een Azure Machine Learning-webservice.  U dit doen vanuit de [Azure AI-galerie](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Hiermee worden twee Azure Machine Learning Studio (klassieke) Web Services (en bijbehorende bronnen) geïmplementeerd in uw Azure-abonnement: een voor anomaliedetectie met seizoensdetectie en één zonder seizoensdetectie.  Zodra de implementatie is voltooid, u uw API's beheren vanaf de pagina [Azure Machine Learning Studio (klassieke) webservices.](https://services.azureml.net/webservices/)  Vanaf deze pagina u uw eindpuntlocaties, API-sleutels en voorbeeldcode voor het aanroepen van de API vinden.  Meer gedetailleerde instructies zijn [hier](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice)beschikbaar.

## <a name="scaling-the-api"></a>De API schalen
Standaard heeft uw implementatie een gratis Dev/Test-factureringsplan met 1.000 transacties per maand en 2 rekenuren/maand.  U upgraden naar een ander abonnement volgens uw behoeften.  Details over de prijzen van verschillende abonnementen zijn [hier](https://azure.microsoft.com/pricing/details/machine-learning/) beschikbaar onder "Production Web API pricing".

## <a name="managing-aml-plans"></a>AML-plannen beheren
U uw factureringsplan [hier](https://services.azureml.net/plans/)beheren.  De naam van het abonnement is gebaseerd op de naam van de resourcegroep die u hebt gekozen bij het implementeren van de API, plus een tekenreeks die uniek is voor uw abonnement.  Instructies voor het upgraden van uw abonnement zijn [hier](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) beschikbaar onder de sectie Factureringsplannen beheren.

## <a name="api-definition"></a>API-definitie
De webservice biedt een REST-gebaseerde API via HTTPS die op verschillende manieren kan worden verbruikt, waaronder een web- of mobiele toepassing, R, Python, Excel, enz.  U stuurt uw tijdreeksgegevens naar deze service via een REST API-aanroep en er wordt een combinatie uitgevoerd van de drie onderstaande anomalietypen.

## <a name="calling-the-api"></a>De API aanroepen
Om de API aan te roepen, moet u de eindpuntlocatie en api-sleutel kennen.  Deze twee vereisten, samen met voorbeeldcode voor het aanroepen van de API, zijn beschikbaar op de pagina [Azure Machine Learning Studio (classic) webservices.](https://services.azureml.net/webservices/)  Navigeer naar de gewenste API en klik op het tabblad 'Consumeren' om deze te vinden.  U de API aanroepen als een Swagger `format=swagger`API (dat wil zeggen met de URL-parameter) of als een niet-Swagger API (dat wil zeggen, zonder de `format` URL-parameter).  De voorbeeldcode maakt gebruik van de Swagger-indeling.  Hieronder vindt u een voorbeeldverzoek en antwoord in niet-Swagger-indeling.  Deze voorbeelden zijn aan het eindpunt seasonality.  Het eindpunt van niet-seizoensgebondenheid is vergelijkbaar.

### <a name="sample-request-body"></a>Voorbeeldaanvraaginstantie
De aanvraag bevat `Inputs` twee `GlobalParameters`objecten: en .  In het onderstaande voorbeeldverzoek worden sommige parameters expliciet verzonden, terwijl andere dat niet zijn (scroll naar beneden voor een volledige lijst met parameters voor elk eindpunt).  Parameters die niet expliciet in de aanvraag worden verzonden, gebruiken de onderstaande standaardwaarden.

    {
                "Inputs": {
                        "input1": {
                                "ColumnNames": ["Time", "Data"],
                                "Values": [
                                        ["5/30/2010 18:07:00", "1"],
                                        ["5/30/2010 18:08:00", "1.4"],
                                        ["5/30/2010 18:09:00", "1.1"]
                                ]
                        }
                },
        "GlobalParameters": {
            "tspikedetector.sensitivity": "3",
            "zspikedetector.sensitivity": "3",
            "bileveldetector.sensitivity": "3.25",
            "detectors.spikesdips": "Both"
        }
    }

### <a name="sample-response"></a>Voorbeeldreactie
Als u het `ColumnNames` veld wilt `details=true` zien, moet u als URL-parameter in uw aanvraag opnemen.  Zie de onderstaande tabellen voor de betekenis achter elk van deze velden.

    {
        "Results": {
            "output1": {
                "type": "table",
                "value": {
                    "Values": [
                        ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                        ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                        ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                    ],
                    "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                    "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
                }
            }
        }
    }


## <a name="score-api"></a>Score-API
De Score API wordt gebruikt voor het uitvoeren van anomaliedetectie op niet-seizoensgebonden tijdreeksgegevens. De API draait een aantal anomaliedetectoren op de gegevens en retourneert hun anomaliescores.
De onderstaande afbeelding toont een voorbeeld van afwijkingen die de Score API kan detecteren. Deze tijdreeks heeft twee verschillende niveauveranderingen, en drie pieken. De rode stippen geven de tijd weer waarop de niveauverandering wordt gedetecteerd, terwijl de zwarte stippen de gedetecteerde pieken weergeven.
![Score-API][1]

### <a name="detectors"></a>Detectoren
De API voor anomaliedetectie ondersteunt detectoren in drie brede categorieën. Details over specifieke invoerparameters en uitgangen voor elke detector zijn te vinden in de volgende tabel.

| Detectorcategorie | Detector | Beschrijving | Invoerparameters | Uitvoer |
| --- | --- | --- | --- | --- |
| Spike Detectoren |TSpike Detector |Detecteren pieken en dips op basis van ver de waarden zijn van de eerste en derde kwartielen |*tspikedetector.sensitivity:* neemt gehele waarde in het bereik 1-10, standaard: 3; Hogere waarden zullen extremere waarden vangen waardoor het minder gevoelig wordt |TSpike: binaire waarden – '1' als een spike/dip wordt gedetecteerd, '0' anders |
| Spike Detectoren | ZSpike Detector |Detecteer pieken en dalen op basis van hoe ver de datapunten zijn van hun gemiddelde |*zspikedetector.sensitivity:* neem gehele waarde in het bereik 1-10, standaard: 3; Hogere waarden zullen vangen meer extreme waarden waardoor het minder gevoelig |ZSpike: binaire waarden – '1' als een spike/dip wordt gedetecteerd, '0' anders |
| Slow Trend Detector |Slow Trend Detector |Detecteer langzame positieve trend volgens de ingestelde gevoeligheid |*trenddetector.sensitivity:* drempel op detectorscore (standaard: 3,25, 3,25 – 5 is een redelijk bereik om uit te kiezen; Hoe hoger hoe minder gevoelig) |tscore: zwevend getal dat anomaliescore op trend vertegenwoordigt |
| Niveauveranderingsdetectoren | Bidirectionele niveauveranderingsdetector |Detecteer zowel opwaartse als neerwaartse verandering per de ingestelde gevoeligheid |*bileveldetector.sensitivity:* drempel op detectorscore (standaard: 3,25, 3,25 – 5 is een redelijk bereik om uit te kiezen; Hoe hoger hoe minder gevoelig) |rpscore: zwevend getal dat anomaliescore op opwaartse en neerwaartse niveau verandering vertegenwoordigt |

### <a name="parameters"></a>Parameters
Meer gedetailleerde informatie over deze invoerparameters wordt vermeld in de onderstaande tabel:

| Invoerparameters | Beschrijving | Standaardinstelling | Type | Geldig bereik | Voorgesteld bereik |
| --- | --- | --- | --- | --- | --- |
| detectoren.historywindow |Geschiedenis (in nummer van gegevenspunten) die wordt gebruikt voor de berekening van de anomaliescore |500 |geheel getal |10-2000 |Afhankelijkvan de tijdreeksen |
| detectoren.spikesdips | Of alleen pieken, alleen dips of beide |Beide |Opgesomde |Beide, Spikes, Dips |Beide |
| bileveldetector.sensitiviteit |Gevoeligheid voor bidirectionele niveau verandering detector. |3.25 |double |Geen |3.25-5 (Mindere waarden betekenen gevoeliger) |
| trenddetector.sensitivity |Gevoeligheid voor positieve trenddetector. |3.25 |double |Geen |3.25-5 (Mindere waarden betekenen gevoeliger) |
| tspikedetector.gevoeligheid |Gevoeligheid voor TSpike Detector |3 |geheel getal |1-10 |3-5 (Mindere waarden betekenen gevoeliger) |
| zspikedetector.gevoeligheid |Gevoeligheid voor ZSpike Detector |3 |geheel getal |1-10 |3-5 (Mindere waarden betekenen gevoeliger) |
| postprocess.tailRows |Aantal van de meest recente gegevenspunten die in de outputresultaten moeten worden bewaard |0 |geheel getal |0 (bewaar alle gegevenspunten) of geef het aantal punten op dat de resultaten moeten behouden |N.v.t. |

### <a name="output"></a>Uitvoer
De API draait alle detectoren op uw tijdreeksgegevens en retourneert anomaliescores en binaire piekindicatoren voor elk punt in de tijd. In de onderstaande tabel worden uitvoer uit de API weergegeven.

| Uitvoer | Beschrijving |
| --- | --- |
| Time |Tijdstempels uit ruwe gegevens of geaggregeerde (en/of) toegerekende gegevens als aggregatie (en/of) ontbrekende gegevenstoerekening wordt toegepast |
| Gegevens |Waarden uit ruwe gegevens of geaggregeerde (en/of) toegerekende gegevens als aggregatie (en/of) ontbrekende gegevenstoerekening wordt toegepast |
| TSpike TSpike |Binaire indicator om aan te geven of een piek wordt gedetecteerd door TSpike Detector |
| ZSpike ZSpike ZSpike |Binaire indicator om aan te geven of een piek wordt gedetecteerd door ZSpike Detector |
| rpscore |Een zwevend getal dat een anomaliescore vertegenwoordigt op bidirectionele niveauwijziging |
| rpalert |1/0 waarde die aangeeft dat er een bidirectionele niveauveranderingsafwijking is op basis van de invoergevoeligheid |
| tscore |Een zwevend getal dat een afwijkende score op positieve trend vertegenwoordigt |
| talert |1/0 waarde die aangeeft dat er een positieve trendafwijking is gebaseerd op de invoergevoeligheid |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
De ScoreWithSeasonality API wordt gebruikt voor het uitvoeren van anomaliedetectie op tijdreeksen met seizoenspatronen. Deze API is handig om afwijkingen in seizoenspatronen te detecteren.
De volgende afbeelding toont een voorbeeld van afwijkingen die in een seizoensgebonden tijdreeks worden gedetecteerd. De tijdreeks heeft één piek (de eerste zwarte stip), twee dips (de tweede zwarte stip en één aan het einde) en één niveauverandering (rode stip). Zowel de dip in het midden van de tijdreeks als de niveauverandering zijn pas waarneembaar nadat seizoenscomponenten uit de serie zijn verwijderd.
![Seizoens-API][2]

### <a name="detectors"></a>Detectoren
De detectoren in het seizoenseindpunt zijn vergelijkbaar met die in het eindpunt van niet-seizoensgebondenheid, maar met iets andere parameternamen (hieronder vermeld).

### <a name="parameters"></a>Parameters

Meer gedetailleerde informatie over deze invoerparameters wordt vermeld in de onderstaande tabel:

| Invoerparameters | Beschrijving | Standaardinstelling | Type | Geldig bereik | Voorgesteld bereik |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Aggregatie-interval in seconden voor het aggregeren van invoertijdreeksen |0 (er wordt geen aggregatie uitgevoerd) |geheel getal |0: aggregatie overslaan, > 0 anders |5 minuten tot 1 dag, tijd-serie afhankelijk |
| preprocess.aggregationFunc |Functie die wordt gebruikt voor het aggregeren van gegevens naar het opgegeven aggregatieinterval |gemiddeld |Opgesomde |gemiddelde, som, lengte |N.v.t. |
| voorproces.vervangenOntbreken |Waarden die worden gebruikt om ontbrekende gegevens te beschuldigen |lkv (laatst bekende waarde) |Opgesomde |nul, lkv, gemiddelde |N.v.t. |
| detectoren.historywindow |Geschiedenis (in nummer van gegevenspunten) die wordt gebruikt voor de berekening van de anomaliescore |500 |geheel getal |10-2000 |Afhankelijkvan de tijdreeksen |
| detectoren.spikesdips | Of alleen pieken, alleen dips of beide |Beide |Opgesomde |Beide, Spikes, Dips |Beide |
| bileveldetector.sensitiviteit |Gevoeligheid voor bidirectionele niveau verandering detector. |3.25 |double |Geen |3.25-5 (Mindere waarden betekenen gevoeliger) |
| postrenddetector.sensitiviteit |Gevoeligheid voor positieve trenddetector. |3.25 |double |Geen |3.25-5 (Mindere waarden betekenen gevoeliger) |
| negtrenddetector.sensitiviteit |Gevoeligheid voor negatieve trenddetector. |3.25 |double |Geen |3.25-5 (Mindere waarden betekenen gevoeliger) |
| tspikedetector.gevoeligheid |Gevoeligheid voor TSpike Detector |3 |geheel getal |1-10 |3-5 (Mindere waarden betekenen gevoeliger) |
| zspikedetector.gevoeligheid |Gevoeligheid voor ZSpike Detector |3 |geheel getal |1-10 |3-5 (Mindere waarden betekenen gevoeliger) |
| seasonality.enable |Of seizoensanalyse moet worden uitgevoerd |waar |booleaans |de waarde True, false |Afhankelijkvan de tijdreeksen |
| seasonality.numSeasonality |Maximum aantal te detecteren periodieke cycli |1 |geheel getal |1, 2 |1-2 |
| seasonality.transform |Of seizoengebonden (en) trendcomponenten moeten worden verwijderd voordat anomaliedetectie wordt toegepast |deseason |Opgesomde |none, deseason, deseasontrend |N.v.t. |
| postprocess.tailRows |Aantal van de meest recente gegevenspunten die in de outputresultaten moeten worden bewaard |0 |geheel getal |0 (bewaar alle gegevenspunten) of geef het aantal punten op dat de resultaten moeten behouden |N.v.t. |

### <a name="output"></a>Uitvoer
De API draait alle detectoren op uw tijdreeksgegevens en retourneert anomaliescores en binaire piekindicatoren voor elk punt in de tijd. In de onderstaande tabel worden uitvoer uit de API weergegeven.

| Uitvoer | Beschrijving |
| --- | --- |
| Time |Tijdstempels uit ruwe gegevens of geaggregeerde (en/of) toegerekende gegevens als aggregatie (en/of) ontbrekende gegevenstoerekening wordt toegepast |
| OrigineleGegevens |Waarden uit ruwe gegevens of geaggregeerde (en/of) toegerekende gegevens als aggregatie (en/of) ontbrekende gegevenstoerekening wordt toegepast |
| Verwerktegegevens |Een van de volgende opties: <ul><li>Seizoengecorrigeerde tijdreeksen als er significante seizoensgebondenheid is gedetecteerd en de seizoensoptie is geselecteerd;</li><li>seizoengezuiverde en gedetrendeerde tijdreeksen als er significante seizoensinvloeden zijn gedetecteerd en deseasontrend-optie is geselecteerd</li><li>anders is deze optie hetzelfde als OriginalData</li> |
| TSpike TSpike |Binaire indicator om aan te geven of een piek wordt gedetecteerd door TSpike Detector |
| ZSpike ZSpike ZSpike |Binaire indicator om aan te geven of een piek wordt gedetecteerd door ZSpike Detector |
| BiLevelChangeScore |Een zwevend getal dat een anomaliescore vertegenwoordigt op niveauwijziging |
| BiLevelChangeAlert |1/0 waarde die aangeeft dat er een niveauveranderingsafwijking is op basis van de invoergevoeligheid |
| PosTrendScore PosTrendScore |Een zwevend getal dat een afwijkende score op positieve trend vertegenwoordigt |
| PosTrendAlert PosTrendAlert |1/0 waarde die aangeeft dat er een positieve trendafwijking is gebaseerd op de invoergevoeligheid |
| NegTrendScore NegTrendScore |Een zwevend getal dat een afwijkende score op negatieve trend vertegenwoordigt |
| NegTrendAlert (NegTrendAlert) |1/0 waarde die aangeeft dat er een negatieve trendafwijking is gebaseerd op de invoergevoeligheid |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

