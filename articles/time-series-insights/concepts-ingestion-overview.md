---
title: Overzicht van opname-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over gegevens opname in Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: ecee6817b58639d1b60264c818ab9517e5d133e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049959"
---
# <a name="azure-time-series-insights-data-ingestion-overview"></a>Overzicht van Azure Time Series Insights gegevens opname

Uw Azure Time Series Insights omgeving bevat een *opname-engine* voor het verzamelen, verwerken en opslaan van streaming time series-gegevens. Wanneer gegevens binnenkomen in uw gebeurtenis bron (nen), wordt Azure Time Series Insights uw gegevens in bijna realtime gebruikt en opgeslagen.

[![Overzicht van opname](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Onderwerpen over opname

De volgende artikelen omvatten uitgebreide gegevens verwerking, waaronder aanbevolen procedures voor het volgen van:

* Meer informatie over [gebeurtenis bronnen](concepts-streaming-ingestion-event-sources.md) en richt lijnen voor het selecteren van een tijds tempel van een gebeurtenis bron.

* De ondersteunde [gegevens typen](concepts-supported-data-types.md) controleren

* Meer informatie over hoe de opname-engine een reeks [regels](./concepts-json-flattening-escaping-rules.md) toepast op uw JSON-eigenschappen om uw opslag account kolommen te maken.

* Controleer de [doorvoer beperkingen](concepts-streaming-throughput-limitations.md) van uw omgeving om te plannen voor uw schaal behoeften.

## <a name="next-steps"></a>Volgende stappen

* Ga door om meer te weten te komen over [gebeurtenis bronnen](concepts-streaming-ingestion-event-sources.md) voor uw Azure time series Insights omgeving. 
