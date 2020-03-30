---
title: Problemen met bestandscompressie oplossen in Azure CDN | Microsoft Documenten
description: Problemen met Azure CDN-bestandscompressie oplossen.
services: cdn
documentationcenter: ''
author: sohamnc
manager: danielgi
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: aff2dadee365fcdc7e14070714aa1d2cbba901ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476420"
---
# <a name="troubleshooting-cdn-file-compression"></a>Problemen met CDN-bestandscompressie oplossen
Met dit artikel u problemen met [cdn-bestandscompressie](cdn-improve-performance.md)oplossen.

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op [de MSDN Azure- en de Stack Overflow-forums.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure Support-site](https://azure.microsoft.com/support/options/) en klik op **Ondersteuning krijgen**.

## <a name="symptom"></a>Symptoom
Compressie voor uw eindpunt is ingeschakeld, maar bestanden worden ongecomprimeerd geretourneerd.

> [!TIP]
> Om te controleren of uw bestanden worden geretourneerd gecomprimeerd, moet u een tool zoals [Fiddler](https://www.telerik.com/fiddler) of uw browser [ontwikkelaar tools](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)te gebruiken.  Controleer de HTTP-antwoordkoppen die zijn geretourneerd met uw cdn-inhoud in de cache.  Als er een `Content-Encoding` koptekst is met de naam **gzip,** **bzip2**of **leeglopen,** wordt uw inhoud gecomprimeerd.
> 
> ![Koptekst voor inhoudscodering](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Oorzaak
Er zijn verschillende mogelijke oorzaken, waaronder:

* De gevraagde inhoud komt niet in aanmerking voor compressie.
* Compressie is niet ingeschakeld voor het gevraagde bestandstype.
* De HTTP-aanvraag bevat geen koptekst die een geldig compressietype aanvraagt.
* Origin verzendt geblokte inhoud.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
> [!TIP]
> Net als bij het implementeren van nieuwe eindpunten, hebben cdn-configuratiewijzigingen enige tijd nodig om zich door het netwerk te verspreiden.  Meestal worden wijzigingen binnen 90 minuten toegepast.  Als dit de eerste keer is dat u compressie voor uw CDN-eindpunt hebt ingesteld, moet u overwegen om 1-2 uur te wachten om er zeker van te zijn dat de compressie-instellingen zijn doorgegeven aan de POP's. 
> 
> 

### <a name="verify-the-request"></a>Het verzoek verifiëren
Eerst moeten we een snelle geestelijke gezondheid controleren op het verzoek.  U de [hulpprogramma's voor ontwikkelaars](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) van uw browser gebruiken om de aanvragen te bekijken die worden uitgevoerd.

* Controleer of het verzoek wordt verzonden `<endpointname>.azureedge.net`naar uw eindpunt-URL en niet naar uw oorsprong.
* Controleer of de aanvraag een **koptekst Accepteren en coderen** bevat een koptekst voor accepteren en de waarde voor die koptekst **bevat gzip,** **leeglopen**of **bzip2**.

> [!NOTE]
> **Azure CDN van Akamai-profielen** ondersteunen alleen **gzip-codering.**
> 
> 

![CDN-aanvraagkoppen](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Compressie-instellingen controleren (standaard CDN-profielen)
> [!NOTE]
> Deze stap is alleen van toepassing als uw CDN-profiel een **Azure CDN-standaard van Microsoft,** **Azure CDN Standard van Verizon**of Azure **CDN Standard van Akamai-profiel** is. 
> 
> 

Navigeer naar uw eindpunt in de [Azure-portal](https://portal.azure.com) en klik op de knop **Configureren.**

* Controleer of compressie is ingeschakeld.
* Controleer of het MIME-type voor de inhoud die moet worden gecomprimeerd, is opgenomen in de lijst met gecomprimeerde indelingen.

![CDN-compressie-instellingen](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Compressie-instellingen controleren (Premium CDN-profielen)
> [!NOTE]
> Deze stap is alleen van toepassing als uw CDN-profiel een **Azure CDN Premium van Verizon-profiel** is.
> 
> 

Navigeer naar uw eindpunt in de [Azure-portal](https://portal.azure.com) en klik op de knop **Beheren.**  Het aanvullende portaal gaat open.  Plaats de plaats op het tabblad **HTTP Large** en plaats de plaats vervolgens boven de flyout **Cache-instellingen.**  Klik **op Compressie**. 

* Controleer of compressie is ingeschakeld.
* Controleer of de lijst **Bestandstypen** een door komma's gescheiden lijst (geen spaties) van MIME-typen bevat.
* Controleer of het MIME-type voor de inhoud die moet worden gecomprimeerd, is opgenomen in de lijst met gecomprimeerde indelingen.

![CDN premium compressie-instellingen](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Controleren of de inhoud in de cache is opgeslagen (Verizon CDN-profielen)
> [!NOTE]
> Deze stap is alleen van toepassing als uw CDN-profiel een **Azure CDN-standaard van Verizon** of Azure **CDN Premium is van het Verizon-profiel.**
> 
> 

Controleer met de hulpprogramma's voor ontwikkelaars van uw browser de antwoordkoppen om ervoor te zorgen dat het bestand in de cache wordt opgeslagen in de regio waar het wordt aangevraagd.

* Controleer de koptekst **serverrespons.**  De koptekst moet het formaat **Platform (POP/Server ID)** hebben, zoals te zien in het volgende voorbeeld.
* Controleer de **antwoordkop x-cache.**  De koptekst moet **HIT**lezen.  

![CDN-antwoordkoppen](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Controleer of het bestand voldoet aan de groottevereisten (Verizon CDN-profielen)
> [!NOTE]
> Deze stap is alleen van toepassing als uw CDN-profiel een **Azure CDN-standaard van Verizon** of Azure **CDN Premium is van het Verizon-profiel.**
> 
> 

Om in aanmerking te komen voor compressie, moet een bestand voldoen aan de volgende groottevereisten:

* Groter dan 128 bytes.
* Kleiner dan 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Controleer de aanvraag op de origin-server voor een **Via-header**
De **via** HTTP-header geeft aan de webserver aan dat het verzoek wordt doorgegeven door een proxyserver.  Microsoft IIS-webservers comprimeren standaard geen reacties wanneer het verzoek een **Via-header** bevat.  Voer het volgende uit om dit gedrag te overschrijven:

* **IIS 6**: [HcNoCompressionForProxies="FALSE" instellen in de eigenschappen van IIS Metabase](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 en meer** [: Zowel **noCompressionForHttp10** als **noCompressionForProxies** instellen op False in de serverconfiguratie](https://www.iis.net/configreference/system.webserver/httpcompression)

