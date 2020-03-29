---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een Azure-containerinstantiebron.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 18f4edf5cc63a448779423cc1b302130b4b80724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75692518"
---
## <a name="create-an-azure-container-instance-resource"></a>Een Azure Container Instance-bron maken

1. Ga naar de pagina [Maken](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) voor containerinstanties.

2. Voer op het tabblad **Basis** de volgende details in:

    |Instelling|Waarde|
    |--|--|
    |Abonnement|Selecteer uw abonnement.|
    |Resourcegroep|Selecteer de beschikbare resourcegroep of maak `cognitive-services`een nieuwe groep, zoals .|
    |Containernaam|Voer een naam `cognitive-container-instance`in zoals . De naam moet in lagere caps zijn.|
    |Locatie|Selecteer een regio voor implementatie.|
    |Type installatiekopie|Als uw containerafbeelding is opgeslagen in een containerregister waarvoor `Public`geen referenties nodig zijn, kiest u . Als voor toegang tot de containerafbeelding referenties nodig zijn, kiest u `Private`. Raadpleeg [containeropslagplaatsen en afbeeldingen](../../cognitive-services-container-support.md#container-repositories-and-images) voor meer informatie over `Public` `Private` het al dan niet is of niet de containerafbeelding ('Public Preview'). |
    |Naam van installatiekopie|Voer de containerlocatie Cognitive Services in. De locatie is wat wordt gebruikt `docker pull` als argument voor de opdracht. Raadpleeg de [containeropslagplaatsen en afbeeldingen](../../cognitive-services-container-support.md#container-repositories-and-images) voor de beschikbare afbeeldingsnamen en de bijbehorende opslagplaats.<br><br>De afbeeldingsnaam moet volledig gekwalificeerd zijn met het opgeven van drie delen. Eerst het containerregister, vervolgens de opslagplaats, ten `<container-registry>/<repository>/<image-name>`slotte de naam van de afbeelding: .<br><br>Hier is een `mcr.microsoft.com/azure-cognitive-services/keyphrase` voorbeeld, zou de key phrase extraction afbeelding in het Microsoft Container Registry onder de Azure Cognitive Services repository vertegenwoordigen. Een ander `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` voorbeeld is, dat de afbeelding Van Spraak naar Tekst in de Microsoft-opslagplaats van het containerregister Container Preview zou vertegenwoordigen. |
    |Besturingssysteemtype|`Linux`|
    |Grootte|Wijzig de grootte in de voorgestelde aanbevelingen voor uw specifieke cognitive service-container:<br>2 CPU-cores<br>4 GB

3. Voer op het tabblad **Netwerken** de volgende details in:

    |Instelling|Waarde|
    |--|--|
    |Poorten|Stel de TCP-poort in op `5000`. Onthult de container op poort 5000.|

4. Voer op het tabblad **Geavanceerd** de vereiste omgevingsvariabelen in voor de **factureringsinstellingen** voor containers van de Azure Container Instance-bron:

    | Sleutel | Waarde |
    |--|--|
    |`apikey`|Gekopieerd van de **pagina Sleutels** van de bron. Het is een tekenreeks met 32 alfanumerieke tekens `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`zonder spaties of streepjes.|
    |`billing`|Gekopieerd van de **overzichtspagina** van de bron.|
    |`eula`|`accept`|

5. Klik **op Controleren en maken**
6. Nadat validatie is doorgegeven, klikt u op **Maken** om het creatieproces te voltooien
7. Wanneer de resource is geïmplementeerd, is deze klaar
