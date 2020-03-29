---
title: (AFGESCHAFT) Azure DC/OS-cluster bewaken - Dynatrace
description: Monitor een Azure Container Service DC/OS-cluster met Dynatrace. Implementeer de Dynatrace OneAgent met behulp van het DC/OS-dashboard.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a82481c5cb3d12b11179b41999f73e67583ec43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277753"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(AFGESCHAFT) Een Azure Container Service DC/OS-cluster bewaken met Dynatrace SaaS/Managed

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In dit artikel laten we u zien hoe u de [Dynatrace](https://www.dynatrace.com/) OneAgent implementeert om alle agentknooppunten in uw Azure Container Service-cluster te controleren. Je hebt een account nodig bij Dynatrace SaaS/Managed voor deze configuratie. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/Managed
Dynatrace is een cloud-native monitoring oplossing voor zeer dynamische container- en clusteromgevingen. Hiermee u uw containerimplementaties en geheugentoewijzingen beter optimaliseren met behulp van realtime gebruiksgegevens. Het is in staat om automatisch te lokaliseren toepassing en infrastructuur problemen door het verstrekken van geautomatiseerde baselining, probleem correlatie, en root-cause detectie.

De volgende afbeelding toont de Dynatrace-gebruikersinterface:

![Dynatrace-gebruikersinterface](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Vereisten 
[Implementeren](container-service-deployment.md) en [verbinding maken met](./../container-service-connect.md) een cluster dat is geconfigureerd door Azure Container Service. Ontdek de [Marathon UI.](container-service-mesos-marathon-ui.md) Ga [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) naar het opzetten van een Dynatrace SaaS-account.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Een Dynatrace-implementatie configureren met Marathon
Deze stappen laten u zien hoe u Dynatrace-toepassingen configureert en implementeert in uw cluster met Marathon.

1. Krijg toegang tot uw [http://localhost:80/](http://localhost:80/)DC/OS-gebruikersinterface via . Eenmaal in de DC/OS-gebruikersinterface navigeert u naar het tabblad **Universe** en zoekt u vervolgens naar **Dynatrace.**

    ![Dynatrace in DC/OS Universe](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Om de configuratie te voltooien, heb je een Dynatrace SaaS-account of een gratis proefaccount nodig. Zodra u zich aanmeldt bij het Dynatrace-dashboard, selecteert u **Dynatrace implementeren**.

    ![Dynatrace PaaS-integratie instellen](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Selecteer op de pagina **PaaS-integratie instellen**. 

    ![Dynatrace API-token](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Voer uw API-token in in de Dynatrace OneAgent-configuratie binnen het DC/OS-universum. 

    ![Dynatrace OneAgent-configuratie in het DC/OS-universum](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Stel de instanties in op het aantal knooppunten dat u wilt uitvoeren. Het instellen van een hoger getal werkt ook, maar DC/OS blijft proberen nieuwe exemplaren te vinden totdat dat aantal daadwerkelijk is bereikt. Als u dat liever hebt, u dit ook instellen op een waarde als 1000000. In dit geval, wanneer een nieuw knooppunt wordt toegevoegd aan het cluster, implementeert Dynatrace automatisch een agent naar dat nieuwe knooppunt, tegen de prijs van DC/OS die voortdurend probeert om verdere exemplaren te implementeren.

    ![Dynatrace-configuratie in de DC/OS Universe-exemplaren](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Volgende stappen

Zodra u het pakket hebt geïnstalleerd, navigeert u terug naar het Dynatrace-dashboard. U de verschillende gebruiksstatistieken voor de containers binnen uw cluster bekijken. 
