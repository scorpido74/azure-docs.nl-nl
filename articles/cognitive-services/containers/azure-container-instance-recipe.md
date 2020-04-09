---
title: Recept azure containerinstance
titleSuffix: Azure Cognitive Services
description: Meer informatie over het implementeren van Cognitive Services Containers op Azure Container Instance
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876654"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Container implementeren en uitvoeren in Azure Container Instance

Schaal Azure Cognitive Services-toepassingen in de cloud eenvoudig met Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/)met de volgende stappen. Containerization helpt u zich te concentreren op het bouwen van uw applicaties in plaats van het beheren van de infrastructuur. Zie [functies en voordelen](../cognitive-services-container-support.md#features-and-benefits)voor meer informatie over het gebruik van containers.

## <a name="prerequisites"></a>Vereisten

Het recept werkt met elke Cognitive Services container. De cognitive service-bron moet worden gemaakt in de Azure-portal voordat u het recept gebruikt. Elke cognitieve service die containers ondersteunt, heeft een document 'Hoe te installeren' speciaal voor het installeren en configureren van de service voor een container. Voor sommige services is een bestand of een set bestanden vereist als invoer voor de container, het is belangrijk dat u de container met succes begrijpt en hebt gebruikt voordat u deze oplossing gebruikt.

* Een cognitive service-bron die is gemaakt in azure-portal.
* URL **van eindpunt** voor cognitieve service - bekijk de 'Hoe te installeren' van uw specifieke service voor de container, om te zien waar de URL van het eindpunt zich bevindt binnen de Azure-portal en hoe een correct voorbeeld van de URL eruit ziet. De exacte indeling kan van service naar service veranderen.
* Cognitieve **servicesleutel** - de sleutels staan op de pagina **Sleutels** voor de Azure-bron. Je hebt maar één van de twee sleutels nodig. De toets is een tekenreeks van 32 alfanumerieke tekens.
* Eén Cognitive Services-container op uw lokale host (uw computer). Zorg ervoor dat je kunt:
  * Trek de afbeelding `docker pull` omlaag met een opdracht.
  * Voer de lokale container met succes `docker run` uit met alle vereiste configuratie-instellingen met een opdracht.
  * Bel het eindpunt van de container en krijg een reactie van HTTP 2xx en een JSON-antwoord terug.

Alle variabelen in `<>`hoekbeugels, moeten worden vervangen door uw eigen waarden. Deze vervanging omvat de hoekbeugels.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>De containerinstantie gebruiken

1. Selecteer het **overzicht** en kopieer het IP-adres. Het zal een numeriek `55.55.55.55`IP-adres zijn, zoals .
1. Open een nieuw browsertabblad en gebruik bijvoorbeeld `http://<IP-address>:5000 (http://55.55.55.55:5000`het IP-adres). U ziet de startpagina van de container, zodat u weet dat de container wordt uitgevoerd.

1. Selecteer **Service-API-beschrijving** om de braniepagina voor de container weer te geven.

1. Selecteer een **POST** van de POST-API's en selecteer **Probeer het uit**.  De parameters worden weergegeven, inclusief de invoer. Vul de parameters in.

1. Selecteer **Uitvoeren** om de aanvraag naar uw containerexemplaar te verzenden.

    U hebt containers voor Cognitive Services gemaakt en gebruikt in Azure Container Instance.
