---
title: HW-bewaking configureren met Azure Monitor voor containers | Microsoft Docs
description: In dit artikel wordt beschreven hoe u bewakings Kubernetes-clusters kunt configureren met permanente volumes met Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 30e99c2abbc66de257f9623dedc901fca51976c1
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92492158"
---
# <a name="configure-pv-monitoring-with-azure-monitor-for-containers"></a>HW-bewaking met Azure Monitor voor containers configureren

Met ingang van agent versie *ciprod10052020*ondersteunt Azure monitor voor containers Integrated agent nu het gebruik van PV (persistent volume) voor controle.

## <a name="pv-metrics"></a>Meet waarden voor hw

Azure Monitor voor containers wordt het controleren van de HW automatisch gestart door de volgende metrische gegevens te verzamelen met 60sec intervallen en op te slaan in de tabel **InsightMetrics** .

|Naam van meetwaarde |Metrische dimensie (Tags) |Beschrijving |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Gebruikte ruimte in bytes voor een specifiek permanent volume met een claim die wordt gebruikt door een specifieke pod. `pvCapacityBytes` wordt gevouwen in als een dimensie in het veld Tags om de kosten voor de opname van gegevens te verlagen en query's te vereenvoudigen.|

## <a name="monitor-persistent-volumes"></a>Permanente volumes bewaken

Azure Monitor voor containers bevat vooraf geconfigureerde grafieken voor deze metrische gegevens in een werkmap voor elk cluster. U kunt de grafieken vinden op het tabblad permanent volume van de werkmap **Details werk belasting** rechtstreeks vanuit een AKS-cluster door werkmappen te selecteren in het linkerdeel venster en in de vervolg keuzelijst **werkmappen weer geven** in het inzicht. U kunt ook een aanbevolen waarschuwing voor het gebruik van HW inschakelen en deze metrische gegevens in Log Analytics opvragen.  

![Voor beeld van werkmap van Azure Monitor HW werk belasting](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over verzamelde meet waarden voor hw [vindt u hier](https://aka.ms/ci/pvconfig).
