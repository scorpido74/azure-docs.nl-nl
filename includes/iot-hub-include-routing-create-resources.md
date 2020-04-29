---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 5680bd1c3fadab8edcde1634c708ca8683c0e99c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76021196"
---
## <a name="create-base-resources"></a>Basis bronnen maken

Voordat u de bericht routering kunt configureren, moet u een IoT-hub, een opslag account en een Service Bus wachtrij maken. Deze resources kunnen worden gemaakt met behulp van een van de vier artikelen die beschikbaar zijn voor deel 1 van deze zelf studie: de Azure Portal, een Azure Resource Manager sjabloon, de Azure CLI of Azure PowerShell.

Gebruik dezelfde resourcegroep en -locatie voor alle resources. Aan het einde kunt u alle resources in één stap verwijderen door de resource groep te verwijderen.

Hieronder vindt u een overzicht van de stappen die moeten worden uitgevoerd in de volgende secties: 

1. Maak een [resource groep](../articles/azure-resource-manager/management/overview.md).

2. Maak een IoT Hub in de S1-laag. Voeg een consumentengroep toe aan uw IoT Hub. De consumentengroep wordt gebruikt door de Azure Stream Analytics bij het ophalen van gegevens.

   > [!NOTE]
   > U moet een IoT Hub in een betaalde laag gebruiken om deze zelfstudie te voltooien. Met de gratis laag kunt u slechts één eindpunt instellen, terwijl voor deze zelfstudie meerdere eindpunten zijn vereist.
   > 

3. Maak een standaard V1-opslagaccount met Standard_LRS replicatie.

4. Maak een Service Bus-naamruimte en -wachtrij.

5. Maak een apparaat-id voor het gesimuleerde apparaat dat berichten naar uw hub verzendt. Sla de sleutel op voor de testfase. (Als u een resource manager-sjabloon maakt, wordt dit gedaan na het implementeren van de sjabloon.)