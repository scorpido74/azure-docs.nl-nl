---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe Docker-containers dichter bij Cognitive Services toegang krijgen tot uw gegevens.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 11/11/2019
ms.author: dapine
ms.openlocfilehash: 2b6118930626ce753060addcf64c568c7c086efb
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091231"
---
# <a name="container-support-in-azure-cognitive-services"></a>Ondersteuning voor containers in Azure Cognitive Services

Ondersteuning voor containers in Azure Cognitive Services kunnen ontwikkelaars gebruikmaken van de dezelfde uitgebreide API's die beschikbaar in Azure zijn en flexibiliteit kunt waar u wilt implementeren en te hosten van de services die worden geleverd met [Docker-containers](https://www.docker.com/what-container). Container ondersteuning is momenteel beschikbaar voor een subset van Azure Cognitive Services, met inbegrip van delen van:

> [!div class="checklist"]
> * [Anomalie detectie][ad-containers]
> * [Computer Vision][cv-containers]
> * [Face][fa-containers]
> * [Formulier herkenning][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Speech Service-API][sp-containers]
> * [Tekstanalyse][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Containerstrategie is een benadering voor softwaredistributie waarin een toepassing of service, inclusief de afhankelijkheden en de configuratie wordt geleverd samen als een containerinstallatiekopie. Met weinig of geen wijziging, kan de installatiekopie van een container worden geïmplementeerd op een containerhost. Containers zijn geïsoleerd van elkaar worden verbonden en het onderliggende besturingssysteem, met een kleinere footprint dan een virtuele machine. Containers kunnen worden geïnstantieerd van containerinstallatiekopieën voor taken op korte termijn en verwijderd wanneer het niet meer nodig hebt.

Cognitive Services resources zijn beschikbaar op [Microsoft Azure](https://azure.microsoft.com). Meld u aan bij de [Azure-portal](https://portal.azure.com/) maken en verken Azure-resources voor deze services.

## <a name="features-and-benefits"></a>Functies en voordelen

- **Onveranderbare infra structuur**: DevOps-teams inschakelen om gebruik te maken van een consistente en betrouw bare set bekende systeem parameters, terwijl u kunt aanpassen aan de wijzigingen. Containers bieden de flexibiliteit om te draaien in een voorspelbaar ecosysteem en configuratie drift te voor komen.
- **Controle over gegevens**: zodat klanten waar deze Cognitive Services hun gegevens verwerken. Dit is essentieel voor klanten die gegevens verzenden naar de cloud, kunnen niet, maar toegang nodig tot Cognitive Services-technologie. Ondersteunings consistentie in hybride omgevingen: voor gegevens, beheer, identiteit en beveiliging.
- **Controle over gegevensmodellen kunnen bijwerken**: bieden klanten flexibiliteit in versiebeheer en bijwerken van modellen die zijn geïmplementeerd in hun oplossingen.
- **Draagbare architectuur**: het maken van een draagbare toepassingsarchitectuur die kan worden geïmplementeerd op Azure, on-premises en edge inschakelen. Containers rechtstreeks naar kunnen worden geïmplementeerd [Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml), of naar een [Kubernetes](https://kubernetes.io/) cluster geïmplementeerd op [Azure Stack](/azure-stack/operator). Zie voor meer informatie, [Kubernetes met Azure Stack implementeren](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Hoge doorvoer / lage latentie**: bieden klanten de mogelijkheid om te schalen voor hoge doorvoer en lage latentie is vereist door het inschakelen van Cognitive Services om uit te voeren fysiek dicht bij hun logica van toepassingen en gegevens. Containers transacties per seconde (TPS) kunnen niet worden gegevenslimiet en kunnen worden gemaakt voor het schalen van zowel en uitbreiden voor het afhandelen van aanvraag als u de benodigde hardwarebronnen opgeven.
- **Schaal baarheid**: met de steeds groeiende populariteit van container opslag-en container Orchestration-software, zoals Kubernetes; schaal baarheid bevindt zich in de Forefront van technologische voor uitgangen. Voortbouwend op een schaal bare cluster-Foundation, waarbij toepassingen kunnen worden ontwikkeld tot hoge Beschik baarheid.

## <a name="containers-in-azure-cognitive-services"></a>Containers in Azure Cognitive Services

Azure Cognitive Services-containers bieden de volgende set Docker-containers, die elk een subset van de functionaliteit van services in Azure Cognitive Services bevat:

| Service | Ondersteunde prijs categorie | Container | Beschrijving |
|---------|----------|----------|-------------|
|[Anomaly Detector][ad-containers] |F0, S0|**Anomalie detectie** |Met de anomalie detectie-API kunt u afwijkingen in uw time series-gegevens controleren en detecteren met machine learning.<br>[Toegang aanvragen](https://aka.ms/adcontainer)|
|[Computer Vision][cv-containers] |F0, S1|**Lezen** |Extraheert gedrukte tekst uit afbeeldingen van verschillende objecten met verschillende oppervlakken en achtergronden, zoals ontvangsten, posters en visitekaartjes. De Lees container detecteert ook *handgeschreven tekst* in afbeeldingen en biedt ondersteuning voor PDF/TIFF/meerdere pagina's.<br/><br/>**Belang rijk:** De Lees container werkt momenteel alleen met Engels.|
|[Face][fa-containers] |F0, S0|**Face** |Detecteert menselijke gezichten in afbeeldingen en -kenmerken, zoals gezichtsoriëntatiepunten (zoals hartstukken en ogen), geslacht, leeftijd en andere machine voorspeld gezichtskenmerken identificeert. Naast detectie controleren Face of twee gezichten in dezelfde afbeelding of andere installatiekopieën zijn hetzelfde met behulp van een betrouwbaarheidsscore of gezichten op een database om te zien als een gelijkende vergelijken of identieke face al bestaat. Deze kunt soortgelijke gezichten ook organiseren in groepen, met behulp van gedeelde visuele kenmerken.<br>[Toegang aanvragen](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Formulier herkenning][fr-containers] |F0, S0|**Formulier herkenning** |Formulier uitleg is van toepassing machine learning technologie om sleutel-waardeparen en tabellen uit formulieren te identificeren en uit te pakken.<br>[Toegang aanvragen](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**LUIS** ([installatiekopie](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Een ervaren of gepubliceerd Language Understanding-model, ook wel bekend als een LUIS-app, worden in een docker-container en biedt toegang tot de voorspellingen van de query van de API-eindpunten van de container. U kunt het verzamelen van Logboeken voor query's uit de container en upload deze terug naar de [LUIS portal](https://www.luis.ai) voor het verbeteren van de nauwkeurigheid van de app.|
|[Speech Service-API][sp-containers-stt] |F0, S0|**Spraak naar tekst** |Transcribeert continue realtime spraak naar tekst.|
|[Speech Service-API][sp-containers-cstt] |F0, S0|**Custom Speech-naar-tekst** |Transcribeert doorlopend realtime spraak naar tekst met behulp van een aangepast model.|
|[Speech Service-API][sp-containers-tts] |F0, S0|**Tekst naar spraak** |Converteert tekst naar natuurlijk klinkende spraak.|
|[Speech Service-API][sp-containers-ctts] |F0, S0|**Aangepaste tekst-naar-spraak** |Hiermee wordt tekst geconverteerd naar een natuurlijk geluids fragment met behulp van een aangepast model.|
|[Tekstanalyse][ta-containers] |F0, S|**Sleutel vindt er sleuteltermextractie plaats** ([installatiekopie](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Extraheert sleuteltermen voor het identificeren van de belangrijkste punten. Bijvoorbeeld, voor de invoertekst 'het eten was heerlijk en de bediening fantastisch' retourneert de API de belangrijkste gespreksonderwerpen: 'eten' en 'bediening fantastisch'. |
|[Tekstanalyse][ta-containers]|F0, S|**Taaldetectie** ([installatiekopie](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Detecteert welke taal de ingevoerde tekst is geschreven en het rapport een code één taal voor elk document dat op de aanvraag ingediend voor maximaal 120 talen. De taalcode is gekoppeld aan een score die de sterkte van de score aangeeft. |
|[Tekstanalyse][ta-containers]|F0, S|**Sentimentanalyse** ([installatiekopie](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Onbewerkte tekst of er aanwijzingen over positief of negatief gevoel analyseert. Deze API retourneert een gevoelsscore tussen 0 en 1 voor elk document, waarbij 1 het meest positief is. De analyse-modellen zijn vooraf getrainde met behulp van een uitgebreide hoofdtekst van de tekst en natuurlijke taal technologieën van Microsoft. Voor [geselecteerde talen](./text-analytics/language-support.md) kan de API elke onbewerkte tekst die u opgeeft analyseren en beoordelen en de resultaten direct doorgeven aan de aanroepende toepassing. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Daarnaast worden sommige containers ondersteund in Cognitive Services [**alles-in-één-** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) bron sleutels. U kunt één Cognitive Services alles-in-één resource maken en dezelfde facturerings sleutel gebruiken voor de ondersteunde services voor de volgende services:

* Computer Vision
* Face
* LUIS
* Tekstanalyse

## <a name="container-availability-in-azure-cognitive-services"></a>Beschikbaarheid van de container in Azure Cognitive Services

Azure Cognitive Services-containers zijn openbaar beschikbaar zijn via uw Azure-abonnement en Docker-containerinstallatiekopieën kunnen worden opgehaald uit het Microsoft Container Registry of Docker Hub. U kunt de [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) opdracht voor het downloaden van de installatiekopie van een container van de juiste registersleutel.

> [!IMPORTANT]
> Op dit moment moet u een aanmeldings proces voor toegang tot de volgende containers volt ooien, waarin u een vragen lijst kunt invullen en verzenden met vragen over u, uw bedrijf en het gebruiks voorbeeld waarvoor u de containers wilt implementeren. Nadat u toegang hebt verleend en referenties hebt verstrekt, kunt u de container installatie kopieën ophalen uit een persoonlijk container register dat wordt gehost door Azure Container Registry.
> * [Anomaly Detector](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Face](Face/face-how-to-install-containers.md)
> * [Formulier herkenning](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Lezen](computer-vision/computer-vision-how-to-install-containers.md)
> * [Spraak naar tekst en tekst naar spraak](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Vereisten

U moet voldoen aan de volgende vereisten voordat u met behulp van Azure Cognitive Services-containers:

**Docker-Engine**: U moet Docker Engine lokaal zijn geïnstalleerd. Docker biedt pakketten die de Docker-omgeving configureren op [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), en [Windows](https://docs.docker.com/docker-for-windows/). Op Windows, moet Docker worden geconfigureerd ter ondersteuning van Linux-containers. Docker-containers kunnen ook rechtstreeks naar worden geïmplementeerd [Azure Kubernetes Service](../aks/index.yml) of [Azure Container Instances](../container-instances/index.yml).

Docker moet worden geconfigureerd, zodat de containers om te verbinden met en facturering gegevens verzenden naar Azure.

**Vertrouwd zijn met Microsoft Container Registry en Docker**: U moet een basiskennis hebben van zowel Microsoft Container Registry en Docker-concepten, zoals registers, -opslagplaatsen, containers, en containerinstallatiekopieën, evenals kennis van Basic `docker` opdrachten.

Zie voor een uitleg van de basisprincipes van Docker en containers, de [dockeroverzicht](https://docs.docker.com/engine/docker-overview/).

Afzonderlijke containers kunnen hun eigen vereisten, evenals, met inbegrip van de server en de toewijzing van geheugenvereisten hebben.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [container recepten](containers/container-reuse-recipe.md) die u kunt gebruiken met de Cognitive Services.

Installeren en de functionaliteit van containers in Azure Cognitive Services verkennen:

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