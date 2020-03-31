---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176666"
---
SPF-records (Sender Policy Framework) worden gebruikt om aan te geven welke e-mailservers e-mail namens een domeinnaam kunnen verzenden. Een correcte configuratie van SPF-records is belangrijk om te voorkomen dat ontvangers uw e-mail als ongewenste e-mail markeren.

De DNS RFC's introduceerden oorspronkelijk een nieuw SPF-recordtype om dit scenario te ondersteunen. Om oudere naamservers te ondersteunen, stonden ze ook toe dat het type TXT-record wordt gebruikt om SPF-records op te geven. Deze dubbelzinnigheid leidde tot verwarring, die werd opgelost door [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Daarin staat dat SPF-records moeten worden gemaakt met behulp van het type TXT-record. Het stelt ook dat de SPF record type is afgeschaft.

**SPF-records worden ondersteund door Azure DNS en moeten worden gemaakt met behulp van het TXT-recordtype.** Het verouderde SPF-recordtype wordt niet ondersteund. Wanneer u [een DNS-zonebestand importeert,](../articles/dns/dns-import-export.md)worden alle SPF-records die het SPF-recordtype gebruiken, geconverteerd naar het recordtype TXT.
