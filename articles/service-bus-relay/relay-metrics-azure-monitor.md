---
title: Azure Relay metrische gegevens in Azure Monitor | Microsoft Docs
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
ms.openlocfilehash: 159249e2c997e4c414127992b08a83b488281e46
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273123"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Relay metrische gegevens in Azure Monitor 
Azure Relay metrische gegevens geven u de status van resources in uw Azure-abonnement. Met een uitgebreide set metrische gegevens kunt u de algemene status van uw doorstuur bronnen beoordelen, niet alleen op het niveau van de naam ruimte, maar ook op het niveau van de entiteit. Deze statistieken kunnen van belang zijn wanneer u de status van Azure Relay kunt bewaken. Metrische gegevens kunnen ook helpen problemen hoofdoorzaak zonder contact opnemen met ondersteuning van Azure.

Azure Monitor biedt een uniforme gebruikersinterfaces voor bewaking over de verschillende Azure-services. Zie voor meer informatie [bewaking in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) en het voor beeld [Azure monitor metrische gegevens ophalen met .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) op github.

> [!IMPORTANT]
> Dit artikel is alleen van toepassing op de Hybride verbindingen functie van Azure Relay, niet op de WCF Relay. 

## <a name="access-metrics"></a>Toegang tot metrische gegevens

Azure Monitor biedt meerdere manieren voor toegang tot metrische gegevens. U hebt toegang tot metrische gegevens via de [Azure Portal](https://portal.azure.com), of u kunt gebruikmaken van de Azure monitor API'S (rest en .net) en analyse oplossingen zoals Operation Management Suite en Event hubs. Zie [bewaking van gegevens die zijn verzameld door Azure monitor](../azure-monitor/platform/data-platform.md)voor meer informatie.

Metrische gegevens zijn standaard ingeschakeld en u hebt toegang tot gegevens van de meest recente 30 dagen. Als u behouden van gegevens voor een langere periode wilt, kunt u metrische gegevens om een Azure Storage-account te archiveren. Dit wordt geconfigureerd in [Diagnostische instellingen](../azure-monitor/platform/diagnostic-settings.md) in azure monitor.

## <a name="access-metrics-in-the-portal"></a>Toegang tot metrische gegevens in de portal

U kunt metrische gegevens gedurende een bepaalde periode bewaken in de [Azure Portal](https://portal.azure.com). Het volgende voorbeeld laat zien hoe om binnenkomende aanvragen op accountniveau en geslaagde aanvragen weer te geven:

![][1]

U kunt ook toegang tot metrische gegevens rechtstreeks via de naamruimte. Hiertoe selecteert u uw naam ruimte en klikt u vervolgens op * * metrische gegevens * *. 

Voor metrische gegevens ondersteunen dimensies, moet u filteren met de gewenste dimensie waarde.

## <a name="billing"></a>Facturering

Met metrische gegevens in Azure Monitor is momenteel gratis tijdens Preview-versie. Echter, als u aanvullende oplossingen die metrische gegevens opnemen, u mogelijk worden kosten in rekening gebracht door deze oplossingen. U wordt bijvoorbeeld gefactureerd door Azure Storage als u metrische gegevens om een Azure Storage-account te archiveren. U wordt ook gefactureerd door Azure Monitor-Logboeken als u metrische gegevens streamt naar Azure Monitor-logboeken voor geavanceerde analyse.

De volgende metrische gegevens geven u een overzicht van de status van uw service. 

> [!NOTE]
> Er zijn verschillende metrische gegevens niet meer ondersteund als ze worden verplaatst onder een andere naam. Dit moet u mogelijk uw referenties bijwerken. Metrische gegevens die zijn gemarkeerd met het sleutelwoord 'afgeschaft' wordt niet ondersteund voortaan.

Alle metrische waarden worden verzonden naar Azure Monitor elke minuut. De tijdgranulatie definieert het tijdsinterval waarvoor metrische waarden worden weergegeven. Het ondersteunde tijds interval voor alle Azure Relay meet waarden is 1 minuut.

## <a name="connection-metrics"></a>Metrische verbindingsgegevens

| Naam van meetwaarde | Beschrijving |
| ------------------- | ----------------- |
| ListenerConnections-Success  | Het aantal geslaagde listener-verbindingen dat gedurende een opgegeven periode is gemaakt aan Azure Relay. <br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|ListenerConnections-client error |Het aantal client fouten op listener-verbindingen gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|ListenerConnections-server error |Het aantal server fouten op de listener-verbindingen gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|SenderConnections-Success |Het aantal geslaagde verzender verbindingen dat gedurende een opgegeven periode is gemaakt.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|SenderConnections-client error |Het aantal client fouten op de verbindingen van de afzender gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|SenderConnections-server error |Het aantal server fouten op de verzender verbindingen gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|ListenerConnections-TotalRequests |Het totale aantal listener-verbindingen gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|SenderConnections-TotalRequests |De verbindings aanvragen van de afzenders gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|ActiveConnections |Het aantal actieve verbindingen. Deze waarde is een waarde voor een bepaald tijd punt.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|ActiveListeners |Het aantal actieve listeners. Deze waarde is een waarde voor een bepaald tijd punt.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|ListenerDisconnects |Het aantal niet-verbonden listeners gedurende een opgegeven periode.<br/><br/> Eenheid: Bytes <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|SenderDisconnects |Het aantal niet-verbonden afzenders gedurende een opgegeven periode.<br/><br/> Eenheid: Bytes <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|

## <a name="memory-usage-metrics"></a>Metrische gegevens over geheugen gebruik

| Naam van meetwaarde | Beschrijving |
| ------------------- | ----------------- |
|BytesTransferred |Het aantal bytes dat is overgedragen gedurende een opgegeven periode.<br/><br/> Eenheid: Bytes <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|

## <a name="metrics-dimensions"></a>Metrische gegevens over dimensies

Azure Relay ondersteunt de volgende dimensies voor metrische gegevens in Azure Monitor. Dimensies toevoegen aan uw metrische gegevens is optioneel. Als u dimensies niet toevoegt, worden de metrische gegevens opgegeven op het niveau van de naamruimte. 

|Dimensie naam|Beschrijving|
| ------------------- | ----------------- |
|EntityName| Azure Relay ondersteunt Messa ging-entiteiten in de naam ruimte.|

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van Azure-bewaking](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




