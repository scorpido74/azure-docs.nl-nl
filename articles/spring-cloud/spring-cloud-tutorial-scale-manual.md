---
title: 'Zelf studie: een toepassing schalen in azure lente Cloud | Microsoft Docs'
description: In deze zelf studie leert u hoe u een toepassing kunt schalen in azure lente-Cloud op het Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/06/2019
ms.openlocfilehash: 31b2322bdf7b7c03ae8974d57ee1b44c2f6137b9
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607517"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Zelf studie: een toepassing schalen in een Azure lente-Cloud

In deze zelf studie wordt gedemonstreerd hoe u een micro service-toepassing kunt schalen met behulp van het Azure veer Cloud-dash board in de Azure Portal. Schaal uw toepassing omhoog en omlaag door het aantal virtuele Cpu's (Vcpu's) en de hoeveelheid geheugen aan te passen. Schaal uw toepassing in-en uitschalen door het aantal exemplaren van de toepassing te wijzigen. Wanneer u klaar bent, weet u hoe u snel hand matige aanpassingen kunt maken voor elke toepassing in uw service. Schalen neemt in een paar seconden toe en vereist geen code wijzigingen of herimplementatie.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 
* Een geïmplementeerd Azure lente-Cloud service-exemplaar.  Volg onze [Snelstartgids](spring-cloud-quickstart-launch-app-cli.md) om aan de slag te gaan.
* Ten minste één toepassing is al in dat service-exemplaar gemaakt.


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Ga naar de pagina schalen in het Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Ga naar de **overzichts** pagina van Azure lente Cloud.

1. Ga naar het tabblad **apps** onder de kop **instellingen** in het menu aan de linkerkant.

1. Selecteer de toepassing die u wilt schalen. In dit voor beeld wordt de toepassing met de naam ' account-service ' geschaald. Hiervoor gaat u naar de **overzichts** pagina van de toepassing.

1. Ga naar het tabblad **schalen** onder de kop **instellingen** in het menu aan de linkerkant. Er wordt een formulier weer geven met regels voor elk van de eerder genoemde schaal kenmerken.

## <a name="scale-your-application"></a>Schaal aanpassen van uw toepassing

U kunt de schaal kenmerken wijzigen. Houd de volgende opmerkingen in het oog.

* **Cpu's**: het maximum aantal cpu's dat is toegestaan, is 4 per toepassings exemplaar. Het totale aantal Cpu's voor een toepassing is de waarde die hier is ingesteld, vermenigvuldigd met het aantal toepassings exemplaren.

* **Geheugen/GB**: de Maxi maal toegestane hoeveelheid geheugen is 8 GB per toepassings exemplaar.  De totale hoeveelheid geheugen voor een toepassing is de waarde die hier is ingesteld, vermenigvuldigd met het aantal toepassings exemplaren.

* **Aantal instanties**: u kunt Maxi maal 20 exemplaren uitschalen in de laag standaard. Met deze waarde wordt het aantal afzonderlijke actieve exemplaren van de micro service-toepassing gewijzigd.

Zorg ervoor dat u op de knop **Opslaan** klikt om uw schaal instellingen toe te passen.

Na enkele seconden worden de wijzigingen die u hebt aangebracht, weer gegeven op de **overzichts** pagina, met meer details die beschikbaar zijn op het tabblad **toepassings exemplaren** . voor het schalen is geen code wijzigingen of herimplementatie vereist.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u uw Azure lente-Cloud toepassingen hand matig kunt schalen.  Ga door naar de volgende zelf studie voor meer informatie over het bewaken van uw toepassing.

> [!div class="nextstepaction"]
> [Meer informatie over het bewaken van uw toepassing](spring-cloud-tutorial-distributed-tracing.md)
