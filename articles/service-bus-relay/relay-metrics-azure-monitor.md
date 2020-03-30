---
title: Azure Relay-statistieken in Azure Monitor | Microsoft Documenten
description: In dit artikel vindt u informatie over hoe u Azure Monitor gebruiken om de status van Azure Relay te controleren.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273123"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Relay-statistieken in Azure Monitor 
Azure Relay-statistieken bieden u de status van resources in uw Azure-abonnement. Met een uitgebreide set metrische gegevens u de algehele status van uw Relay-resources beoordelen, niet alleen op naamruimteniveau, maar ook op entiteitsniveau. Deze statistieken kunnen belangrijk zijn omdat ze u helpen om de status van Azure Relay te controleren. Statistieken kunnen ook helpen bij het oplossen van problemen met de oorzaak van de oorzaak zonder dat u contact hoeft op te nemen met Azure-ondersteuning.

Azure Monitor biedt uniforme gebruikersinterfaces voor bewaking in verschillende Azure-services. Zie [Controle in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) en de Azure [Monitor-statistieken ophalen met het voorbeeld .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) op GitHub voor meer informatie.

> [!IMPORTANT]
> Dit artikel is alleen van toepassing op de functie Hybride verbindingen van Azure Relay, niet op het WCF Relay. 

## <a name="access-metrics"></a>Toegangsstatistieken

Azure Monitor biedt meerdere manieren om toegang te krijgen tot statistieken. U via de [Azure-portal](https://portal.azure.com)toegang krijgen tot statistieken of de Azure Monitor API's (REST en .NET) en analyseoplossingen zoals Operation Management Suite en Event Hubs gebruiken. Zie [Gegevens controleren die zijn verzameld door Azure Monitor](../azure-monitor/platform/data-platform.md)voor meer informatie.

Statistieken zijn standaard ingeschakeld en u hebt toegang tot de meest recente 30 dagen aan gegevens. Als u gegevens voor een langere periode moet bewaren, u metrische gegevens archiveren naar een Azure Storage-account. Dit is geconfigureerd in [diagnostische instellingen](../azure-monitor/platform/diagnostic-settings.md) in Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Toegang tot statistieken in de portal

U statistieken in de loop van de tijd controleren in de [Azure-portal.](https://portal.azure.com) In het volgende voorbeeld ziet u hoe u succesvolle aanvragen en binnenkomende aanvragen op accountniveau weergeven:

![][1]

U ook rechtstreeks via de naamruimte statistieken openen. Selecteer hiervoor uw naamruimte en klik op **Statistieken **. 

Voor metrische gegevens die dimensies ondersteunen, moet u filteren met de gewenste dimensiewaarde.

## <a name="billing"></a>Billing

Het gebruik van metrische gegevens in Azure Monitor is momenteel gratis tijdens het bekijken. Als u echter aanvullende oplossingen gebruikt die metrische gegevens opnemen, wordt u mogelijk gefactureerd door deze oplossingen. U wordt bijvoorbeeld gefactureerd door Azure Storage als u metrische gegevens archiveert naar een Azure Storage-account. U wordt ook gefactureerd door Azure Monitor-logboeken als u metrische gegevens streamt naar Azure Monitor-logboeken voor geavanceerde analyse.

De volgende statistieken geven u een overzicht van de status van uw service. 

> [!NOTE]
> We zijn verschillende statistieken aan het deprecating omdat ze onder een andere naam worden verplaatst. Hiervoor u uw referenties bijwerken. Statistieken die zijn gemarkeerd met het trefwoord 'afgeschaft' worden in de toekomst niet ondersteund.

Alle metrische waarden worden elke minuut naar Azure Monitor verzonden. De tijdgranulariteit definieert het tijdsinterval waarvoor metrische waarden worden weergegeven. Het ondersteunde tijdsinterval voor alle Azure Relay-statistieken is 1 minuut.

## <a name="connection-metrics"></a>Verbindingsstatistieken

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
| ListenerConnections-Succes  | Het aantal geslaagde listenerverbindingen dat gedurende een bepaalde periode met Azure Relay is gemaakt. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|ListenerConnections-ClientError |Het aantal clientfouten op listenerverbindingen over een bepaalde periode.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|ListenerConnections-ServerFout |Het aantal serverfouten op de listenerverbindingen over een bepaalde periode.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|SenderConnections-Succes |Het aantal succesvolle afzenderverbindingen dat over een bepaalde periode is gemaakt.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|SenderConnections-ClientError |Het aantal clientfouten op afzenderverbindingen gedurende een bepaalde periode.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|SenderConnections-ServerD |Het aantal serverfouten op afzenderverbindingen gedurende een bepaalde periode.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|ListenerConnections-TotaalAanvragen |Het totale aantal listenerverbindingen over een bepaalde periode.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|SenderConnections-TotaalAanvragen |De verbindingsaanvragen die de afzenders gedurende een bepaalde periode hebben gedaan.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Actieve verbindingen |Het aantal actieve verbindingen. Deze waarde is een point-in-time waarde.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Actieveluisteraars |Het aantal actieve luisteraars. Deze waarde is een point-in-time waarde.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|ListenerKoppelt de verbinding |Het aantal losgekoppelde luisteraars over een bepaalde periode.<br/><br/> Eenheid: Bytes <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Afzenderkoppelt |Het aantal losgekoppelde afzenders over een bepaalde periode.<br/><br/> Eenheid: Bytes <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|

## <a name="memory-usage-metrics"></a>Statistieken voor geheugengebruik

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
|Bytes overgedragen |Het aantal bytes dat over een bepaalde periode wordt overgedragen.<br/><br/> Eenheid: Bytes <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|

## <a name="metrics-dimensions"></a>Afmetingen met statistieken

Azure Relay ondersteunt de volgende dimensies voor metrische gegevens in Azure Monitor. Het toevoegen van dimensies aan uw statistieken is optioneel. Als u geen dimensies toevoegt, worden metrische gegevens opgegeven op het naamruimteniveau. 

|Dimensienaam|Beschrijving|
| ------------------- | ----------------- |
|Entiteitsnaam| Azure Relay ondersteunt berichtenentiteiten onder de naamruimte.|

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht Azure Monitoring](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




