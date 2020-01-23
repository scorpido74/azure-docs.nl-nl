---
title: Azure Relay metrische gegevens in Azure Monitor (preview) | Microsoft Docs
description: Dit artikel bevat informatie over hoe u Azure Monitor kunt gebruiken om te controleren op de status van Azure Relay.
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 5c548186ec51cf86f34942cb15d8f984afa60268
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514932"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Azure Relay metrische gegevens in Azure Monitor (preview-versie)
Azure Relay metrische gegevens geven u de status van resources in uw Azure-abonnement. Met een uitgebreide set metrische gegevens kunt u de algemene status van uw doorstuur bronnen beoordelen, niet alleen op het niveau van de naam ruimte, maar ook op het niveau van de entiteit. Deze statistieken kunnen van belang zijn wanneer u de status van Azure Relay kunt bewaken. Metrische gegevens kunnen ook helpen problemen hoofdoorzaak zonder contact opnemen met ondersteuning van Azure.

Azure Monitor biedt een uniforme gebruikersinterfaces voor bewaking over de verschillende Azure-services. Zie voor meer informatie, [bewaken in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) en de [ophalen van Azure Monitor metrics met .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) op GitHub.

> [!IMPORTANT]
> Dit artikel is alleen van toepassing op de Hybride verbindingen functie van Azure Relay, niet op de WCF Relay. 

## <a name="access-metrics"></a>Toegang tot metrische gegevens

Azure Monitor biedt meerdere manieren voor toegang tot metrische gegevens. U kunt de toegang tot metrische gegevens via de [Azure-portal](https://portal.azure.com), of gebruik de Azure Monitor API's (REST en .NET) en oplossingen voor gegevensanalyse, zoals operations Management Suite en Event Hubs. Zie voor meer informatie, [door gegevens te controleren die worden verzameld door Azure Monitor](../azure-monitor/platform/data-platform.md).

Metrische gegevens zijn standaard ingeschakeld en u hebt toegang tot gegevens van de meest recente 30 dagen. Als u behouden van gegevens voor een langere periode wilt, kunt u metrische gegevens om een Azure Storage-account te archiveren. Dit is geconfigureerd in [diagnostische instellingen](../azure-monitor/platform/diagnostic-settings.md) in Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Toegang tot metrische gegevens in de portal

U kunt metrische gegevens controleren na verloop van tijd in de [Azure-portal](https://portal.azure.com). Het volgende voorbeeld laat zien hoe om binnenkomende aanvragen op accountniveau en geslaagde aanvragen weer te geven:

![][1]

U kunt ook toegang tot metrische gegevens rechtstreeks via de naamruimte. Hiertoe selecteert u uw naam ruimte en klikt u vervolgens op **metrische gegevens (preview)** . 

Voor metrische gegevens ondersteunen dimensies, moet u filteren met de gewenste dimensie waarde.

## <a name="billing"></a>Billing

Met metrische gegevens in Azure Monitor is momenteel gratis tijdens Preview-versie. Echter, als u aanvullende oplossingen die metrische gegevens opnemen, u mogelijk worden kosten in rekening gebracht door deze oplossingen. U wordt bijvoorbeeld gefactureerd door Azure Storage als u metrische gegevens om een Azure Storage-account te archiveren. U wordt ook gefactureerd door Azure Monitor-Logboeken als u metrische gegevens streamt naar Azure Monitor-logboeken voor geavanceerde analyse.

De volgende metrische gegevens geven u een overzicht van de status van uw service. 

> [!NOTE]
> Er zijn verschillende metrische gegevens niet meer ondersteund als ze worden verplaatst onder een andere naam. Dit moet u mogelijk uw referenties bijwerken. Metrische gegevens die zijn gemarkeerd met het sleutelwoord 'afgeschaft' wordt niet ondersteund voortaan.

Alle metrische waarden worden verzonden naar Azure Monitor elke minuut. De tijdgranulatie definieert het tijdsinterval waarvoor metrische waarden worden weergegeven. Het ondersteunde tijds interval voor alle Azure Relay meet waarden is 1 minuut.

## <a name="connection-metrics"></a>Metrische verbindingsgegevens

| Naam van meetwaarde | Beschrijving |
| ------------------- | ----------------- |
| ListenerConnections-geslaagd (preview-versie) | Het aantal geslaagde listener-verbindingen dat gedurende een opgegeven periode is gemaakt aan Azure Relay. <br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|ListenerConnections-client error (preview-versie)|Het aantal client fouten op listener-verbindingen gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|ListenerConnections-server error (preview-versie)|Het aantal server fouten op de listener-verbindingen gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|SenderConnections-geslaagd (preview-versie)|Het aantal geslaagde verzender verbindingen dat gedurende een opgegeven periode is gemaakt.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|SenderConnections-client error (preview-versie)|Het aantal client fouten op de verbindingen van de afzender gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|SenderConnections-server error (preview-versie)|Het aantal server fouten op de verzender verbindingen gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|ListenerConnections-TotalRequests (preview-versie)|Het totale aantal listener-verbindingen gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|SenderConnections-TotalRequests (preview-versie)|De verbindings aanvragen van de afzenders gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|ActiveConnections (preview)|Het aantal actieve verbindingen gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|ActiveListeners (preview-versie)|Het aantal actieve listeners gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|ListenerDisconnects (preview-versie)|Het aantal niet-verbonden listeners gedurende een opgegeven periode.<br/><br/> Eenheid: Bytes <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|SenderDisconnects (preview-versie)|Het aantal niet-verbonden afzenders gedurende een opgegeven periode.<br/><br/> Eenheid: Bytes <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|

## <a name="memory-usage-metrics"></a>Metrische gegevens over geheugen gebruik

| Naam van meetwaarde | Beschrijving |
| ------------------- | ----------------- |
|BytesTransferred (preview-versie)|Het aantal bytes dat is overgedragen gedurende een opgegeven periode.<br/><br/> Eenheid: Bytes <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|

## <a name="metrics-dimensions"></a>Metrische gegevens over dimensies

Azure Relay ondersteunt de volgende dimensies voor metrische gegevens in Azure Monitor. Dimensies toevoegen aan uw metrische gegevens is optioneel. Als u dimensies niet toevoegt, worden de metrische gegevens opgegeven op het niveau van de naamruimte. 

|Dimensie naam|Beschrijving|
| ------------------- | ----------------- |
|EntityName| Azure Relay ondersteunt Messa ging-entiteiten in de naam ruimte.|

## <a name="next-steps"></a>Volgende stappen

Zie de [overzicht Azure Monitoring](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




