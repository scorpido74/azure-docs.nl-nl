---
title: Over Azure Machine Learning-omgevingen
titleSuffix: Azure Machine Learning
description: Leer in dit artikel de voordelen van machine learning-omgevingen, die reproduceerbare, controleerbare en draagbare afhankelijkheidsdefinities voor machine learning mogelijk maken voor verschillende rekendoelen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064198"
---
# <a name="what-are-azure-machine-learning-environments"></a>Wat zijn Azure Machine Learning-omgevingen?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning-omgevingen geven de Python-pakketten, omgevingsvariabelen en software-instellingen op rond uw trainings- en scoringsscripts. Ze geven ook looptijden op (Python, Spark of Docker). De omgevingen worden beheerd en geversioned entiteiten binnen uw Machine Learning-werkruimte die reproduceerbare, controleerbare en draagbare machine learning-workflows mogelijk maken voor verschillende rekendoelen.

U `Environment` een object op uw lokale gegevensberekening gebruiken om:
* Ontwikkel je trainingsscript.
* Dezelfde omgeving opnieuw gebruiken op Azure Machine Learning Compute voor modeltraining op schaal.
* Implementeer uw model met dezelfde omgeving.

In het volgende diagram ziet u `Environment` hoe u één object gebruiken in zowel uw runconfiguratie, voor training als voor uw gevolgtrekking en implementatieconfiguratie voor implementaties van webservices.

![Diagram van een omgeving in machine learning-werkstroom](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Typen omgevingen

Omgevingen kunnen in grote lijnen worden onderverdeeld in drie categorieën: *samengesteld,* *user-managed*, en *systeem-beheerd*.

Samengestelde omgevingen worden geleverd door Azure Machine Learning en zijn standaard beschikbaar in uw werkruimte. Ze bevatten verzamelingen python-pakketten en -instellingen om u op weg te helpen met verschillende machine learning-frameworks. 

In door gebruikers beheerde omgevingen bent u verantwoordelijk voor het instellen van uw omgeving en het installeren van elk pakket dat uw trainingsscript nodig heeft op het rekendoel. Conda controleert uw omgeving niet en installeert niets voor u. Als u uw eigen omgeving definieert, `azureml-defaults` moet `>= 1.0.45` u de versie als pip-afhankelijkheid aanbieden. Dit pakket bevat de functionaliteit die nodig is om het model als webservice te hosten.

U gebruikt systeembeheeromgevingen wanneer u wilt dat [Conda](https://conda.io/docs/) de Python-omgeving en de scriptafhankelijkheden voor u beheert. De service gaat standaard uit van dit type omgeving, vanwege het nut ervan op externe rekendoelen die niet handmatig kunnen worden geconfigureerd.

## <a name="create-and-manage-environments"></a>Omgevingen maken en beheren

U omgevingen maken door:

* Het definiëren `Environment` van nieuwe objecten, met behulp van een samengestelde omgeving of door het definiëren van uw eigen afhankelijkheden.
* Bestaande `Environment` objecten uit uw werkruimte gebruiken. Deze aanpak zorgt voor consistentie en reproduceerbaarheid met uw afhankelijkheden.
* Importeren uit een bestaande Anaconda-omgevingsdefinitie.
* De Azure Machine Learning CLI gebruiken

Zie voor specifieke codevoorbeelden het gedeelte 'Een omgeving maken' van [Hergebruikomgevingen voor training en implementatie.](how-to-use-environments.md#create-an-environment) Omgevingen zijn ook eenvoudig te beheren via uw werkruimte. Ze bevatten de volgende functionaliteit:

* Omgevingen worden automatisch geregistreerd op uw werkruimte wanneer u een experiment indient. Ze kunnen ook handmatig worden geregistreerd.
* U omgevingen uit uw werkruimte ophalen om te gebruiken voor training of implementatie, of om bewerkingen aan te brengen in de omgevingsdefinitie.
* Met versiebeheer u in de loop van de tijd wijzigingen in uw omgevingen zien, wat reproduceerbaarheid garandeert.
* U Docker-afbeeldingen automatisch vanuit uw omgevingen bouwen.

Zie de sectie 'Omgevingen beheren' van [Hergebruikomgevingen voor training en implementatie voor](how-to-use-environments.md#manage-environments)codevoorbeelden.

## <a name="environment-building-caching-and-reuse"></a>Milieugebouw, caching en hergebruik

De Azure Machine Learning-service bouwt omgevingsdefinities in Docker-afbeeldingen en conda-omgevingen. Het caches ook de omgevingen, zodat ze kunnen worden hergebruikt in de volgende training loopt en service endpoint implementaties.

### <a name="building-environments-as-docker-images"></a>Omgevingen bouwen als Docker-afbeeldingen

Wanneer u een run voor het eerst indient met een omgeving, roept de Azure Machine Learning-service doorgaans een [ACR Build-taak](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) in voor het Azure Container Registry (ACR) dat is gekoppeld aan de werkruimte. De ingebouwde Docker-afbeelding wordt vervolgens in de cache opgeslagen op de Workspace ACR. Aan het begin van de uitvoering wordt de afbeelding opgehaald door het rekendoel.

De image build bestaat uit twee stappen:

 1. Een basisafbeelding downloaden en stappen docker uitvoeren
 2. Het bouwen van een conda-omgeving volgens conda-afhankelijkheden die zijn opgegeven in de omgevingsdefinitie.

De tweede stap wordt weggelaten als u [door de gebruiker beheerde afhankelijkheden](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py)opgeeft. In dit geval bent u verantwoordelijk voor het installeren van Python-pakketten, door ze in uw basisafbeelding op te nemen of aangepaste Docker-stappen op te geven in de eerste stap. U bent ook verantwoordelijk voor het opgeven van de juiste locatie voor de Python executable.

### <a name="image-caching-and-reuse"></a>Afbeelding seinen en hergebruik

Als u dezelfde omgevingsdefinitie voor een andere run gebruikt, gebruikt de Azure Machine Learning-service de afbeelding in de cache van de Workspace ACR opnieuw. 

Als u de details van een afbeelding in de cache wilt weergeven, gebruikt u de methode [Environment.get_image_details.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-)

Om te bepalen of een afbeelding in de cache opnieuw moet worden gebruikt of een nieuwe moet worden gebouwd, berekent de service [een hashwaarde](https://en.wikipedia.org/wiki/Hash_table) uit de omgevingsdefinitie en vergelijkt deze met de hashes van bestaande omgevingen. De hash is gebaseerd op:
 
 * Waarde van eigenschap basisafbeelding
 * Waarde van de eigenschap aangepaste dockerstappen
 * Lijst met Python-pakketten in Conda-definitie
 * Lijst met pakketten in Spark-definitie 

De hash is niet afhankelijk van de naam of versie van de omgeving. Wijzigingen in de omgevingsdefinitie, zoals het toevoegen of verwijderen van een Python-pakket of het wijzigen van de pakketversie, zorgen ervoor dat de hashwaarde verandert en een afbeelding opnieuw wordt weergegeven. Als u echter gewoon de naam van uw omgeving wijzigt of een nieuwe omgeving maakt met de exacte eigenschappen en pakketten van een bestaande omgeving, blijft de hashwaarde hetzelfde en wordt de afbeelding in de cache gebruikt.

Zie het volgende diagram met drie omgevingsdefinities. Twee van hen hebben verschillende naam en versie, maar identieke basisafbeelding en Python pakketten. Ze hebben dezelfde hash en komen daarom overeen met dezelfde afbeelding in de cache. De derde omgeving heeft verschillende Python-pakketten en -versies en komt daarom overeen met een andere afbeelding in de cache.

![Diagram van caches als Docker-afbeeldingen](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Als u bijvoorbeeld ```numpy```een omgeving maakt met een niet-vastgemaakte pakketafhankelijkheid, blijft die omgeving de pakketversie gebruiken die is geïnstalleerd op het moment van het maken van de _omgeving._ Ook elke toekomstige omgeving met overeenkomende definitie zal blijven gebruiken van de oude versie. 

Als u het pakket wilt bijwerken, geeft u ```numpy==1.18.1```bijvoorbeeld een versienummer op om het opnieuw opbouwen van afbeeldingen te forceren. Houd er rekening mee dat er nieuwe afhankelijkheden, waaronder geneste afhankelijkheden worden geïnstalleerd die een eerder werkend scenario kunnen breken.

> [!WARNING]
>  De [methode Environment.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) herbouwt de afbeelding in de cache, met mogelijk neveneffect van het bijwerken van niet-vastgemaakte pakketten en het doorbreken van reproduceerbaarheid voor alle omgevingsdefinities die overeenkomen met die afbeelding in de cache.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken en gebruiken van omgevingen](how-to-use-environments.md) in Azure Machine Learning.
* Zie de Python SDK-referentiedocumentatie voor de [omgevingsklasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Zie de R SDK-referentiedocumentatie voor [omgevingen](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
