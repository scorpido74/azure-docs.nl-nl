---
title: Over Azure Machine Learning omgevingen
titleSuffix: Azure Machine Learning
description: In dit artikel vindt u meer informatie over de voor delen van machine learning omgevingen, waarmee u reproduceer bare, audit bare en Portable machine learning afhankelijkheids definities kunt gebruiken in verschillende reken doelen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 036efa27fb8d22c32f2f6bce1efe9dea300a3972
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302764"
---
# <a name="what-are-azure-machine-learning-environments"></a>Wat zijn Azure Machine Learning omgevingen?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In Azure Machine Learning omgevingen worden de Python-pakketten, omgevings variabelen en software-instellingen voor uw trainings-en Score scripts opgegeven. Ze specificeren ook uitvoerings tijden (python, Spark of docker). De omgevingen zijn beheerde en geversiede entiteiten in uw Machine Learning-werk ruimte waarmee u reproduceer bare, audit bare en Portable machine learning werk stromen kunt maken in verschillende reken doelen.

U kunt een `Environment`-object op uw lokale Compute gebruiken om het volgende te doen:
* Ontwikkel uw trainings script.
* Gebruik dezelfde omgeving op Azure Machine Learning Compute for model-training op schaal.
* Implementeer uw model met diezelfde omgeving.

In het volgende diagram ziet u hoe u een enkel `Environment`-object kunt gebruiken in zowel uw uitvoerings configuratie, voor training, als uw detrain-en implementatie configuratie voor webservice-implementaties.

![Diagram van een omgeving in machine learning werk stroom](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Typen omgevingen

Omgevingen kunnen breed worden onderverdeeld in drie categorieën: met *curator*, door de *gebruiker beheerd*en door *het systeem beheerd*.

Gehoste omgevingen worden verzorgd door Azure Machine Learning en zijn standaard beschikbaar in uw werk ruimte. Ze bevatten verzamelingen van Python-pakketten en instellingen waarmee u aan de slag kunt met diverse machine learning frameworks. 

In door gebruikers beheerde omgevingen bent u verantwoordelijk voor het instellen van uw omgeving en het installeren van elk pakket dat uw trainings script nodig heeft voor het Compute-doel. Conda controleert uw omgeving niet en installeert niets voor u. Als u uw eigen omgeving definieert, moet u `azureml-defaults` met versie `>= 1.0.45` als een PIP-afhankelijkheid vermelden. Dit pakket bevat de functionaliteit die nodig is om het model als een webservice te hosten.

U gebruikt door het systeem beheerde omgevingen als u wilt dat [Conda](https://conda.io/docs/) de python-omgeving en de script afhankelijkheden voor u beheert. De service gaat ervan uit dat type omgeving standaard, vanwege de bruikbaarheid op externe Compute-doelen die niet hand matig kunnen worden geconfigureerd.

## <a name="create-and-manage-environments"></a>Omgevingen maken en beheren

U kunt omgevingen maken op:

* Het definiëren van nieuwe `Environment`-objecten, hetzij door gebruik te maken van een netwerk met een curator of het definiëren van uw eigen afhankelijkheden.
* Bestaande `Environment`-objecten uit uw werk ruimte gebruiken. Deze benadering maakt consistentie en reproduceer baarheid mogelijk met uw afhankelijkheden.
* Importeren uit een bestaande Anaconda-omgevings definitie.
* De Azure Machine Learning CLI gebruiken

Zie voor voor beelden van specifieke code de sectie ' een omgeving maken ' voor het [hergebruik van omgevingen voor training en implementatie](how-to-use-environments.md#create-an-environment). Omgevingen worden ook eenvoudig beheerd via uw werk ruimte. Ze bevatten de volgende functionaliteit:

* Omgevingen worden automatisch bij uw werk ruimte geregistreerd wanneer u een experiment verzendt. Ze kunnen ook hand matig worden geregistreerd.
* U kunt in uw werk ruimte omgevingen ophalen die u wilt gebruiken voor training of implementatie, of om wijzigingen aan te brengen in de omgevings definitie.
* Met versie beheer kunt u in de loop der tijd wijzigingen in uw omgevingen zien, waardoor de reproduceer baarheid gewaarborgd is.
* U kunt docker-installatie kopieën automatisch bouwen vanuit uw omgevingen.

Voor code voorbeelden, zie de sectie omgevingen beheren van [omgevingen hergebruiken voor training en implementatie](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Omgeving bouwen, in cache plaatsen en opnieuw gebruiken

De Azure Machine Learning-service bouwt omgevings definities in docker-installatie kopieën en Conda-omgevingen. Ook worden de omgevingen in de cache opgeslagen, zodat ze opnieuw kunnen worden gebruikt in volgende trainings uitvoeringen en implementaties van service-eind punten.

### <a name="building-environments-as-docker-images"></a>Omgevingen bouwen als docker-installatie kopieën

Wanneer u voor het eerst een uitvoering met een omgeving verzendt, wordt door de Azure Machine Learning-service doorgaans een [ACR-build-taak](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) aangeroepen in de Azure container Registry (ACR) die aan de werk ruimte is gekoppeld. De ingebouwde docker-installatie kopie wordt vervolgens in de cache opgeslagen in de werk ruimte ACR. Aan het begin van het uitvoeren van de uitvoering wordt de installatie kopie opgehaald door het berekenings doel.

De afbeeldings opbouw bestaat uit twee stappen:

 1. Een basis installatie kopie downloaden en alle docker-stappen uitvoeren
 2. Het bouwen van een Conda-omgeving op basis van Conda-afhankelijkheden die zijn opgegeven in de omgevings definitie.

De tweede stap wordt wegge laten als u door de [gebruiker beheerde afhankelijkheden](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py)opgeeft. In dit geval bent u verantwoordelijk voor het installeren van Python-pakketten door ze op te nemen in uw basis installatie kopie of door aangepaste docker-stappen in de eerste stap op te geven. U bent ook verantwoordelijk voor het opgeven van de juiste locatie voor het uitvoer bare python-bestand.

### <a name="image-caching-and-reuse"></a>Afbeeldingen in cache opslaan en opnieuw gebruiken

Als u dezelfde omgevings definitie gebruikt voor een andere uitvoering, gebruikt de Azure Machine Learning-service de in de cache opgeslagen afbeelding van de werk ruimte ACR. 

Als u de details van een afbeelding in de cache wilt weer geven, gebruikt u de methode [environment. get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) .

Als u wilt bepalen of u een installatie kopie in cache opnieuw wilt gebruiken of een nieuwe afbeelding wilt maken, berekent de service [een hashwaarde](https://en.wikipedia.org/wiki/Hash_table) van de omgevings definitie en vergelijkt deze met de hashes van bestaande omgevingen. De hash is gebaseerd op:
 
 * Eigenschaps waarde basis afbeelding
 * Eigenschaps waarde voor aangepaste docker-stappen
 * Lijst met Python-pakketten in de Conda-definitie
 * Lijst met pakketten in Spark-definitie 

De hash is niet afhankelijk van de naam of versie van de omgeving. Wijzigingen in de omgevings definitie, zoals het toevoegen of verwijderen van een python-pakket of het wijzigen van de pakket versie, zorgt ervoor dat de hash-waarde wordt gewijzigd en een installatie kopie wordt opnieuw opgebouwd. Als u echter eenvoudigweg de naam van uw omgeving wijzigt of een nieuwe omgeving maakt met de exacte eigenschappen en pakketten van een bestaande, blijft de hash-waarde hetzelfde en wordt de afbeelding in de cache gebruikt.

Zie het volgende diagram waarin drie omgevings definities worden weer gegeven. Twee hiervan hebben een andere naam en versie, maar identieke basis installatie kopieën en Python-pakketten. Ze hebben dezelfde hash en komen daarom overeen met dezelfde afbeelding in de cache. De derde omgeving heeft andere Python-pakketten en-versies en komt daarom overeen met een andere kopie in de cache.

![Diagram van het opslaan van omgevingen in de cache als docker-installatie kopieën](./media/concept-environments/environment-caching.png)

Als u een omgeving met een niet-vastgemaakte pakket afhankelijkheid maakt, bijvoorbeeld ```numpy```, blijft die omgeving gebruikmaken van de pakket versie die is geïnstalleerd op het moment dat de omgeving wordt gemaakt. Daarnaast blijft de oude versie van alle toekomstige omgevingen met de overeenkomende definitie bewaard. Als u het pakket wilt bijwerken, moet u een versie nummer opgeven om het opnieuw samen stellen van de installatie kopie af te dwingen, bijvoorbeeld ```numpy==1.18.1```. Houd er rekening mee dat er nieuwe afhankelijkheden, inclusief geneste items, worden geïnstalleerd die een eerder werkend scenario kunnen verstoren

> [!WARNING]
>  Met de methode [environment. build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace-) wordt de installatie kopie in de cache opnieuw opgebouwd, met mogelijk neven effect van het bijwerken van losgemaakte pakketten en het verbreken van de reproduceer baarheid voor alle omgevings definities die overeenkomen met die in de cache opgeslagen afbeelding.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken en gebruiken van omgevingen](how-to-use-environments.md) in azure machine learning.
* Raadpleeg de documentatie van python SDK voor de [omgevings klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Raadpleeg de naslag documentatie over R SDK voor [omgevingen](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
