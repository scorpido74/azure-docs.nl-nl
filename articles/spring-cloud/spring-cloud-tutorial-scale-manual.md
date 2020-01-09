---
title: 'Zelf studie: een toepassing schalen in azure lente Cloud | Microsoft Docs'
description: In deze zelf studie leert u hoe u een toepassing kunt schalen met Azure veer Cloud in de Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/06/2019
ms.openlocfilehash: eaf7e7ec39555e5b933020835f3bb96429e3aa81
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461428"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Een toepassing schalen in een Azure lente-Cloud

In deze zelf studie wordt gedemonstreerd hoe u een micro service-toepassing kunt schalen met behulp van het Azure veer Cloud-dash board in de Azure Portal.

Schaal uw toepassing omhoog en omlaag door het aantal virtuele Cpu's (Vcpu's) en de hoeveelheid geheugen aan te passen. Schaal uw toepassing in-en uitschalen door het aantal toepassings exemplaren te wijzigen.

Nadat u klaar bent, weet u hoe u snel hand matige wijzigingen kunt aanbrengen in elke toepassing in uw service. Schalen gaat in binnen enkele seconden en vereist geen code wijzigingen of herimplementatie.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 
* Een geïmplementeerd Azure lente-Cloud service-exemplaar.  Volg onze [Snelstartgids voor het implementeren van een app via de Azure cli](spring-cloud-quickstart-launch-app-cli.md) om aan de slag te gaan.
* Ten minste één toepassing is al in uw service-exemplaar gemaakt.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Ga naar de pagina schalen in het Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Ga naar de **overzichts** pagina van Azure lente Cloud.

1. Selecteer de resource groep die uw service bevat.

1. Selecteer het tabblad **apps** onder **instellingen** in het menu aan de linkerkant van de pagina.

1. Selecteer de toepassing die u wilt schalen. Selecteer in dit voor beeld de toepassing met de naam **account-service**. Vervolgens ziet u de **overzichts** pagina van de toepassing.

1. Ga naar het tabblad **schalen** onder **instellingen** in het menu aan de linkerkant van de pagina. U ziet nu opties voor het schalen van de kenmerken die in de volgende sectie worden weer gegeven.

## <a name="scale-your-application"></a>Schaal aanpassen van uw toepassing

Als u de schaal kenmerken wijzigt, houdt u de volgende opmerkingen in acht:

* **Cpu's**: het maximum aantal cpu's per toepassings exemplaar is vier. Het totale aantal Cpu's voor een toepassing is de waarde die hier is ingesteld, vermenigvuldigd met het aantal toepassings exemplaren.

* **Geheugen/GB**: de maximale hoeveelheid geheugen per toepassings exemplaar is 8 GB. De totale hoeveelheid geheugen voor een toepassing is de waarde die hier is ingesteld, vermenigvuldigd met het aantal toepassings exemplaren.

* **Aantal app-exemplaren**: in de laag standaard kunt u tot Maxi maal 20 exemplaren uitschalen. Met deze waarde wordt het aantal afzonderlijke actieve exemplaren van de micro service-toepassing gewijzigd.

Zorg ervoor dat u **Opslaan** selecteert om uw schaal instellingen toe te passen.

![De schaal service in de Azure Portal](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Na enkele seconden worden de wijzigingen die u hebt aangebracht, weer gegeven op de pagina **overzicht** , met meer details die beschikbaar zijn op het tabblad **toepassings exemplaren** . voor schalen is geen code wijzigingen of herimplementatie vereist.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u uw Azure lente-Cloud toepassingen hand matig kunt schalen. Ga door naar de volgende zelf studie voor meer informatie over het bewaken van uw toepassing.

> [!div class="nextstepaction"]
> [Meer informatie over het bewaken van een toepassing](spring-cloud-tutorial-distributed-tracing.md)
