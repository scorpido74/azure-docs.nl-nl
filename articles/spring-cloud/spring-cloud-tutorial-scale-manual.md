---
title: 'Zelfstudie: Een toepassing schalen in Azure Spring Cloud | Microsoft Docs'
description: Leer hoe u een toepassing kunt schalen met Azure Spring Cloud in de Azure-portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: ca889e48510ec20b4823c6ec5e4e0149d5aaaa62
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86141873"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Zelfstudie: Een toepassing schalen in Azure Spring Cloud

In deze zelfstudie wordt gedemonstreerd hoe u een microservicetoepassing kunt schalen met behulp van het Azure Spring Cloud-dashboard in de Azure-portal.

Schaal uw toepassing omhoog en omlaag door het aantal virtuele CPU's (vCPU's) en de hoeveelheid geheugen aan te passen. Schaal uw toepassing in en uit door het aantal toepassingsexemplaren te wijzigen.

Nadat u klaar bent, weet u hoe u snel handmatige wijzigingen kunt aanbrengen in elke toepassing in uw service. Schalen gaat binnen enkele seconden van kracht en vereist geen codewijzigingen of herimplementatie.

## <a name="prerequisites"></a>Vereisten

Als u deze procedures wilt volgen, hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 
* Een geïmplementeerd Azure Spring Cloud-service-exemplaar.  Volg de [quickstart voor het implementeren van een app via Azure CLI](spring-cloud-quickstart-launch-app-cli.md) om aan de slag te gaan.
* Er is al minstens één toepassing gemaakt in uw service-exemplaar.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Ga naar de pagina Schalen in de Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Ga naar de **Overzichtspagina** van Azure Spring Cloud.

1. Selecteer dezelfde resourcegroep die uw service bevat.

1. Selecteer het tabblad **Apps** onder **Instellingen** in het menu aan de linkerkant van de pagina.

1. Selecteer de toepassing die u wilt schalen. Selecteer in dit voorbeeld de toepassing met de naam **account-service**. Vervolgens ziet u de **Overzichtspagina** van de toepassing.

1. Ga naar het tabblad **Schalen** onder **Instellingen** in het menu aan de linkerkant van de pagina. U ziet nu opties voor het schalen van de kenmerken die worden weergegeven in de volgende sectie.

## <a name="scale-your-application"></a>Uw toepassing schalen

Als u de schaalkenmerken wijzigt, moet u rekening houden met de volgende opmerkingen:

* **CPU’s**: Het maximumaantal CPU's per toepassingsexemplaar is vier. Het totale aantal CPU's voor een toepassing is de waarde die hier is ingesteld, vermenigvuldigd met het aantal toepassingsexemplaren.

* **Geheugen/GB**: De maximale hoeveelheid geheugen per toepassingsexemplaar is 8 GB. De totale hoeveelheid geheugen voor een toepassing is de waarde die hier is ingesteld, vermenigvuldigd met het aantal toepassingsexemplaren.

* **Aantal app-exemplaren**: In de Standard-laag kunt u uitschalen naar maximaal 20 exemplaren. Met deze waarde wordt het aantal afzonderlijke actieve exemplaren van de microservicetoepassing gewijzigd.

Zorg ervoor dat u **Opslaan** selecteert om de schaalinstellingen toe te passen.

![De service Schalen in de Azure-portal](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Na enkele seconden worden de wijzigingen die u hebt aangebracht, weergegeven op **Overzichtspagina**. Meer details zijn beschikbaar op het tabblad **Toepassingsexemplaren**. Voor schalen zijn geen codewijzigingen of herimplementatie vereist.

## <a name="upgrade-to-the-standard-tier"></a>Upgraden naar de prijscategorie Standard
Als u zich in de Basic-laag bevindt en bent beperkt door een of meer van deze [Limieten](spring-cloud-quotas.md), kunt u een upgrade uitvoeren naar de Standard-laag. Als u dit wilt doen, gaat u naar het menu Prijscategorie door eerst de kolom Standard-laag te selecteren en vervolgens op de knop **Upgraden** te klikken.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Azure Spring Cloud-toepassingen handmatig kunt schalen. Ga door naar de volgende zelfstudie voor informatie over het bewaken van een toepassing door meldingen in te stellen.

> [!div class="nextstepaction"]
> [Meer informatie over het instellen van meldingen](spring-cloud-tutorial-alerts-action-groups.md)
