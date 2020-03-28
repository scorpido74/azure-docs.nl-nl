---
title: 'Zelfstudie: Een toepassing schalen in Azure Spring Cloud | Microsoft Documenten'
description: In deze zelfstudie leert u hoe u een toepassing schalen met Azure Spring Cloud in de Azure-portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: f08a3b5d0a03b0e898457bbb783dd5031c4b0f27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277482"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Een toepassing schalen in Azure Spring Cloud

In deze zelfstudie wordt uitgelegd hoe u elke microservicetoepassing schaalt met het Azure Spring Cloud-dashboard in de Azure-portal.

Schaal uw toepassing op en neer door het aantal virtuele CPU's (vCPU's) en de hoeveelheid geheugen te wijzigen. Schaal uw toepassing in en uit door het aantal toepassingsinstanties te wijzigen.

Nadat u klaar bent, weet u hoe u snel handmatige wijzigingen aanbrengen in elke toepassing in uw service. Schalen wordt binnen enkele seconden van kracht en vereist geen codewijzigingen of herplaatsing.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint. 
* Een geïmplementeerdazure springcloudserviceexemplaar.  Volg onze [quickstart bij het implementeren van een app via de Azure CLI](spring-cloud-quickstart-launch-app-cli.md) om aan de slag te gaan.
* Ten minste één toepassing die al is gemaakt in uw service-instantie.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Navigeren naar de pagina Schalen in de Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Ga naar de pagina Azure Spring Cloud **Overview.**

1. Selecteer de resourcegroep die uw service bevat.

1. Selecteer het tabblad **Apps** onder **Instellingen** in het menu aan de linkerkant van de pagina.

1. Selecteer de toepassing die u wilt schalen. Selecteer in dit voorbeeld de toepassing met de naam **accountservice**. U ziet dan de **overzichtspagina** van de toepassing.

1. Ga naar het tabblad **Schalen** onder **Instellingen** in het menu aan de linkerkant van de pagina. U ziet opties voor het schalen van de kenmerken in de volgende sectie.

## <a name="scale-your-application"></a>Uw toepassing schalen

Als u de schaalkenmerken wijzigt, houdt u rekening met de volgende notities:

* **CPU's**: Het maximum aantal CPU's per toepassingsinstantie is vier. Het totale aantal CPU's voor een toepassing is de waarde die hier is ingesteld vermenigvuldigd met het aantal toepassingsinstanties.

* **Geheugen/GB**: De maximale hoeveelheid geheugen per toepassingsinstantie is 8 GB. De totale hoeveelheid geheugen voor een toepassing is de waarde die hier is ingesteld vermenigvuldigd met het aantal toepassingsinstanties.

* **Aantal app-exemplaren:** in de standaardlaag u uitschalen naar maximaal 20 exemplaren. Deze waarde wijzigt het aantal afzonderlijke lopende exemplaren van de microservicetoepassing.

Zorg ervoor dat u **Opslaan** selecteert om uw schaalinstellingen toe te passen.

![De scale-service in de Azure-portal](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Na een paar seconden worden de wijzigingen die u hebt aangebracht weergegeven op de pagina **Overzicht,** met meer details beschikbaar op het tabblad **Toepassingsinstanties.**

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u uw Azure Spring Cloud-toepassingen handmatig schalen. Ga verder naar de volgende zelfstudie om te leren hoe u uw toepassing controleren.

> [!div class="nextstepaction"]
> [Meer informatie over het controleren van een toepassing](spring-cloud-tutorial-distributed-tracing.md)
