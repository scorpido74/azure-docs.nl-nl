---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe docker-containers Cognitive Services dichter bij uw gegevens kunnen krijgen.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2020
ms.author: aahi
ms.openlocfilehash: 69984f9dbd94bcdca2e272a5bdebbb7fc1464dae
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86104402"
---
# <a name="container-support-in-azure-cognitive-services"></a>Ondersteuning voor containers in azure Cognitive Services

Met container ondersteuning in azure Cognitive Services kunnen ontwikkel aars gebruikmaken van dezelfde uitgebreide Api's die beschikbaar zijn in Azure en kunnen ze de services die worden geleverd met [docker-containers](https://www.docker.com/what-container)implementeren en hosten. Container ondersteuning is momenteel beschikbaar voor een subset van Azure Cognitive Services, met inbegrip van delen van:

> [!div class="checklist"]
> * [Anomaly Detector][ad-containers]
> * [Computer Vision][cv-containers]
> * [Face][fa-containers]
> * [Form Recognizer][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Speech Service-API][sp-containers]
> * [Text Analytics][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Container opslag is een benadering van software distributie waarbij een toepassing of service, inclusief de bijbehorende afhankelijkheden & configuratie, als container installatie kopie wordt verpakt. Met weinig of geen wijzigingen kan een container installatie kopie worden geïmplementeerd op een container host. Containers zijn geïsoleerd van elkaar en het onderliggende besturings systeem, met een kleinere footprint dan een virtuele machine. Containers kunnen worden geïnstantieerd vanuit container installatie kopieën voor taken op korte termijn en worden verwijderd wanneer deze niet meer nodig zijn.

Cognitive Services resources zijn beschikbaar op [Microsoft Azure](https://azure.microsoft.com). Meld u aan bij de [Azure Portal](https://portal.azure.com/) om Azure-resources te maken en te verkennen voor deze services.

## <a name="features-and-benefits"></a>Functies en voor delen

- **Onveranderbare infra structuur**: DevOps-teams inschakelen om gebruik te maken van een consistente en betrouw bare set bekende systeem parameters, terwijl u kunt aanpassen aan de wijzigingen. Containers bieden de flexibiliteit om te draaien in een voorspelbaar ecosysteem en configuratie drift te voor komen.
- **Controle over gegevens**: klanten toestaan om te kiezen waar deze Cognitive Services hun gegevens verwerken. Dit is essentieel voor klanten die geen gegevens naar de Cloud kunnen verzenden, maar wel toegang nodig hebben tot Cognitive Services technologie. Ondersteunings consistentie in hybride omgevingen: voor gegevens, beheer, identiteit en beveiliging.
- **Controle over model updates**: bieden klanten flexibiliteit in het maken van versies en het bijwerken van modellen die in hun oplossingen zijn geïmplementeerd.
- **Draag bare architectuur**: Schakel het maken van een draag bare toepassings architectuur in die op Azure, on-premises en de rand kan worden geïmplementeerd. Containers kunnen rechtstreeks worden geïmplementeerd in [Azure Kubernetes service](../aks/index.yml), [Azure container instances](../container-instances/index.yml)of op een [Kubernetes](https://kubernetes.io/) -cluster dat is geïmplementeerd op [Azure stack](/azure-stack/operator). Zie [Deploy Kubernetes to Azure stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)voor meer informatie.
- **Hoge door Voer/lage latentie**: bieden klanten de mogelijkheid om te schalen voor vereisten voor hoge door Voer en lage latentie door Cognitive Services in te scha kelen om fysiek te sluiten op hun toepassings logica en-gegevens. Containers hebben geen Cap-trans acties per seconde (TPS) en kunnen worden gemaakt om te schalen om de vraag te verwerken als u de benodigde hardwarebronnen opgeeft.
- **Schaal baarheid**: met de steeds groeiende populariteit van container opslag-en container Orchestration-software, zoals Kubernetes; schaal baarheid bevindt zich in de Forefront van technologische voor uitgangen. Voortbouwend op een schaal bare cluster-Foundation, waarbij toepassingen kunnen worden ontwikkeld tot hoge Beschik baarheid.

## <a name="containers-in-azure-cognitive-services"></a>Containers in azure Cognitive Services

Azure Cognitive Services-containers bieden de volgende set docker-containers, die elk een subset van functionaliteit van services in azure Cognitive Services bevat:

| Service | Ondersteunde prijs categorie | Container | Description |
|--|--|--|--|
| [Anomalie detectie][ad-containers] | F0, S0 | **Anomalie detectie** | Met de anomalie detectie-API kunt u afwijkingen in uw time series-gegevens controleren en detecteren met machine learning.<br>[Toegang aanvragen][request-access] |
| [Computer Vision][cv-containers] | F0, S1 | **Lezen** | Hiermee wordt afgedrukte tekst geëxtraheerd uit afbeeldingen van verschillende objecten met verschillende Opper vlakken en achtergronden, zoals bevestigingen, posters en visite kaartjes. De Lees container detecteert ook *handgeschreven tekst* in afbeeldingen en biedt ondersteuning voor PDF/TIFF/meerdere pagina's.<br/><br/>**Belang rijk:** De Lees container werkt momenteel alleen met Engels. |
| [Face][fa-containers] | F0, S0 | **Face** | Detecteert menselijke gezichten in afbeeldingen en identificeert kenmerken, met inbegrip van gezichts bezienswaardigheden (zoals neus en ogen), geslacht, leeftijd en andere computer-voorspelde gezichts functies. Naast detectie kan het gezicht controleren of twee gezichten in dezelfde afbeelding of verschillende afbeeldingen hetzelfde zijn door gebruik te maken van een betrouwbaarheids Score, of de gezichten vergelijken met een Data Base om te zien of er al een vergelijkbaar of identiek gezicht bestaat. Het kan ook gelijksoortige gezichten in groepen indelen met behulp van gedeelde visuele elementen.<br>[Toegang aanvragen][request-access] |
| [Formulier herkenning][fr-containers] | F0, S0 | **Form Recognizer** | Formulier uitleg is van toepassing machine learning technologie om sleutel-waardeparen en tabellen uit formulieren te identificeren en uit te pakken.<br>[Toegang aanvragen][request-access] |
| [LUIS][lu-containers] | F0, S0 | **Luis** ([afbeelding](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Laadt een getraind of gepubliceerd Language Understanding model, ook wel een LUIS-app genoemd, op in een docker-container en biedt toegang tot de query voorspellingen vanuit de API-eind punten van de container. U kunt query logboeken van de container verzamelen en deze weer uploaden naar de [Luis-Portal](https://www.luis.ai) om de nauw keurigheid van de app te verbeteren. |
| [Speech Service-API][sp-containers-stt] | F0, S0 | **Spraak naar tekst** | Transcribeert continue realtime spraak naar tekst. |
| [Speech Service-API][sp-containers-cstt] | F0, S0 | **Custom Speech-naar-tekst** | Transcribeert doorlopend realtime spraak naar tekst met behulp van een aangepast model. |
| [Speech Service-API][sp-containers-tts] | F0, S0 | **Tekst naar spraak** | Converteert tekst naar natuurlijk klinkende spraak. |
| [Speech Service-API][sp-containers-ctts] | F0, S0 | **Aangepaste tekst-naar-spraak** | Hiermee wordt tekst geconverteerd naar een natuurlijk geluids fragment met behulp van een aangepast model. |
| [Text Analytics][ta-containers-keyphrase] | F0, S | **Sleuteltermextractie** ([afbeelding](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Extraheert sleutel zinnen om de belangrijkste punten te identificeren. Bijvoorbeeld, voor de invoertekst 'het eten was heerlijk en de bediening fantastisch' retourneert de API de belangrijkste gespreksonderwerpen: 'eten' en 'bediening fantastisch'. |
| [Text Analytics][ta-containers-language] | F0, S | **Taaldetectie** ([afbeelding](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | Voor Maxi maal 120 talen detecteert u in welke taal de invoer tekst wordt geschreven en rapporteert u één taal code voor elk document dat voor de aanvraag wordt verzonden. De taalcode is gekoppeld aan een score die de sterkte van de score aangeeft. |
| [Text Analytics][ta-containers-sentiment] | F0, S | **Sentimentanalyse v3** ([installatie kopie](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analyseer onbewerkte tekst voor aanwijzingen over positieve of negatieve sentiment. Deze versie van sentiment analyse retourneert sentiment-labels (bijvoorbeeld *positief* of *negatief*) voor elk document en elke zin daarin. |
| [Text Analytics][ta-containers-health] | F0, S | **Text Analytics voor de status** | Medische gegevens uit ongestructureerde klinische tekst extra heren en labelen. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Daarnaast worden sommige containers ondersteund in Cognitive Services [**alles-in-één-**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) bron sleutels. U kunt één Cognitive Services alles-in-één resource maken en dezelfde facturerings sleutel gebruiken voor de ondersteunde services voor de volgende services:

* Computer Vision
* Face
* LUIS
* Text Analytics

## <a name="container-availability-in-azure-cognitive-services"></a>Beschik baarheid van containers in azure Cognitive Services

Azure Cognitive Services-containers zijn openbaar beschikbaar via uw Azure-abonnement en docker-container installatie kopieën kunnen worden opgehaald uit de micro soft Container Registry of docker-hub. U kunt de opdracht [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) gebruiken om een container installatie kopie te downloaden uit het juiste REGI ster.

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Vereisten

U moet voldoen aan de volgende vereisten voordat u Azure Cognitive Services-containers kunt gebruiken:

**Docker-engine**: u moet de docker-Engine lokaal geïnstalleerd hebben. Docker biedt pakketten voor het configureren van de docker-omgeving op [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)en [Windows](https://docs.docker.com/docker-for-windows/). In Windows moet docker worden geconfigureerd voor de ondersteuning van Linux-containers. Docker-containers kunnen ook rechtstreeks worden geïmplementeerd in [Azure Kubernetes service](../aks/index.yml) of [Azure container instances](../container-instances/index.yml).

Docker moet worden geconfigureerd zodat de containers verbinding kunnen maken met en facturerings gegevens kunnen verzenden naar Azure.

**Vertrouwd met micro soft container Registry en docker**: u moet een basis kennis hebben van zowel micro soft container Registry als docker-concepten, zoals registers, opslag plaatsen, containers en container installatie kopieën, evenals kennis van basis `docker` opdrachten.

Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.

Afzonderlijke containers kunnen ook hun eigen vereisten hebben, inclusief de vereisten voor Server-en geheugen toewijzing.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [container recepten](containers/container-reuse-recipe.md) die u kunt gebruiken met de Cognitive Services.

Installeer en verken de functionaliteit van containers in azure Cognitive Services:

* [Anomalie detectie containers][ad-containers]
* [Computer Vision containers][cv-containers]
* [Gezichts containers][fa-containers]
* [Containers voor formulier herkenning][fr-containers]
* [Language Understanding-containers (LUIS)][lu-containers]
* [API-containers voor speech-service][sp-containers]
* [Text Analytics containers][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u