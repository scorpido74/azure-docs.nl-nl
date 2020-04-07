---
title: Productfuncties van Azure Content Delivery Network (CDN) vergelijken| Microsoft Docs
description: Meer informatie over de functies waarvoor elk product van Azure Content Delivery Network (CDN) ondersteuning biedt.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 540dd51abf8b832194c3814c8198cb72cc60d348
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745447"
---
# <a name="compare-azure-cdn-product-features"></a>Azure CDN-producteigenschappen vergelijken

Azure Content Delivery Network (CDN) bestaat uit vier producten: **Azure CDN Standard van Microsoft**, **Azure CDN Standard van Akamai**, **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon**. Voor informatie over het migreren van een **Azure CDN Standard van Verizon**-profiel naar **Azure CDN Premium van Verizon**, raadpleegt u [Een Azure CDN-profiel migreren van Standard Verizon naar Premium Verizon](cdn-migrate.md). Merk op dat, terwijl er een upgrade pad van Standard Verizon naar Premium Verizon, is er geen conversie mechanisme tussen andere producten op dit moment.

De volgende tabel vergelijkt de functies die beschikbaar zijn voor elk product.

| **Prestatiefuncties en optimalisaties** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Dynamische siteversnelling](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Aangeboden via [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dynamische siteversnelling - adaptieve afbeeldingscompressie](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dynamische siteversnelling - vooraf ophalen van objecten](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Algemene webdelivery-optimalisatie](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;**, Selecteer dit optimalisatietype als de gemiddelde bestandsgrootte kleiner is dan 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Optimalisatie van videostreaming](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | via Algemene Webdelivery | **&#x2713;**  | via Algemene Webdelivery |  via Algemene Webdelivery |
| [Grote bestandsoptimalisatie](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | via Algemene Webdelivery | **&#x2713;**, Selecteer dit optimalisatietype als de gemiddelde bestandsgrootte groter is dan 10 MB   | via Algemene Webdelivery |  via Algemene Webdelivery |
| Optimalisatietype wijzigen | |**&#x2713;** | | |
| Oorsprongspoort |Alle TCP-poorten |[Toegestane oorsprongspoorten](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Alle TCP-poorten |Alle TCP-poorten |
| [GsLB (Global server load balancing)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Snel leegmaken](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;, **Purge all en Wildcard purge worden momenteel niet ondersteund door Azure CDN van Akamai |**&#x2713;** |**&#x2713;** |
| [Vooraf laden van assets](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Instellingen voor cache/koptekst (met behulp van [regels voor opslaan in cache](cdn-caching-rules.md))  |**&#x2713;** met [standaardregelsengine](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Aanpasbare, op regels gebaseerde engine voor het leveren van inhoud |**&#x2713;** met [standaardregelsengine](cdn-standard-rules-engine.md)  | | |**&#x2713;** met behulp van [regels motor](cdn-rules-engine.md) |
| Cache-/koptekstinstellingen  |**&#x2713;** met [standaardregelsengine](cdn-standard-rules-engine.md) | | |**&#x2713;** met [de Premium-regelsengine](cdn-rules-engine.md) |
| URL omleiden/herschrijven |**&#x2713;** met [standaardregelsengine](cdn-standard-rules-engine.md)  | | |**&#x2713;** met [de Premium-regelsengine](cdn-rules-engine.md) |
| Regels voor mobiele apparaten  |**&#x2713;** met [standaardregelsengine](cdn-standard-rules-engine.md) | | |**&#x2713;** met [de Premium-regelsengine](cdn-rules-engine.md) |
| [Queryreeksen opslaan in cache](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 dual stack | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Ondersteuning voor HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Beveiliging** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| HTTPS-ondersteuning met CDN-eindpunt | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS voor aangepaste domeinen](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;**, vereist directe CNAME om |**&#x2713;** |**&#x2713;** |
| [Ondersteuning voor aangepaste domeinnamen](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geo-filteren](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tokenverificatie](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS-bescherming](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Uw eigen certificaat gebruiken](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Ondersteunde TLS-versies | TLS 1.2, TLS 1.0/1.1 - [Configureerbaar](https://docs.microsoft.com/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Analyses en rapportages** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Diagnostische logboeken in Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Verizon-kernrapporten](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Aangepaste Verizon-rapporten](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Geavanceerde HTTP-rapporten](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Realtime statistieken](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Prestaties van edge-knooppunt](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Realtime waarschuwingen](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Gebruiksgemak** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Eenvoudige integratie met Azure-services, zoals [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) en [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Beheer via [REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) of [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [MIME-typen compressie](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Standaard alleen |Configureerbaar |Configureerbaar  |Configureerbaar  |
| Compressiecoderingen  |gzip, brotli |Gzip |gzip, leeglopen, bzip2, brotili  |gzip, leeglopen, bzip2, brotili  |






