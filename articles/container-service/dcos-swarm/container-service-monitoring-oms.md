---
title: (AFGESCHAFT) Azure DC/OS-cluster bewaken - Operations Management
description: Monitor een Azure Container Service DC/OS-cluster met Log Analytics.
author: keikhara
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1ab8d1cf3eb38a17f0b3d6c8137e37237498a527
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277324"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(AFGESCHAFT) Een AZURE Container Service DC/OS-cluster bewaken met Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics is de cloudgebaseerde IT-beheeroplossing van Microsoft die u helpt bij het beheren en beschermen van uw on-premises en cloudinfrastructuur.Container Solution is een oplossing in Log Analytics, waarmee u de containervoorraad, prestaties en logboeken op één locatie bekijken. U controleren, containers oplossen door de logboeken op een centrale locatie te bekijken en luidruchtige overtollige container op een host vinden.

![](media/container-service-monitoring-oms/image1.png)

Zie de [Container Solution Log Analytics](../../azure-monitor/insights/containers.md)voor meer informatie over Container Solution.

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Log Analytics instellen vanuit het DC/OS-universum


In dit artikel wordt ervan uitgegaan dat u een DC/OS hebt ingesteld en eenvoudige webcontainertoepassingen op het cluster hebt geïmplementeerd.

### <a name="pre-requisite"></a>Vereiste
- [Microsoft Azure-abonnement](https://azure.microsoft.com/free/) - U een abonnement gratis krijgen.  
- Logboekanalyse-werkruimtesetup - zie 'Stap 3' hieronder
- [DC/OS CLI](https://docs.mesosphere.com/1.12/cli) geïnstalleerd.

1. Klik in het DC/OS dashboard op Universe en zoek naar 'OMS' zoals hieronder te zien is.

   >[!NOTE]
   >OMS wordt nu Log Analytics genoemd.

   ![](media/container-service-monitoring-oms/image2.png)

2. Klik **op Installeren**. U ziet een pop-up met de versie-informatie en een **installatiepakket** of **geavanceerde installatieknop.** Wanneer u op **Geavanceerde installatie**klikt, leidt dat u naar de **pagina OMS-specifieke configuratie-eigenschappen.**

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Hier wordt u gevraagd om `wsid` de (de log `wskey` Analytics-werkruimte-id) en (de primaire sleutel voor de werkruimte-id) in te voeren. Om beide `wsid` `wskey` te krijgen en je <https://mms.microsoft.com>nodig hebt om een account aan te maken op .
   Volg de stappen om een account aan te maken. Zodra u klaar bent met het maken `wsid` `wskey` van het account, moet u uw en door te klikken op **Instellingen,** dan **Verbonden Bronnen**, en vervolgens **Linux Servers**, zoals hieronder weergegeven.

   ![](media/container-service-monitoring-oms/image5.png)

4. Selecteer het gewenste aantal exemplaren en klik op de knop 'Controleren en installeren'. Normaal gesproken wilt u het aantal exemplaren hebben dat gelijk is aan het aantal VM's dat u in uw agentcluster hebt. Log Analytics-agent voor Linux installeert als afzonderlijke containers op elke VM die het informatie wil verzamelen voor het bewaken en registreren van informatie.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Een eenvoudig Log Analytics-dashboard instellen

Zodra u de Log Analytics-agent voor Linux op de VM's hebt geïnstalleerd, is de volgende stap het instellen van het Log Analytics-dashboard. U het dashboard instellen via azure portal.

### <a name="azure-portal"></a>Azure Portal 

Meld u aan <https://portal.microsoft.com/>bij Azure portal op . Ga naar **Marketplace,** selecteer **Monitoring + beheer** en klik op Alles **weergeven**. Typ `containers` vervolgens op zoek. U ziet "containers" in de zoekresultaten. Selecteer **Containers** en klik op **Maken**.

![](media/container-service-monitoring-oms/image9.png)

Zodra u op **Maken**klikt, wordt u om uw werkruimte gevraagd. Selecteer uw werkruimte of als u er geen hebt, maakt u een nieuwe werkruimte.

![](media/container-service-monitoring-oms/image10.PNG)

Nadat u uw werkruimte hebt geselecteerd, klikt u op **Maken**.

![](media/container-service-monitoring-oms/image11.png)

Voor meer informatie over de Log Analytics Container Solution verwijzen wij u naar de [Container Solution Log Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Log Analytics-agent schalen met ACS DC/OS 

Als u de Log Analytics-agent moet hebben geïnstalleerd zonder het werkelijke aantal knooppunten of als u de virtuele `msoms` machineschaal opschaalt die is ingesteld door meer VM toe te voegen, u dit doen door de service te schalen.

U naar Marathon of het tabblad DC/OS UI Services gaan en het aantal knooppuntgegevens opschalen.

![](media/container-service-monitoring-oms/image12.PNG)

Dit wordt geïmplementeerd naar andere knooppunten die de Log Analytics-agent nog niet hebben geïmplementeerd.

## <a name="uninstall-ms-oms"></a>MS OMS verwijderen

Als u MS OMS wilt verwijderen, voert u de volgende opdracht in:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Laat het ons weten!!!
Wat werkt? Wat ontbreekt er? Wat heb je nog meer nodig om dit nuttig voor je te hebben? Laat het ons weten bij <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Volgende stappen

 Nu u Log Analytics hebt ingesteld om uw containers te controleren,[raadpleegt u uw containerdashboard.](../../azure-monitor/insights/containers.md)
