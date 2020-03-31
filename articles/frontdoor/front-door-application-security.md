---
title: Azure Front Door - Beveiliging van toepassingslagen | Microsoft Documenten
description: Dit artikel helpt u te begrijpen hoe Azure Front Door de back-ends van uw toepassing beschermen en beveiligen
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: e458926930c1b95d48886559551878fc6c9d0673
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471792"
---
# <a name="application-layer-security-with-front-door"></a>Beveiliging van toepassingslagen met voordeur
Azure Front Door biedt webapplicatiebescherming om uw webtoepassingen te beschermen tegen netwerkaanvallen en veelvoorkomende webkwetsbaarheden zoals SQL Injection of Cross Site Scripting (XSS). Ingeschakeld voor http(s) front-ends, Front Door's applicatie laag beveiliging is wereldwijd gedistribueerd en altijd aan, het stoppen van kwaadaardige aanvallen op azure's netwerk rand, ver weg van uw backends. Met extra beveiliging en prestatieoptimalisatie biedt Front Door snelle en veilige webervaringen aan uw eindgebruikers.

## <a name="application-protection"></a>Toepassingsbescherming
De applicatiebeveiliging van Front Door is geconfigureerd op elke randomgeving over de hele wereld, in lijn met toepassingen, en blokkeert automatisch niet-http(s) verkeer van het bereiken van uw webapplicaties. Onze multi-tenant gedistribueerde architectuur maakt wereldwijde bescherming op schaal mogelijk zonder in te leveren op prestaties. Voor http(s) workloads biedt de webapplicatiebeveiligingsservice van Front Door een uitgebreide regelsengine voor aangepaste regels, vooraf geconfigureerde regelset tegen veelvoorkomende aanvallen en gedetailleerde logboekregistratie voor alle aanvragen die overeenkomen met een regel. Flexibele acties, waaronder alleen toestaan, blokkeren of loggen, worden ondersteund.

## <a name="custom-access-control-rules"></a>Aangepaste regels voor toegangscontrole
- **LIJST- en bloklijst voor IP-toestaan:** U aangepaste regels configureren om de toegang tot uw webtoepassingen te beheren op basis van een lijst met IP-adressen van client. Zowel IP v4 als IP v6 worden ondersteund
- **Geografisch gebaseerd toegangsbeheer:** U aangepaste regels configureren om de toegang tot uw webtoepassingen te beheren op basis van landcode die een client-IP
- **HTTP-parameters filteren:** U aangepaste toegangsregels configureren op basis van overeenkomende http-queryparameters, waaronder kopteksten, URL' s en querytekenreeksen

## <a name="azure-managed-rules"></a>Azure-beheerde regels
- Een vooraf geconfigureerde set regels tegen veelvoorkomende OWASP-kwetsbaarheden is standaard ingeschakeld. Bij preview, de set van regels omvat sqli en xss verzoeken controleren. Er worden aanvullende regels toegevoegd. U ervoor kiezen om te beginnen met alleen logboekactie om vooraf geconfigureerde regels te valideren die werken zoals verwacht voor uw toepassingen 

## <a name="rate-limiting"></a>Snelheidsbeperking
- Een regel voor tariefcontrole is om abnormaal hoog verkeer van een client-IP te beperken.  U een drempel waarde instellen voor het aantal webaanvragen dat een client-IP gedurende een duur van één minuut heeft toegestaan.

## <a name="centralized-protection-policy"></a>Gecentraliseerd beschermingsbeleid
- U verschillende beveiligingsregels definiëren en deze in prioriteitsvolgorde toevoegen aan een beleid. Aangepaste regels hebben een hogere prioriteit dan beheerde regelset om uitzonderingen toe te staan. Aan uw webtoepassing is één beleid gekoppeld.  Hetzelfde beleid voor de bescherming van webapplicaties wordt gerepliceerd naar alle edge-servers op alle locaties, zorgen voor een consistent beveiligingsbeleid in alle regio's

## <a name="configuration"></a>Configuratie
- Tijdens de preview u REST API's, PowerShell of CLI gebruiken om de regels en beleidsregels voor toepassingsbescherming van Front Door te maken en te implementeren. Toegang tot portalen wordt ondersteund voordat de service algemeen beschikbaar is. 


## <a name="monitoring"></a>Bewaking
Front Door biedt de mogelijkheid om webapplicaties te monitoren tegen aanvallen met behulp van realtime statistieken die zijn geïntegreerd met Azure Monitor om waarschuwingen bij te houden en trends eenvoudig te volgen.

## <a name="pricing"></a>Prijzen
De beveiliging van de toepassingslaag van De voordeur is gratis tijdens de preview.


## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
