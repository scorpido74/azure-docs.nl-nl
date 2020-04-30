---
title: Productfuncties van Azure Content Delivery Network (CDN) vergelijken| Microsoft Docs
description: Meer informatie over de functies waarvoor elk product van Azure Content Delivery Network (CDN) ondersteuning biedt.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 0e57ae691bf4b07b8161bc343929510d6be041a8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81260510"
---
# <a name="compare-azure-cdn-product-features"></a>Azure CDN-producteigenschappen vergelijken

Azure Content Delivery Network (CDN) bestaat uit vier producten: **Azure CDN Standard van Microsoft**, **Azure CDN Standard van Akamai**, **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon**. Voor informatie over het migreren van een **Azure CDN Standard van Verizon**-profiel naar **Azure CDN Premium van Verizon**, raadpleegt u [Een Azure CDN-profiel migreren van Standard Verizon naar Premium Verizon](cdn-migrate.md). Houd er rekening mee dat er een upgrade-pad van Standard Verizon naar Premium Verizon is, er is op dit moment geen conversie mechanisme tussen andere producten.

De volgende tabel vergelijkt de functies die beschikbaar zijn voor elk product.

| **Prestatie functies en-optimalisaties** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Dynamische site versnelling](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Aangeboden via de [Azure front-deur service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dynamische siteversnelling - adaptieve afbeeldingscompressie](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dynamische siteversnelling - vooraf ophalen van objecten](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Algemene optimalisatie van weblevering](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;**, selecteer dit optimalisatie type als uw gemiddelde bestands grootte kleiner is dan 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Optimalisatie van video-streaming](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | via algemene Internet levering | **&#x2713;**  | via algemene Internet levering |  via algemene Internet levering |
| [Optimalisatie van grote bestanden](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | via algemene Internet levering | **&#x2713;**, selecteer dit optimalisatie type als uw gemiddelde bestands grootte groter is dan 10 MB   | via algemene Internet levering |  via algemene Internet levering |
| Type optimalisatie wijzigen | |**&#x2713;** | | |
| Poort van oorsprong |Alle TCP-poorten |[Toegestane oorsprongs poorten](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Alle TCP-poorten |Alle TCP-poorten |
| [Globale server taak verdeling (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Snel leegmaken](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, het leegmaken van alle en het maken van joker tekens wordt niet ondersteund door Azure CDN van de huidige Akamai |**&#x2713;** |**&#x2713;** |
| [Vooraf laden van assets](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Instellingen voor cache/koptekst (met behulp van [regels voor opslaan in cache](cdn-caching-rules.md))  |**&#x2713;** met [standaard regels-engine](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Aanpas bare, op regels gebaseerde engine voor content levering |**&#x2713;** met [standaard regels-engine](cdn-standard-rules-engine.md)  | | |**&#x2713;** met behulp van [regels-engine](cdn-rules-engine.md) |
| Instellingen voor cache/header  |**&#x2713;** met [standaard regels-engine](cdn-standard-rules-engine.md) | | |**&#x2713;** met [Premium-regel engine](cdn-rules-engine.md) |
| URL-omleiding/herschrijven |**&#x2713;** met [standaard regels-engine](cdn-standard-rules-engine.md)  | | |**&#x2713;** met [Premium-regel engine](cdn-rules-engine.md) |
| Regels voor mobiele apparaten  |**&#x2713;** met [standaard regels-engine](cdn-standard-rules-engine.md) | | |**&#x2713;** met [Premium-regel engine](cdn-rules-engine.md) |
| [Queryreeksen opslaan in cache](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 dual stack | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Ondersteuning voor HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Beveiliging** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| HTTPS-ondersteuning met CDN-eindpunt | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS voor aangepaste domeinen](cdn-custom-ssl.md)  | **&#x2713;** | Hiervoor moet direct CNAME zijn ingeschakeld voor **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Ondersteuning voor aangepaste domeinnamen](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geo-filteren](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tokenverificatie](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS-beveiliging](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Uw eigen certificaat gebruiken](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Ondersteunde TLS-versies | TLS 1,2, TLS 1.0/1.1- [configureerbaar](https://docs.microsoft.com/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Analyse en rapportage** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Diagnostische logboeken in Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Verizon-kernrapporten](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Aangepaste Verizon-rapporten](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Geavanceerde HTTP-rapporten](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Realtime statistieken](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Prestaties van edge-knooppunt](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Realtime waarschuwingen](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Gebruiks gemak** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Eenvoudige integratie met Azure-services, zoals [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) en [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Beheer via [REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) of [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [MIME-typen compressie](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Alleen standaard |Configureerbaar |Configureerbaar  |Configureerbaar  |
| Compressie codering  |gzip, brotli |gzip |gzip, verkleinen, bzip2, brotili  |gzip, verkleinen, bzip2, brotili  |






