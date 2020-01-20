---
title: KEUR Azure DC/OS-cluster controleren-Dynatrace
description: Een Azure Container Service DC/OS-cluster bewaken met Dynatrace. Implementeer de Dynatrace OneAgent met behulp van het DC/OS-dash board.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a82481c5cb3d12b11179b41999f73e67583ec43b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277753"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>KEUR Een Azure Container Service DC/OS-cluster bewaken met Dynatrace SaaS/Managed

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In dit artikel laten we zien hoe u de [Dynatrace](https://www.dynatrace.com/) OneAgent implementeert om alle agent knooppunten in uw Azure container service-cluster te bewaken. U hebt een account met Dynatrace SaaS/Managed voor deze configuratie nodig. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/beheerd
Dynatrace is een Cloud systeem eigen bewakings oplossing voor Maxi maal dynamische container-en cluster omgevingen. Zo kunt u uw container implementaties en geheugen toewijzingen beter optimaliseren door gebruik te maken van real-time gebruiks gegevens. Het is geschikt voor het automatisch herkennen van problemen met toepassingen en infra structuur door automatische basis lijnen, probleem correlatie en detectie van de hoofd oorzaak te bieden.

In de volgende afbeelding ziet u de Dynatrace-gebruikers interface:

![Dynatrace-gebruikers interface](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Vereisten 
[Implementeer](container-service-deployment.md) en [Maak verbinding](./../container-service-connect.md) met een cluster dat is geconfigureerd door Azure container service. Verken de [Marathon-gebruikersinterface](container-service-mesos-marathon-ui.md). Ga naar [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) om een Dynatrace SaaS-account in te stellen.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Een Dynatrace-implementatie configureren met marathon
Deze stappen laten zien hoe u Dynatrace-toepassingen in uw cluster kunt configureren en implementeren met marathon.

1. Toegang tot uw DC/OS-gebruikers interface via [http://localhost:80/](http://localhost:80/). Ga in de DC/OS-gebruikers interface naar het tabblad **universum** en zoek naar **Dynatrace**.

    ![Dynatrace in DC/OS-universum](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. U hebt een Dynatrace SaaS-account of een gratis proef account nodig om de configuratie te volt ooien. Nadat u zich hebt aangemeld bij het Dynatrace-dash board, selecteert u **Dynatrace implementeren**.

    ![Dynatrace-PaaS-integratie instellen](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Selecteer op de pagina **PaaS-integratie instellen**. 

    ![Dynatrace-API-token](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Voer uw API-token in voor de Dynatrace OneAgent-configuratie binnen het DC/OS-universum. 

    ![Dynatrace OneAgent-configuratie in het DC/OS-universum](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Stel de exemplaren in op het aantal knoop punten dat u wilt uitvoeren. Het instellen van een hoger nummer werkt ook, maar DC/OS zal blijven proberen om nieuwe instanties te vinden totdat het aantal daad werkelijk wordt bereikt. Als u wilt, kunt u dit ook instellen op een waarde als 1000000. Wanneer een nieuw knoop punt aan het cluster wordt toegevoegd, implementeert Dynatrace automatisch een agent naar dat nieuwe knoop punt, tegen de prijs van DC/OS die voortdurend probeert om verdere instanties te implementeren.

    ![Dynatrace-configuratie in het DC/OS-universum-instanties](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u het pakket hebt geïnstalleerd, gaat u terug naar het Dynatrace-dash board. U kunt de verschillende metrische gegevens over het gebruik van de containers in uw cluster verkennen. 
