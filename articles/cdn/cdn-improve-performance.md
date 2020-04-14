---
title: Prestaties verbeteren door bestanden in Azure CDN te comprimeren | Microsoft Documenten
description: Meer informatie over het verbeteren van de snelheid van bestandsoverdracht en het verbeteren van de prestaties van pagina's door uw bestanden in Azure CDN te comprimeren.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: 7124dd40d4510674014afe012a8f40dcb5bb6153
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253761"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>De prestaties verbeteren door bestanden in Azure CDN te comprimeren
Bestandscompressie is een eenvoudige en effectieve methode om de bestandsoverdrachtsnelheid te verbeteren en de prestaties van de paginabelasting te verhogen door de grootte van een bestand te verkleinen voordat het vanaf de server wordt verzonden. Bestandscompressie kan de bandbreedtekosten verlagen en uw gebruikers een responsievere ervaring bieden.

Er zijn twee manieren om bestandscompressie in te schakelen:

- Compressie inschakelen op uw origin-server. In dit geval passeert Azure CDN de gecomprimeerde bestanden en levert deze aan clients die daarom vragen.
- Compressie direct inschakelen op de CDN POP-servers *(compressie on the fly).* In dit geval comprimeert het CDN de bestanden en dient ze aan de eindgebruikers, zelfs als ze niet zijn gecomprimeerd door de origin-server.

> [!IMPORTANT]
> Azure CDN-configuratiewijzigingen kunnen enige tijd duren voordat het netwerk wordt doorgegeven: 
> - Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen 10 minuten voltooid. 
> - Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
> - Profielen van **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** worden normaal gesproken binnen 10 minuten doorgegeven. 
> 
> Als u compressie voor de eerste keer instelt voor uw CDN-eindpunt, u overwegen om 1 tot 2 uur te wachten voordat u problemen oplost om ervoor te zorgen dat de compressie-instellingen zijn doorgegeven aan de POP's.

## <a name="enabling-compression"></a>Compressie inschakelen
De standaard- en premium CDN-lagen bieden dezelfde compressiefunctionaliteit, maar de gebruikersinterface verschilt. Zie [Azure CDN-overzicht](cdn-overview.md)voor meer informatie over de verschillen tussen standaard- en premium CDN-lagen.

### <a name="standard-cdn-profiles"></a>Standaard CDN-profielen 
> [!NOTE]
> Deze sectie is van toepassing op **Azure CDN Standard van Microsoft,** **Azure CDN Standard van Verizon**en Azure **CDN Standard van Akamai-profielen.**
> 
> 

1. Selecteer op de profielpagina van CDN het CDN-eindpunt dat u wilt beheren.

    ![CDN-profieleindpunten](./media/cdn-file-compression/cdn-endpoints.png)

    De cdn-eindpuntpagina wordt geopend.
2. Selecteer **Compressie**.

    ![CDN-compressieselectie](./media/cdn-file-compression/cdn-compress-select-std.png)

    De compressiepagina wordt geopend.
3. Selecteer **Aan** om compressie in te schakelen.

    ![Opties voor cdn-bestandscompressie](./media/cdn-file-compression/cdn-compress-standard.png)
4. Gebruik de standaard MIME-typen of wijzig de lijst door MIME-typen toe te voegen of te verwijderen.

   > [!TIP]
   > Hoewel het mogelijk is, wordt het niet aanbevolen om compressie toe te passen op gecomprimeerde formaten. Bijvoorbeeld ZIP, MP3, MP4 of JPG.
   > 

   > [!NOTE]
   > Het wijzigen van de standaardlijst met MIME-typen wordt momenteel niet ondersteund in Azure CDN Standard van Microsoft.
   > 

5. Nadat u de wijzigingen hebt aangebracht, selecteert u **Opslaan**.

### <a name="premium-cdn-profiles"></a>Premium CDN-profielen
> [!NOTE]
> Deze sectie is alleen van toepassing op **Azure CDN Premium van Verizon-profielen.**
> 

1. Selecteer beheren op de profielpagina **van**CDN .

    ![CDN Beheren selecteren](./media/cdn-file-compression/cdn-manage-btn.png)

    Het CDN-beheerportaal wordt geopend.
2. Plaats de plaats op het tabblad **HTTP Large** en plaats de plaats vervolgens boven de flyout **Cache-instellingen.** Selecteer **Compressie**.

    ![CDN-compressieselectie](./media/cdn-file-compression/cdn-compress-select.png)

    De compressieopties worden weergegeven.

    ![Opties voor cdn-bestandscompressie](./media/cdn-file-compression/cdn-compress-files.png)
3. Compressie inschakelen door **Compressie ingeschakeld te**selecteren . Voer de MIME-typen in die u wilt comprimeren als een lijst met komma's (geen spaties) in het vak **Bestandstypen.**

   > [!TIP]
   > Hoewel het mogelijk is, wordt het niet aanbevolen om compressie toe te passen op gecomprimeerde formaten. Bijvoorbeeld ZIP, MP3, MP4 of JPG.
   > 

4. Nadat u de wijzigingen hebt aangebracht, selecteert u **Bijwerken**.

## <a name="compression-rules"></a>Compressieregels

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN-standaard uit Microsoft-profielen

Voor **Azure CDN Standard van Microsoft-profielen** worden alleen in aanmerking komende bestanden gecomprimeerd. Om in aanmerking te komen voor compressie, moet een bestand:
- Be van een MIME-type dat is [geconfigureerd voor compressie.](#enabling-compression)
- Groter zijn dan 1 KB
- Kleiner zijn dan 8 MB

Deze profielen ondersteunen de volgende compressiecoderingen:
- gzip (GNU zip)
- brotli 

Als de aanvraag meer dan één compressietype ondersteunt, heeft brotlicompressie voorrang.

Wanneer een aanvraag voor een asset gzip-compressie opgeeft en de aanvraag resulteert in een cachemiss, voert Azure CDN gzip-compressie van het item rechtstreeks uit op de POP-server. Daarna wordt het gecomprimeerde bestand vanuit de cache weergegeven.

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN van Verizon-profielen

Voor **Azure CDN Standard van Verizon** en Azure **CDN Premium uit Verizon-profielen** worden alleen in aanmerking komende bestanden gecomprimeerd. Om in aanmerking te komen voor compressie, moet een bestand:
- Groter zijn dan 128 bytes
- Kleiner zijn dan 3 MB

Deze profielen ondersteunen de volgende compressiecoderingen:
- gzip (GNU zip)
- Deflate
- bzip2 bzip2
- brotli 

Als de aanvraag meer dan één compressietype ondersteunt, hebben deze compressietypen voorrang op brotlicompressie.

Wanneer een aanvraag voor een asset brotlicompressie opgeeft (HTTP-header is) `Accept-Encoding: br`en de aanvraag resulteert in een cachemiss, voert Azure CDN brotli-compressie van het item rechtstreeks uit op de POP-server. Daarna wordt het gecomprimeerde bestand vanuit de cache weergegeven.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN-standaard van Akamai-profielen

Voor **Azure CDN Standard van Akamai-profielen** komen alle bestanden in aanmerking voor compressie. Een bestand moet echter een MIME-type zijn dat is [geconfigureerd voor compressie.](#enabling-compression)

Deze profielen ondersteunen alleen gzip-compressiecodering. Wanneer een profieleindpunt een gzip-gecodeerd bestand aanvraagt, wordt het altijd gevraagd bij de oorsprong, ongeacht het clientverzoek. 

## <a name="compression-behavior-tables"></a>Compressiegedragstabellen
In de volgende tabellen wordt het compressiegedrag van Azure CDN voor elk scenario beschreven:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Compressie is uitgeschakeld of bestand komt niet in aanmerking voor compressie
| Door de client aangevraagde indeling (via header Accepteren encoderen) | Bestandsindeling in cache | De CDN-reactie op de client | &nbsp; &nbsp; Opmerkingen&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Gecomprimeerd |Gecomprimeerd |Gecomprimeerd | |
| Gecomprimeerd |Ongecomprimeerde |Ongecomprimeerde | |
| Gecomprimeerd |Niet in de cache |Gecomprimeerd of niet-gecomprimeerd |De oorsprongsrespons bepaalt of CDN een compressie uitvoert. |
| Ongecomprimeerde |Gecomprimeerd |Ongecomprimeerde | |
| Ongecomprimeerde |Ongecomprimeerde |Ongecomprimeerde | |
| Ongecomprimeerde |Niet in de cache |Ongecomprimeerde | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Compressie is ingeschakeld en bestand komt in aanmerking voor compressie
| Door de client aangevraagde indeling (via header Accepteren encoderen) | Bestandsindeling in cache | CDN-antwoord op de client | Opmerkingen |
| --- | --- | --- | --- |
| Gecomprimeerd |Gecomprimeerd |Gecomprimeerd |CDN-transcodes tussen ondersteunde indelingen. |
| Gecomprimeerd |Ongecomprimeerde |Gecomprimeerd |CDN voert een compressie uit. |
| Gecomprimeerd |Niet in de cache |Gecomprimeerd |CDN voert een compressie uit als de oorsprong een niet-gecomprimeerd bestand retourneert. <br/>**Azure CDN van Verizon** passeert het niet-gecomprimeerde bestand op het eerste verzoek en comprimeert en caches het bestand voor volgende aanvragen. <br/>Bestanden met `Cache-Control: no-cache` de koptekst worden nooit gecomprimeerd. |
| Ongecomprimeerde |Gecomprimeerd |Ongecomprimeerde |CDN voert een decompressie uit. |
| Ongecomprimeerde |Ongecomprimeerde |Ongecomprimeerde | |
| Ongecomprimeerde |Niet in de cache |Ongecomprimeerde | |

## <a name="media-services-cdn-compression"></a>Media Services CDN-compressie
Voor eindpunten die zijn ingeschakeld voor CDN-streaming van Media Services, is compressie standaard ingeschakeld voor de volgende MIME-typen: 
- toepassing/vnd.ms-sstr+xml 
- toepassing/streepje+xml
- toepassing/vnd.apple.mpegurl
- toepassing/f4m+xml 

## <a name="see-also"></a>Zie ook
* [Problemen met CDN-bestandscompressie oplossen](cdn-troubleshoot-compression.md)    

