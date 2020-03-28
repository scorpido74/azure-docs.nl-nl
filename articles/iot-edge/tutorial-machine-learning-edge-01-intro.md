---
title: 'Zelfstudie: Gedetailleerde walkthrough van Machine Learning op Azure IoT Edge'
description: Een zelfstudie op hoog niveau die door de verschillende taken loopt die nodig zijn om een end-to-end, machine learning aan de rand te maken.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 965c420fa29c4cf82517148c01e17d6d7dd6ea97
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74106505"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Zelfstudie: een end-to-end-oplossing met Azure Machine Learning en IoT Edge

Vaak willen IoT-toepassingen profiteren van de intelligente cloud en de intelligente rand. In deze zelfstudie doorlopen we u door een machine learning-model met gegevens die zijn verzameld van IoT-apparaten in de cloud, het implementeren van dat model naar IoT Edge en het periodiek onderhouden en verfijnen van het model.

Het primaire doel van deze zelfstudie is om de verwerking van IoT-gegevens te introduceren met machine learning, met name op de rand. Hoewel we veel aspecten van een algemene machine learning-workflow aanraken, is deze zelfstudie niet bedoeld als een diepgaande introductie tot machine learning. Als voorbeeld proberen we geen sterk geoptimaliseerd model voor de use case te maken - we doen gewoon genoeg om het proces van het maken en gebruiken van een levensvatbaar model voor IoT-gegevensverwerking te illustreren.

## <a name="target-audience-and-roles"></a>Doelgroep en rollen

Deze set artikelen is bedoeld voor ontwikkelaars zonder eerdere ervaring in IoT-ontwikkeling of machine learning. Het implementeren van machine learning aan de rand vereist kennis van het verbinden van een breed scala aan technologieën. Daarom omvat deze zelfstudie een volledig end-to-end scenario om een manier aan te tonen om deze technologieën samen te voegen voor een IoT-oplossing. In een echte omgeving kunnen deze taken worden verdeeld over verschillende mensen met verschillende specialisaties. Ontwikkelaars zouden zich bijvoorbeeld richten op apparaat- of cloudcode, terwijl gegevenswetenschappers de analysemodellen hebben ontworpen. Om een individuele ontwikkelaar in staat te stellen deze zelfstudie met succes te voltooien, hebben we aanvullende richtlijnen voorzien van inzichten en links naar meer informatie die we hopen dat voldoende is om te begrijpen wat er wordt gedaan, evenals waarom.

U ook samenwerken met collega's van verschillende rollen om de tutorial samen te volgen, uw volledige expertise te dragen en als team te leren hoe dingen bij elkaar passen.

In beide gevallen, om te helpen oriënteren van de lezer (s), elk artikel in deze tutorial geeft de rol van de gebruiker. Deze rollen omvatten:

* Cloudontwikkeling (inclusief een cloudontwikkelaar die in een DevOps-capaciteit werkt)
* Gegevensanalyse

## <a name="use-case-predictive-maintenance"></a>Use case: Voorspellend onderhoud

We baseerden dit scenario op een use case gepresenteerd op de Conferentie over Prognostics and Health Management (PHM08) in 2008. Het doel is om de resterende levensduur (RUL) van een set turbofan vliegtuigmotoren te voorspellen. Deze gegevens werden gegenereerd met behulp van C-MAPSS, de commerciële versie van MAPSS (Modular Aero-Propulsion System Simulation) software. Deze software biedt een flexibele turbofan motor simulatie omgeving om gemakkelijk te simuleren de gezondheid, controle, en motor parameters.

De gegevens die worden gebruikt in deze tutorial is afkomstig uit de [Turbofan motor degradatie simulatie gegevensset](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Uit het leesme-bestand:

***Experimenteel scenario***

*Gegevenssets bestaan uit meerdere multivariate tijdreeksen. Elke dataset wordt verder onderverdeeld in trainings- en testsubsets. Elke keer serie is van een andere motor - dat wil zeggen, de gegevens kunnen worden beschouwd als afkomstig van een vloot van motoren van hetzelfde type. Elke motor begint met verschillende graden van initiële slijtage en productie variatie die onbekend is voor de gebruiker. Deze slijtage en variatie wordt beschouwd als normaal, dat wil zeggen, het wordt niet beschouwd als een fout voorwaarde. Er zijn drie operationele instellingen die een aanzienlijk effect hebben op de prestaties van de motor. Deze instellingen zijn ook opgenomen in de gegevens. De gegevens zijn verontreinigd met sensorruis.*

*De motor werkt normaal aan het begin van elke tijdreeks en ontwikkelt een fout op wat punt tijdens de reeks. In de trainingsset wordt de fout groter totdat het systeem uitvalt. In de testset eindigt de tijdreeks enige tijd voordat het systeem uitvalt. Het doel van de wedstrijd is om het aantal resterende operationele cycli te voorspellen voordat de testset niet is mislukt, d.w.z. het aantal operationele cycli na de laatste cyclus dat de motor zal blijven werken. Ook een vector van echte resterende nuttige levensduur (RUL) waarden voor de testgegevens.*

Omdat de gegevens voor een wedstrijd zijn gepubliceerd, zijn verschillende benaderingen om machine learning-modellen af te leiden onafhankelijk gepubliceerd. We ontdekten dat het bestuderen van voorbeelden nuttig is bij het begrijpen van het proces en de redenering die betrokken zijn bij het maken van een specifiek machine learning-model. Zie bijvoorbeeld:

[Vliegtuig motor storing voorspelling model](https://github.com/jancervenka/turbofan_failure) door GitHub gebruiker jancervenka.

[Turbofan motor degradatie](https://github.com/hankroark/Turbofan-Engine-Degradation) door GitHub gebruiker hankroark.

## <a name="process"></a>Proces

De foto hieronder illustreert de ruwe stappen die we volgen in deze tutorial:

![Architectuurdiagram voor processtappen](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Trainingsgegevens verzamelen**: Het proces begint met het verzamelen van trainingsgegevens. In sommige gevallen zijn gegevens al verzameld en beschikbaar in een database, of in de vorm van gegevensbestanden. In andere gevallen, met name voor IoT-scenario's, moeten de gegevens worden verzameld van IoT-apparaten en sensoren en in de cloud worden opgeslagen.

   We gaan ervan uit dat u geen verzameling turbofan-motoren hebt, dus de projectbestanden bevatten een eenvoudige apparaatsimulator die de NASA-apparaatgegevens naar de cloud stuurt.

1. **Gegevens voorbereiden**. In de meeste gevallen zullen de ruwe gegevens zoals verzameld van apparaten en sensoren voorbereiding voor machine learning vereisen. Deze stap kan betrekking hebben op het opruimen van gegevens, het opnieuw formatteren van gegevens of het voorbewerken om extra informatie te injecteren die machine learning kan uitschakelen.

   Voor onze gegevens van de vliegtuigmotormachine omvat gegevensvoorbereiding het berekenen van expliciete tijd-tot-uitvaltijden voor elk gegevenspunt in de steekproef op basis van de werkelijke waarnemingen op de gegevens. Met deze informatie kan het machine learning-algoritme correlaties vinden tussen werkelijke sensorgegevenspatronen en de verwachte resterende levensduur van de engine. Deze stap is zeer domeinspecifiek.

1. **Bouw een machine learning model**. Op basis van de voorbereide gegevens kunnen we nu experimenteren met verschillende machine learning-algoritmen en parameterisaties om modellen te trainen en de resultaten met elkaar te vergelijken.

   In dit geval vergelijken we voor het testen de voorspelde uitkomst berekend door het model met de werkelijke uitkomst die wordt waargenomen op een reeks motoren. In Azure Machine Learning kunnen we de verschillende iteraties beheren van modellen die we in een modelregister maken.

1. **Implementeer het model**. Zodra we een model hebben dat voldoet aan onze succescriteria, kunnen we overgaan tot implementatie. Dat houdt in het verpakken van het model in een web service app die kan worden gevoed met gegevens met behulp van REST-oproepen en rendement analyse resultaten. De webservice-app wordt vervolgens verpakt in een dockercontainer, die op zijn beurt kan worden geïmplementeerd in de cloud of als een IoT Edge-module. In dit voorbeeld richten we ons op implementatie naar IoT Edge.

1. **Het model onderhouden en verfijnen**. Ons werk wordt niet gedaan zodra het model is geïmplementeerd. In veel gevallen willen we doorgaan met het verzamelen van gegevens en die gegevens periodiek uploaden naar de cloud. We kunnen deze gegevens vervolgens gebruiken om ons model om te scholen en te verfijnen, dat we vervolgens kunnen herschikken naar IoT Edge.

## <a name="prerequisites"></a>Vereisten

Als u de zelfstudie wilt voltooien, hebt u toegang nodig tot een Azure-abonnement waarin u rechten hebt om resources te maken. Verschillende van de services die in deze zelfstudie worden gebruikt, worden azure-kosten in rekening gebracht. Als u nog geen Azure-abonnement hebt, u mogelijk aan de slag met een [Gratis Azure-account.](https://azure.microsoft.com/offers/ms-azr-0044p/)

U hebt ook een machine nodig waarop PowerShell is geïnstalleerd, waar u scripts uitvoeren om een Azure Virtual Machine in te stellen als uw ontwikkelingsmachine.

In dit document gebruiken we de volgende set hulpprogramma's:

* Een Azure IoT-hub voor het vastleggen van gegevens

* Azure Notebooks als onze belangrijkste front-end voor gegevensvoorbereiding en machine learning-experimenten. Het uitvoeren van python-code in een notitieblok op een subset van de voorbeeldgegevens is een geweldige manier om snel iteratief en interactief te worden tijdens de voorbereiding van gegevens. Jupyter-notitieblokken kunnen ook worden gebruikt om scripts voor te bereiden om op schaal te worden uitgevoerd in een compute backend.

* Azure Machine Learning als back-end voor machine learning op schaal en voor machine learning image generation. We rijden de Azure Machine Learning backend met scripts die zijn voorbereid en getest in Jupyter-notitieblokken.

* Azure IoT Edge voor off-cloud toepassing van een machine learning-afbeelding

Uiteraard zijn er andere opties beschikbaar. In bepaalde scenario's kan IoT Central bijvoorbeeld worden gebruikt als een no-code alternatief om initiële trainingsgegevens van IoT-apparaten vast te leggen.

## <a name="next-steps"></a>Volgende stappen

Deze zelfstudie is onderverdeeld in de volgende secties:

1. Stel uw ontwikkelmachine en Azure-services in.
2. Genereer de trainingsgegevens voor de machine learning-module.
3. Train en implementeer de machine learning module.
4. Configureer een IoT Edge-apparaat om als transparante gateway te fungeren.
5. IoT Edge-modules maken en implementeren.
6. Gegevens verzenden naar uw IoT Edge-apparaat.

Ga verder naar het volgende artikel om een ontwikkelmachine in te stellen en Azure-resources in te richten.

> [!div class="nextstepaction"]
> [Een omgeving instellen voor machine learning op IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)
