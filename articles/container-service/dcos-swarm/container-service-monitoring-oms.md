---
title: KEUR Azure DC/OS-cluster bewaken-Operations Management
description: Een Azure Container Service DC/OS-cluster bewaken met Log Analytics.
author: keikhara
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1ab8d1cf3eb38a17f0b3d6c8137e37237498a527
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277324"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>KEUR Een Azure Container Service DC/OS-cluster bewaken met Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics is de Cloud oplossing van micro soft die u helpt bij het beheren en beveiligen van uw on-premises en Cloud infrastructuur.Container oplossing is een oplossing in Log Analytics, waarmee u de container inventaris, de prestaties en logboeken op één locatie kunt bekijken. U kunt controleren, problemen met containers oplossen door de logboeken op gecentraliseerde locatie te bekijken en te zoeken naar een overmatige container op een host.

![](media/container-service-monitoring-oms/image1.png)

Zie de [container solution log Analytics](../../azure-monitor/insights/containers.md)voor meer informatie over container oplossing.

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Log Analytics instellen vanuit het DC/OS-universum


In dit artikel wordt ervan uitgegaan dat u een DC/OS hebt ingesteld en eenvoudige web container toepassingen hebt geïmplementeerd op het cluster.

### <a name="pre-requisite"></a>Vereiste
- [Microsoft Azure abonnement](https://azure.microsoft.com/free/) : u kunt gratis een abonnement ontvangen.  
- Log Analytics werk ruimte instellen: Zie ' stap 3 ' hieronder
- [DC/OS cli](https://docs.mesosphere.com/1.12/cli) geïnstalleerd.

1. Klik in het DC/OS-dash board op universum en zoek naar ' OMS ', zoals hieronder wordt weer gegeven.

   >[!NOTE]
   >OMS wordt nu Log Analytics genoemd.

   ![](media/container-service-monitoring-oms/image2.png)

2. Klik op **installeren**. Er wordt een pop-upvenster weer gegeven met de versie-informatie en een knop voor een installatie **pakket** of een **Geavanceerde installatie** . Wanneer u op **Geavanceerde installatie**klikt, wordt u naar de OMS-pagina met **specifieke configuratie-eigenschappen** geleid.

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Hier wordt u gevraagd om de `wsid` (de log Analytics werk ruimte-id) en `wskey` (de primaire sleutel voor de werk ruimte-id) in te voeren. Als u een `wsid` account `wskey` wilt maken, kunt u dit op <https://mms.microsoft.com>beide vragen.
   Volg de stappen voor het maken van een account. Wanneer u klaar bent met het maken van het account, moet u `wsid` uw `wskey` en door te klikken op **instellingen**, vervolgens **verbonden bronnen**en **Linux-servers**, zoals hieronder wordt weer gegeven.

   ![](media/container-service-monitoring-oms/image5.png)

4. Selecteer het gewenste aantal exemplaren en klik op de knop controleren en installeren. Normaal gesp roken wilt u het aantal exemplaren dat gelijk is aan het aantal VM'S dat u in uw agent cluster hebt. Log Analytics-agent voor Linux wordt als afzonderlijke containers geïnstalleerd op elke virtuele machine die informatie wil verzamelen voor informatie over bewaking en logboek registratie.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Een eenvoudig Log Analytics dash board instellen

Nadat u de Log Analytics-agent voor Linux op de Vm's hebt geïnstalleerd, is de volgende stap het instellen van het Log Analytics dash board. U kunt het dash board instellen via Azure Portal.

### <a name="azure-portal"></a>Azure Portal 

Meld u aan bij Azure Portal <https://portal.microsoft.com/>op. Ga naar **Marketplace**, selecteer **bewaking en beheer** en klik op **alles weer geven**. Typ `containers` vervolgens zoeken. U ziet ' containers ' in de zoek resultaten. Selecteer **containers** en klik op **maken**.

![](media/container-service-monitoring-oms/image9.png)

Nadat u op **maken**hebt geklikt, wordt u gevraagd om uw werk ruimte. Selecteer uw werk ruimte of maak een nieuwe werk ruimte als u er nog geen hebt.

![](media/container-service-monitoring-oms/image10.PNG)

Wanneer u uw werk ruimte hebt geselecteerd, klikt u op **maken**.

![](media/container-service-monitoring-oms/image11.png)

Voor meer informatie over de Log Analytics-container oplossing raadpleegt u de [container oplossing log Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Log Analytics-agent schalen met ACS DC/OS 

Als u Log Analytics agent van het werkelijke aantal knoop punten wilt installeren, of als u de schaalset voor virtuele machines wilt schalen door meer VM toe te voegen, kunt u dit doen door de `msoms` service te schalen.

U kunt door gaan naar marathon of het tabblad DC/OS UI Services en het aantal knoop punten omhoog schalen.

![](media/container-service-monitoring-oms/image12.PNG)

Hiermee wordt geïmplementeerd naar andere knoop punten die de Log Analytics-agent nog niet hebben geïmplementeerd.

## <a name="uninstall-ms-oms"></a>MS OMS verwijderen

Als u MS OMS wilt verwijderen, voert u de volgende opdracht in:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Laat het ons weten!!!
Wat werkt er? Wat ontbreekt er? Wat moet u nog meer doen om dit nuttig te maken? Laat het ons weten op <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Volgende stappen

 Nu u Log Analytics hebt ingesteld om uw containers te bewaken,[raadpleegt u het container dashboard](../../azure-monitor/insights/containers.md).
