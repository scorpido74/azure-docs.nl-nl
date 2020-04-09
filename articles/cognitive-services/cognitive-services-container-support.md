---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
description: Ontdek hoe Docker-containers Cognitive Services dichter bij uw gegevens kunnen brengen.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7a38ec47d416027e8ea3fa772ae01e4f6264197a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876816"
---
# <a name="container-support-in-azure-cognitive-services"></a>Containerondersteuning in Azure Cognitive Services

Containerondersteuning in Azure Cognitive Services stelt ontwikkelaars in staat om dezelfde uitgebreide API's te gebruiken die beschikbaar zijn in Azure en maakt flexibiliteit mogelijk in het implementeren en hosten van de services die worden geleverd met [Docker-containers.](https://www.docker.com/what-container) Containerondersteuning is momenteel beschikbaar voor een subset van Azure Cognitive Services, inclusief delen van:

> [!div class="checklist"]
> * [Anomaly Detector][ad-containers]
> * [Computer Vision][cv-containers]
> * [Face][fa-containers]
> * [Form Recognizer][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Speech Service-API][sp-containers]
> * [Tekstanalyse][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Containerization is een benadering van softwaredistributie waarbij een toepassing of service, inclusief de afhankelijkheden & configuratie, wordt verpakt als een containerafbeelding. Met weinig of geen wijziging kan een containerafbeelding worden geïmplementeerd op een containerhost. Containers worden geïsoleerd van elkaar en het onderliggende besturingssysteem, met een kleinere voetafdruk dan een virtuele machine. Containers kunnen worden geinstantieerd uit containerafbeeldingen voor taken op korte termijn en worden verwijderd wanneer deze niet meer nodig is.

Resources voor cognitive services zijn beschikbaar op [Microsoft Azure.](https://azure.microsoft.com) Meld u aan bij de [Azure-portal](https://portal.azure.com/) om Azure-bronnen voor deze services te maken en te verkennen.

## <a name="features-and-benefits"></a>Functies en voordelen

- **Onveranderlijke infrastructuur**: Schakel DevOps-teams in staat om een consistente en betrouwbare set bekende systeemparameters te gebruiken, terwijl ze zich kunnen aanpassen aan veranderingen. Containers bieden de flexibiliteit om te draaien binnen een voorspelbaar ecosysteem en configuratiedrift te voorkomen.
- **Controle over gegevens:** laat klanten kiezen waar deze Cognitive Services hun gegevens verwerken. Dit is essentieel voor klanten die geen gegevens naar de cloud kunnen verzenden, maar wel toegang nodig hebben tot Cognitive Services-technologie. Ondersteuning voor consistentie in hybride omgevingen – op het gebied van gegevens, beheer, identiteit en beveiliging.
- **Controle over modelupdates:** bied klanten flexibiliteit bij het versien en bijwerken van modellen die in hun oplossingen worden geïmplementeerd.
- **Draagbare architectuur:** schakel de creatie van een draagbare toepassingsarchitectuur in die kan worden geïmplementeerd op Azure, on-premises en de edge. Containers kunnen rechtstreeks worden geïmplementeerd in [Azure Kubernetes Service,](../aks/index.yml) [Azure Container Instances](../container-instances/index.yml)of naar een [Kubernetes-cluster](https://kubernetes.io/) dat is geïmplementeerd in [Azure Stack.](/azure-stack/operator) Zie [Kubernetes implementeren naar Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)voor meer informatie.
- **Hoge doorvoer / lage latentie**: Bied klanten de mogelijkheid om te schalen voor hoge doorvoer- en lage latentievereisten door Cognitive Services in staat te stellen fysiek dicht bij hun toepassingslogica en -gegevens uit te voeren. Containers beperken transacties per seconde (TPS) niet en kunnen worden opschalen om zowel omhoog als uit te schalen om de vraag te verwerken als u de benodigde hardwarebronnen levert.
- **Schaalbaarheid**: Met de steeds groeiende populariteit van containerisatie en container orchestration software, zoals Kubernetes; schaalbaarheid is in de voorhoede van de technologische vooruitgang. Voortbouwend op een schaalbare clusterstichting, is applicatieontwikkeling geschikt voor hoge beschikbaarheid.

## <a name="containers-in-azure-cognitive-services"></a>Containers in Azure Cognitive Services

Azure Cognitive Services-containers bieden de volgende set Docker-containers, die elk een subset van functionaliteit bevatten van services in Azure Cognitive Services:

| Service | Ondersteunde prijscategorie | Container | Beschrijving |
|---------|----------|----------|-------------|
|[Anomaliedetector][ad-containers] |F0, S0|**Anomalie-detector** |Met de Anomaly Detector API u afwijkingen in uw tijdreeksgegevens met machine learning monitoren en detecteren.<br>[Toegang aanvragen](https://aka.ms/adcontainer)|
|[Computer Vision][cv-containers] |F0, S1|**Lezen** |Hiermee haalt u afgedrukte tekst uit afbeeldingen van verschillende objecten met verschillende oppervlakken en achtergronden, zoals ontvangstbewijzen, posters en visitekaartjes. De leescontainer detecteert ook *handgeschreven tekst* in afbeeldingen en biedt ondersteuning voor PDF/TIFF/multi-page.<br/><br/>**Belangrijk:** De Lees-container werkt momenteel alleen met Engels.|
|[Face][fa-containers] |F0, S0|**Face** |Detecteert menselijke gezichten in afbeeldingen en identificeert kenmerken, waaronder gezichtsoriëntatiepunten (zoals neuzen en ogen), geslacht, leeftijd en andere door de machine voorspelde gelaatstrekken. Naast detectie kan Face controleren of twee gezichten in dezelfde afbeelding of verschillende afbeeldingen hetzelfde zijn door een betrouwbaarheidsscore te gebruiken, of gezichten vergelijken met een database om te zien of er al een vergelijkbaar of identiek gezicht bestaat. Het kan ook vergelijkbare gezichten in groepen ordenen, met behulp van gedeelde visuele eigenschappen.<br>[Toegang aanvragen](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Formulierherkenning][fr-containers] |F0, S0|**Form Recognizer** |Form Understanding past machine learning-technologie toe om sleutelwaardeparen en tabellen uit formulieren te identificeren en te extraheren.<br>[Toegang aanvragen](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**LUIS** ([afbeelding](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Laadt een getraind of gepubliceerd taalbegripsmodel, ook wel LUIS-app genoemd, in een dockercontainer en biedt toegang tot de queryvoorspellingen van de API-eindpunten van de container. U querylogboeken uit de container verzamelen en deze uploaden naar de [LUIS-portal](https://www.luis.ai) om de nauwkeurigheid van de voorspelling van de app te verbeteren.|
|[Speech Service-API][sp-containers-stt] |F0, S0|**Spraak naar tekst** |Transcribeert continue realtime spraak naar tekst.|
|[Speech Service-API][sp-containers-cstt] |F0, S0|**Aangepaste spraak-naar-tekst** |Transcribeert continue real-time spraak in tekst met behulp van een aangepast model.|
|[Speech Service-API][sp-containers-tts] |F0, S0|**Tekst naar spraak** |Converteert tekst naar natuurlijk klinkende spraak.|
|[Speech Service-API][sp-containers-ctts] |F0, S0|**Aangepaste tekst-naar-spraak** |Hiermee converteert u tekst naar natuurlijk klinkende spraak met behulp van een aangepast model.|
|[Tekstanalyse][ta-containers-keyphrase] |F0, S|**Trefwoordextractie** [(afbeelding)](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409) |Haalt belangrijke zinnen uit om de belangrijkste punten te identificeren. Bijvoorbeeld, voor de invoertekst 'het eten was heerlijk en de bediening fantastisch' retourneert de API de belangrijkste gespreksonderwerpen: 'eten' en 'bediening fantastisch'. |
|[Tekstanalyse][ta-containers-language]|F0, S|**Taaldetectie** ([afbeelding](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Detecteert voor maximaal 120 talen in welke taal de invoertekst is geschreven en rapporteert één taalcode voor elk document dat op de aanvraag is ingediend. De taalcode is gekoppeld aan een score die de sterkte van de score aangeeft. |
|[Tekstanalyse][ta-containers-sentiment]|F0, S|**Sentimentanalyse** ([afbeelding](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analyseert ruwe tekst voor aanwijzingen over positief of negatief sentiment. Deze API retourneert een gevoelsscore tussen 0 en 1 voor elk document, waarbij 1 het meest positief is. De analysemodellen zijn vooraf opgeleid met behulp van een uitgebreide hoeveelheid tekst- en natuurlijke taaltechnologieën van Microsoft. Voor [geselecteerde talen](./text-analytics/language-support.md) kan de API elke onbewerkte tekst die u opgeeft analyseren en beoordelen en de resultaten direct doorgeven aan de aanroepende toepassing. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Bovendien worden sommige containers ondersteund in Cognitive Services [**All-In-One met**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) resourcesleutels. U één in de één bron voor Cognitive Services all-in-one maken en dezelfde factureringssleutel gebruiken voor ondersteunde services voor de volgende services:

* Computer Vision
* Face
* LUIS
* Tekstanalyse

## <a name="container-availability-in-azure-cognitive-services"></a>Beschikbaarheid van containers in Azure Cognitive Services

Azure Cognitive Services-containers zijn openbaar beschikbaar via uw Azure-abonnement en Docker-containerafbeeldingen kunnen worden opgehaald uit het Microsoft Container Registry of Docker Hub. U de opdracht [pull-docker](https://docs.docker.com/engine/reference/commandline/pull/) gebruiken om een containerafbeelding uit het juiste register te downloaden.

> [!IMPORTANT]
> Momenteel moet u een aanmeldingsproces voltooien om toegang te krijgen tot de volgende containers, waarin u een vragenlijst invult en indient met vragen over u, uw bedrijf en de use case waarvoor u de containers wilt implementeren. Zodra u toegang en opgegeven referenties hebt gekregen, u de containerafbeeldingen ophalen uit een privécontainerregister dat wordt gehost door Azure Container Registry.
> * [Anomaliedetector](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Face](Face/face-how-to-install-containers.md)
> * [Form Recognizer](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Lezen](computer-vision/computer-vision-how-to-install-containers.md)
> * [Spraak-naar-tekst en tekst-naar-spraak](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Vereisten

U moet aan de volgende voorwaarden voldoen voordat u Azure Cognitive Services-containers gebruikt:

**Docker Engine**: U moet Docker Engine lokaal hebben geïnstalleerd. Docker biedt pakketten die de Docker-omgeving configureren op [macOS,](https://docs.docker.com/docker-for-mac/) [Linux](https://docs.docker.com/engine/installation/#supported-platforms)en [Windows.](https://docs.docker.com/docker-for-windows/) Op Windows moet Docker zijn geconfigureerd om Linux-containers te ondersteunen. Docker-containers kunnen ook rechtstreeks worden geïmplementeerd in [Azure Kubernetes Service](../aks/index.yml) of Azure Container [Instances.](../container-instances/index.yml)

Docker moet zijn geconfigureerd om de containers in staat te stellen verbinding te maken met en factureringsgegevens naar Azure te verzenden.

**Vertrouwdheid met Microsoft Container Registry en Docker:** U moet een basiskennis hebben van zowel Microsoft Container Registry- als Docker-concepten, `docker` zoals registers, opslagplaatsen, containers en containerafbeeldingen, evenals kennis van basisopdrachten.

Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.

Individuele containers kunnen ook hun eigen vereisten hebben, inclusief server- en geheugentoewijzingsvereisten.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [containerrecepten](containers/container-reuse-recipe.md) die u gebruiken met de Cognitive Services.

Installeer en verken de functionaliteit van containers in Azure Cognitive Services:

* [Anomalie Detector containers][ad-containers]
* [Computer Vision containers][cv-containers]
* [Gezichtscontainers][fa-containers]
* [Form Recognizer containers][fr-containers]
* [Taalbegrip (LUIS) containers][lu-containers]
* [Api-containers voor spraakservice][sp-containers]
* [Text Analytics-containers][ta-containers]

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