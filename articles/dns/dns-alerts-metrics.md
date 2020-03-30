---
title: Statistieken en waarschuwingen - Azure DNS
description: Ga met dit leerpad aan de slag met Azure DNS-statistieken en waarschuwingen.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: rohink
ms.openlocfilehash: 42acbc0d32b3ce5de4befcf112b68f611ad70542
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937444"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS-statistieken en -waarschuwingen
Azure DNS is een hostingservice voor DNS-domeinen die naamomzetting biedt met behulp van de Microsoft Azure-infrastructuur. In dit artikel worden statistieken en waarschuwingen voor de Azure DNS-service beschreven.

## <a name="azure-dns-metrics"></a>Azure DNS-statistieken

Azure DNS biedt statistieken voor klanten waarmee ze specifieke aspecten van hun DNS-zones kunnen controleren die in de service worden gehost. Bovendien u met Azure DNS-statistieken waarschuwingen configureren en ontvangen op basis van de voorwaarden van belang. De statistieken worden geleverd via de [Azure Monitor-service.](../azure-monitor/index.yml) Azure DNS biedt de volgende statistieken via Azure Monitor voor uw DNS-zones:

-   QueryVolume
-   RecordSetCount RecordSetCount RecordSetCount RecordSet
-   RecordSetCapacityUsage RecordSetCapacityUsage RecordSetCapacityUsage RecordSet

U ook de [definitie van deze statistieken](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) bekijken op de documentatiepagina van Azure Monitor.
>[!NOTE]
> Op dit moment zijn deze statistieken alleen beschikbaar voor openbare DNS-zones die worden gehost in Azure DNS. Als u privézones hebt gehost in Azure DNS, worden in deze statistieken geen gegevens voor die zones weergegeven. Bovendien wordt de functie voor metrische gegevens en waarschuwingen alleen ondersteund in de Azure Public-cloud. Steun voor soevereine wolken zal op een later tijdstip volgen. 

Het meest gedetailleerde element waarvoor u metrische gegevens zien, is een DNS-zone. U momenteel geen statistieken zien voor afzonderlijke resourcerecords binnen een zone.

### <a name="query-volume"></a>Queryvolume

De statistiek *Queryvolume* in Azure DNS toont het volume van DNS-query's (queryverkeer) dat wordt ontvangen door Azure DNS voor uw DNS-zone. De eenheid van meting is Count en de aggregatie is het totaal van alle query's die gedurende een bepaalde periode zijn ontvangen. 

Als u deze statistiek wilt weergeven, selecteert u De explorer-ervaring met statistieken (voorbeeld) op het tabblad Monitor in de Azure-portal. Selecteer uw DNS-zone in de vervolgkeuzelijst Resource, selecteer de statistiek Queryvolume en selecteer Som als aggregatie. Onderstaande screenshot toont een voorbeeld.  Zie [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md)voor meer informatie over de ervaring en grafieken van Metrics Explorer.

![Queryvolume](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Figuur: Azure DNS-queryvolumestatistieken*

### <a name="record-set-count"></a>Recordsetaantal
De statistiek *Recordset Count* geeft het aantal recordsets in Azure DNS voor uw DNS-zone weer. Alle recordsets die in uw zone zijn gedefinieerd, worden geteld. De meeteenheid is Count en de aggregatie is het maximum van alle recordsets. Als u deze statistiek wilt weergeven, selecteert u **De explorer-ervaring met statistieken (voorbeeld)** op het tabblad **Monitor** in de Azure-portal. Selecteer uw DNS-zone in de **vervolgkeuzelijst Resource,** selecteer de statistiek **RecordSet Count** en selecteer **Max** als **aggregatie**. Zie [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md)voor meer informatie over de ervaring en grafieken van Metrics Explorer. 

![Recordsetaantal](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Figuur: Azure DNS Record Set Count metrics*


### <a name="record-set-capacity-utilization"></a>Capaciteitsbenutting recordset
De statistiek *Capaciteitsbenutting recordset* in Azure DNS toont het gebruikspercentage van de capaciteit van uw recordset voor een DNS-zone. Voor elke DNS-zone in Azure DNS geldt een Recordset-limiet die het maximum aantal recordsets definieert dat is toegestaan voor de zone (zie [DNS-limieten).](dns-zones-records.md#limits) Vandaar dat deze statistiek u laat zien hoe dicht u bij het bereiken van de recordsetlimiet bent. Als u bijvoorbeeld 500 recordsets hebt geconfigureerd voor uw DNS-zone en de zone de standaardrecordsetlimiet van 5000 heeft, wordt in de statistiek RecordSetCapacityUtilization de waarde van 10% weergegeven (die wordt verkregen door 500 door 5000 te delen). De meeteenheid is **Percentage** en het **aggregatietype** is **Maximaal**. Als u deze statistiek wilt weergeven, selecteert u De explorer-ervaring met statistieken (voorbeeld) op het tabblad Monitor in de Azure-portal. Selecteer uw DNS-zone in de vervolgkeuzelijst Resource, selecteer de statistiek Capaciteitsbenutting recordset en selecteer Max als aggregatie. Onderstaande screenshot toont een voorbeeld. Zie [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md)voor meer informatie over de ervaring en grafieken van Metrics Explorer. 

![Recordsetaantal](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Figuur: Azure DNS-recordsetcapaciteitsbenuttingsstatistieken*

## <a name="alerts-in-azure-dns"></a>Waarschuwingen in Azure DNS
Azure Monitor biedt de mogelijkheid om te waarschuwen tegen beschikbare metrische waarden. De DNS-statistieken zijn beschikbaar in de nieuwe configuratie-ervaring waarschuwing. Zoals in detail beschreven in de [documentatie met Azure Monitor-waarschuwingen,](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)u DNS-zone als bron selecteren, het metrische signaaltype kiezen en de waarschuwingslogica en andere parameters configureren, zoals **periode** en **frequentie.** U verder een [actiegroep](../azure-monitor/platform/action-groups.md) definiëren voor wanneer aan de waarschuwingsvoorwaarde is voldaan, waarbij de waarschuwing wordt geleverd via de gekozen acties. Zie [Waarschuwingen maken, weergeven en beheren met Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)voor meer informatie over het configureren van waarschuwingen voor Azure Monitor. 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure DNS](dns-overview.md).
