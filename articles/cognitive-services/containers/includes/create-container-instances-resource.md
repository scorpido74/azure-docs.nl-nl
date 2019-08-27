---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een Azure container instance-resource.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/21/2019
ms.author: dapine
ms.openlocfilehash: 07e2067571c7bc7403ee8a1d1a0600c451e1581f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051168"
---
## <a name="create-an-azure-container-instance-resource"></a>Een Azure container instance-resource maken

1. Ga naar de pagina [maken](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) voor container instances.

2. Voer op het tabblad **basis beginselen** de volgende gegevens in:

    |Instelling|Value|
    |--|--|
    |Subscription|Selecteer uw abonnement.|
    |Resource group|Selecteer de beschik bare resource groep of maak een nieuwe, `cognitive-services`zoals.|
    |Containernaam|Voer een naam in `cognitive-container-instance`zoals. De naam moet in kleine letters worden gereduceerd.|
    |Location|Selecteer een regio voor de implementatie.|
    |Type installatiekopie|Als uw container installatie kopie is opgeslagen in een container register waarvoor geen referenties zijn vereist `Public`, kiest u. Als u toegang hebt tot de container installatie kopie `Private`, moet u de optie referenties kiezen. Raadpleeg [container opslagplaatsen en installatie kopieën](../../cognitive-services-container-support.md#container-repositories-and-images) voor meer informatie over of de container installatie kopie al `Public` dan `Private` niet is (' open bare preview '). |
    |Naam van installatiekopie|Voer de locatie van de Cognitive Services-container in. De locatie is wat wordt gebruikt als argument voor de `docker pull` opdracht. Raadpleeg de [container opslagplaatsen en installatie kopieën](../../cognitive-services-container-support.md#container-repositories-and-images) voor de beschik bare afbeeldings namen en de bijbehorende opslag plaats.<br><br>De naam van de installatie kopie moet volledig gekwalificeerd zijn om drie delen op te geven. Ten eerste, het container register en vervolgens de naam van de installatie kopie: `<container-registry>/<repository>/<image-name>`.<br><br>Hier ziet u een voor `mcr.microsoft.com/azure-cognitive-services/keyphrase` beeld van de Sleuteltermextractie installatie kopie in de micro soft-container Registry onder de Azure Cognitive Services-opslag plaats. Een ander voor beeld `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` is dat de spraak-naar-tekst afbeelding wordt weer gegeven in de micro soft-opslag plaats van het container voorbeeld container register. |
    |Type besturingssysteem|`Linux`|
    |Size|Wijzig grootte in de voorgestelde aanbevelingen voor uw specifieke cognitieve service-container:<br>twee CPU-kernen<br>4 GB

3. Voer op het tabblad **netwerken** de volgende gegevens in:

    |Instelling|Value|
    |--|--|
    |Poorten|Stel de TCP-poort `5000`in op. Beschrijft de container op poort 5000.|

4. Voer op het tabblad **Geavanceerd** de vereiste **omgevings variabelen** in voor de instellingen van de container facturering van de Azure container Instance-bron:

    | Sleutel | Value |
    |--|--|
    |`apikey`|Wordt gekopieerd van de pagina **sleutels** van de resource. Het is een teken reeks van 32 alfanumerieke tekens zonder spaties of streepjes `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Wordt gekopieerd van de pagina **overzicht** van de resource.|
    |`eula`|`accept`|

1. Klik op **controleren en maken**
1. Nadat de validatie is geslaagd, klikt u op **maken** om het aanmaak proces te volt ooien
1. Wanneer de resource is geïmplementeerd, is deze gereed