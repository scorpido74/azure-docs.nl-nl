---
title: Azure Traffic Manager-instellingen verifiëren
description: In dit artikel leest u hoe u de instellingen voor verkeersbeheer verifiëren en de routeringsmethode voor verkeer testen.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 94ab5e550f0053fa19b9b93f1d67690211543325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938378"
---
# <a name="verify-traffic-manager-settings"></a>Traffic Manager-instellingen controleren

Als u uw Traffic Manager-instellingen wilt testen, moet u meerdere clients op verschillende locaties hebben, van waaruit u uw tests uitvoeren. Breng vervolgens de eindpunten in je Traffic Manager-profiel één voor één naar beneden.

* Stel de DNS-TTL-waarde laag in, zodat wijzigingen snel worden doorgevoerd (bijvoorbeeld 30 seconden).
* Ken de IP-adressen van uw Azure-cloudservices en -websites in het profiel dat u test.
* Gebruik hulpprogramma's waarmee u een DNS-naam oplossen naar een IP-adres en dat adres weergeven.

U controleert of de DNS-namen worden opgelost naar IP-adressen van de eindpunten in uw profiel. De namen moeten worden opgelost op een manier die overeenkomt met de verkeersrouteringsmethode die is gedefinieerd in het profiel van verkeersbeheerbeheer. U de hulpprogramma's zoals **nslookup** of **graven** gebruiken om DNS-namen op te lossen.

Met de volgende voorbeelden u uw Traffic Manager-profiel testen.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Verkeersbeheerprofiel controleren met nslookup en ipconfig in Windows

1. Open een opdracht of Windows PowerShell-prompt als beheerder.
2. Typ `ipconfig /flushdns` om de DNS resolver-cache door te spoelen.
3. Typ `nslookup <your Traffic Manager domain name>`. Met de volgende opdracht wordt bijvoorbeeld de domeinnaam gecontroleerd met het voorvoegsel *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Een typisch resultaat toont de volgende informatie:

    + De DNS-naam en het IP-adres van de DNS-server die wordt geopend om deze Traffic Manager-domeinnaam op te lossen.
    + De domeinnaam Traffic Manager die u op de opdrachtregel hebt getypt na 'nslookup' en het IP-adres waarop het domein Traffic Manager is opgelost. Het tweede IP-adres is het belangrijkste om te controleren. Het moet overeenkomen met een openbaar virtueel IP-adres (VIP) voor een van de cloudservices of -websites in het Traffic Manager-profiel dat u test.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>De failoververkeersroutemethode testen

1. Laat alle eindpunten omhoog.
2. Met behulp van één client, verzoek DNS-resolutie voor uw bedrijfsdomeinnaam met behulp van nslookup of een soortgelijk hulpprogramma.
3. Controleer of het opgeloste IP-adres overeenkomt met het primaire eindpunt.
4. Haal uw primaire eindpunt naar beneden of verwijder het bewakingsbestand, zodat Traffic Manager denkt dat de toepassing is uitgeschakeld.
5. Wacht op de DNS Time-to-Live (TTL) van het Traffic Manager-profiel plus nog eens twee minuten. Als uw DNS-TTL bijvoorbeeld 300 seconden (5 minuten) bedraagt, moet u zeven minuten wachten.
6. Spoel uw DNS-clientcache door en vraag DNS-resolutie aan met nslookup. In Windows u uw DNS-cache spoelen met de opdracht ipconfig /flushdns.
7. Zorg ervoor dat het opgeloste IP-adres overeenkomt met uw secundaire eindpunt.
8. Herhaal het proces en breng elk eindpunt op zijn beurt naar beneden. Controleer of de DNS het IP-adres van het volgende eindpunt in de lijst retourneert. Wanneer alle eindpunten zijn uitgeschakeld, moet u het IP-adres van het primaire eindpunt opnieuw verkrijgen.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>De gewogen routeringsmethode voor verkeer testen

1. Laat alle eindpunten omhoog.
2. Met behulp van één client, verzoek DNS-resolutie voor uw bedrijfsdomeinnaam met behulp van nslookup of een soortgelijk hulpprogramma.
3. Zorg ervoor dat het opgeloste IP-adres overeenkomt met een van uw eindpunten.
4. Spoel uw DNS-clientcache door en herhaal stap 2 en 3 voor elk eindpunt. U ziet verschillende IP-adressen geretourneerd voor elk van uw eindpunten.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>De methode voor het routeren van prestatieverkeer testen

Als u een methode voor het routeren van prestatieverkeer effectief wilt testen, moet u clients in verschillende delen van de wereld hebben. U clients maken in verschillende Azure-regio's die kunnen worden gebruikt om uw services te testen. Als u een wereldwijd netwerk hebt, u zich op afstand aanmelden bij clients in andere delen van de wereld en uw tests vanaf daar uitvoeren.

Als alternatief zijn er gratis web-based DNS lookup en dig services beschikbaar. Sommige van deze tools geven u de mogelijkheid om DNS-naamresolutie te controleren vanaf verschillende locaties over de hele wereld. Doe een zoekopdracht op "DNS lookup" voor voorbeelden. Services van derden zoals Gomez of Keynote kunnen worden gebruikt om te bevestigen dat uw profielen het verkeer distribueren zoals verwacht.

## <a name="next-steps"></a>Volgende stappen

* [Over verkeersbeheermethoden](traffic-manager-routing-methods.md)
* [Prestatieoverwegingen voor Traffic Manager](traffic-manager-performance-considerations.md)
* [Problemen met Traffic Manager in gedegradeerde status oplossen](traffic-manager-troubleshooting-degraded.md)
