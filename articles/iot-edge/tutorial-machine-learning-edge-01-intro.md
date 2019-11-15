---
title: 'Zelf studie: gedetailleerd overzicht van Machine Learning op Azure IoT Edge'
description: Een hoogwaardige zelf studie die de verschillende taken doorloopt die nodig zijn om een end-to-end-, machine learning te maken in het rand scenario.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 965c420fa29c4cf82517148c01e17d6d7dd6ea97
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106505"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Zelf studie: een end-to-end oplossing met Azure Machine Learning en IoT Edge

Vaak willen IoT-toepassingen profiteren van de intelligente Cloud en de intelligente Edge. In deze zelf studie wordt u begeleid bij het trainen van een machine learning model met gegevens die zijn verzameld van IoT-apparaten in de Cloud, waarbij u dat model implementeert om te IoT Edge en het model regel matig te onderhouden en te verfijnen.

Het belangrijkste doel van deze zelf studie is het introduceren van de verwerking van IoT-gegevens met machine learning, met name aan de rand. Hoewel we veel aspecten van een algemene machine learning werk stroom aanraken, is deze zelf studie niet bedoeld als een diep gaande Inleiding tot machine learning. Als in punt wordt niet geprobeerd een model met hoge betrouw baarheid te maken voor de use-case. we doen net genoeg om het proces van het maken en gebruiken van een levensvatbaar model voor IoT-gegevens verwerking te illustreren.

## <a name="target-audience-and-roles"></a>Doel groep en-rollen

Deze set artikelen is bedoeld voor ontwikkel aars zonder eerdere ervaring in IoT-ontwikkeling of machine learning. Voor de implementatie van machine learning aan de rand is kennis nodig van het aansluiten van een breed scala aan technologieën. Deze zelf studie bevat daarom een volledig end-to-end-scenario om te laten zien hoe u deze technologieën samen kunt koppelen aan een IoT-oplossing. In een praktijk omgeving kunnen deze taken worden verdeeld over verschillende personen met verschillende specials. Ontwikkel aars richten zich bijvoorbeeld op het apparaat of de Cloud code, terwijl gegevens wetenschappers de analyse modellen hebben ontworpen. Om ervoor te zorgen dat een individuele ontwikkelaar deze zelf studie kan volt ooien, hebben we aanvullende richt lijnen gegeven met inzichten en koppelingen naar meer informatie die we nodig hebben om te begrijpen wat er gebeurt, en waarom.

U kunt ook samen werken met collega's van verschillende rollen om de zelf studie te volgen, uw volledige expertise over te brengen en als een team te leren hoe ze bij elkaar passen.

In beide gevallen wordt in elk artikel in deze zelf studie de rol van de gebruiker weer gegeven om te helpen bij het richten van de lezer (s). Deze rollen zijn onder andere:

* Cloud ontwikkeling (inclusief een Cloud ontwikkelaar die werkt in een DevOps-capaciteit)
* Gegevensanalyse

## <a name="use-case-predictive-maintenance"></a>Use-case: voor speld onderhoud

We zijn op dit scenario gebaseerd op een gebruiks voorbeeld dat wordt gepresenteerd in de conferentie over Prognostics and Health Management (PHM08) in 2008. Het doel is om de resterende nuttige levens duur (resterende levens duur) van een set turbofan vliegtuig motoren te voors pellen. Deze gegevens zijn gegenereerd met C-kaarten, de commerciële versie van MAPS (modulair Aero-voortstuwing systeem simulatie) software. Deze software biedt een flexibele turbofan-engine simulatie omgeving waarmee u de status, controle en Engine-para meters eenvoudig kunt simuleren.

De gegevens die in deze zelf studie worden gebruikt, worden opgehaald uit de [turbofan engine-simulatie gegevensverzameling](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

In het Leesmij-bestand:

***Experimentele scenario***

*Gegevens sets bestaan uit meerdere multidimensionale-tijd reeksen. Elke gegevensset wordt verder onderverdeeld in trainings-en test subsets. Elke tijd reeks is afkomstig van een andere engine, dat wil zeggen dat de gegevens kunnen worden beschouwd als een vloot van engines van hetzelfde type. Elke engine begint met een afwijkende mate van eerste slijtage en productie variaties die voor de gebruiker onbekend zijn. Deze slijtage en variant worden normaal gezien, dat wil zeggen, niet als een probleem voorwaarde. Er zijn drie operationele instellingen die een aanzienlijk effect hebben op de prestaties van de engine. Deze instellingen zijn ook opgenomen in de gegevens. De gegevens worden verontreinigd met sensor ruis.*

*De engine werkt normaal aan het begin van elke tijd reeks en ontwikkelt een fout op een bepaald moment tijdens de serie. In de Trainingsset wordt de fout in omvang verg root tot systeem storingen. In de testset eindigt de tijd serie enige tijd voordat de systeem fout is opgetreden. Het doel van de concurrentie is het voors pellen van het aantal resterende operationele cycli vóór het mislukken van de testset, d.w.z. het aantal operationele cycli na de laatste cyclus dat de engine actief blijft. Daarnaast hebt u ook een vector gegeven van de resterende levens duur-waarden (waar resterende nuttige levens duur) voor de test gegevens.*

Omdat de gegevens zijn gepubliceerd voor een competitie, zijn verschillende benaderingen voor het afleiden van machine learning modellen onafhankelijk gepubliceerd. We hebben vastgesteld dat de studie voorbeelden nuttig zijn bij het maken van een specifiek machine learning model. Zie bijvoorbeeld:

[Voorspellings model voor de vliegtuig motor](https://github.com/jancervenka/turbofan_failure) door github User jancervenka.

[Degradatie van turbofan-engine](https://github.com/hankroark/Turbofan-Engine-Degradation) door github User hankroark.

## <a name="process"></a>Proces

In de onderstaande afbeelding ziet u de ruwe stappen die we in deze zelf studie volgen:

![Architectuur diagram voor proces stappen](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Trainings gegevens verzamelen**: het proces begint met het verzamelen van trainings gegevens. In sommige gevallen zijn gegevens al verzameld en beschikbaar in een Data Base, of in de vorm van gegevens bestanden. In andere gevallen, met name voor IoT-scenario's, moeten de gegevens worden verzameld van IoT-apparaten en Sens oren en worden opgeslagen in de Cloud.

   We gaan ervan uit dat u geen verzameling turbofan-engines hebt, zodat de project bestanden een eenvoudige Device Simulator bevatten die de gegevens van de NASA-apparaten naar de Cloud verzendt.

1. **Gegevens voorbereiden**. In de meeste gevallen vereisen de onbewerkte gegevens die worden verzameld van apparaten en Sens oren, voor bereiding voor machine learning. Deze stap kan het opschonen van gegevens, het opnieuw Format teren van gegevens of de voor verwerking van extra informatie omvatten machine learning kan worden uitgeschakeld.

   Voor de computer gegevens van onze vliegtuig motor moeten gegevens voorbereiding een expliciete time-to-failure tijd berekenen voor elk gegevens punt in het voor beeld op basis van de werkelijke waarnemingen van de gegevens. Met deze informatie kan het machine learning-algoritme correlaties tussen de werkelijke sensor gegevens patronen en de verwachte resterende levens duur van de engine vinden. Deze stap is zeer specifiek voor een domein.

1. **Bouw een machine learning model**. Op basis van de voor bereide gegevens kunnen we nu experimenteren met verschillende machine learning algoritmen en parameterizations om modellen te trainen en de resultaten met elkaar te vergelijken.

   In dit geval vergelijken we het voorspelde resultaat dat door het model is berekend met het reële resultaat dat is waargenomen voor een set engines. In Azure Machine Learning kunnen we de verschillende herhalingen van modellen die we in een model register maken, beheren.

1. **Implementeer het model**. Zodra we een model hebben dat voldoet aan onze criteria voor succes, kunnen we overstappen op de implementatie. Hiervoor moet u het model inpakken in een webservice-app die kan worden ingevoerd met gegevens met behulp van REST-aanroepen en analyse resultaten retour neren. De web service-app wordt vervolgens ingepakt in een docker-container, die op zijn beurt kan worden geïmplementeerd in de Cloud of als een IoT Edge module. In dit voor beeld is de implementatie gericht op het IoT Edge.

1. **Het model onderhouden en verfijnen**. Het werk wordt niet uitgevoerd wanneer het model is geïmplementeerd. In veel gevallen willen we door gaan met het verzamelen van gegevens en deze gegevens periodiek uploaden naar de Cloud. We kunnen deze gegevens vervolgens gebruiken om ons model opnieuw te trainen en te verfijnen. Dit kan vervolgens opnieuw worden geïmplementeerd voor IoT Edge.

## <a name="prerequisites"></a>Vereisten

Als u de zelf studie wilt volt ooien, moet u toegang hebben tot een Azure-abonnement waarin u de benodigde rechten hebt om resources te maken. Voor diverse services die in deze zelf studie worden gebruikt, worden Azure-kosten in rekening gebracht. Als u nog geen Azure-abonnement hebt, kunt u aan de slag met een [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/).

U hebt ook een machine nodig waarop Power shell is geïnstalleerd, waar u scripts kunt uitvoeren om een virtuele Azure-machine in te stellen als uw ontwikkel machine.

In dit document gebruiken we de volgende set hulpprogram ma's:

* Een Azure IoT hub voor het vastleggen van gegevens

* Azure Notebooks als de belangrijkste front-end voor het voorbereiden van gegevens en machine learning experimenten. Het uitvoeren van python-code in een notebook op een subset van de voorbeeld gegevens is een uitstekende manier om snel iteratieve en interactieve verwerkings tijd te verkrijgen tijdens de voor bereiding van gegevens. Jupyter-notebooks kunnen ook worden gebruikt om scripts voor te bereiden die op schaal worden uitgevoerd in een compute-back-end.

* Azure Machine Learning als back-end voor machine learning op schaal en voor het genereren van machine learning installatie kopie. We sturen de Azure Machine Learning back-end met behulp van scripts die zijn voor bereid en getest in Jupyter-notebooks.

* Azure IoT Edge voor een off-Cloud toepassing van een machine learning installatie kopie

Er zijn uiteraard andere opties beschikbaar. In bepaalde scenario's kan IoT Central bijvoorbeeld worden gebruikt als alternatief voor het vastleggen van de eerste trainings gegevens van IoT-apparaten.

## <a name="next-steps"></a>Volgende stappen

Deze zelf studie is onderverdeeld in de volgende secties:

1. Stel uw ontwikkel machine en Azure-Services in.
2. Genereer de trainings gegevens voor de module machine learning.
3. Train en implementeer de machine learning-module.
4. Een IoT Edge apparaat configureren dat als transparante gateway fungeert.
5. IoT Edge modules maken en implementeren.
6. Gegevens verzenden naar uw IoT Edge-apparaat.

Ga door naar het volgende artikel om een ontwikkel machine in te stellen en Azure-resources in te richten.

> [!div class="nextstepaction"]
> [Een omgeving instellen voor machine learning op IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)
