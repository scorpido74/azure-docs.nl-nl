---
title: Failover over meerdere Azure CDN-eindpunten met Traffic Manager
description: Meer informatie over het instellen van Azure Traffic Manager met Azure CDN-eindpunten.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: de91f61385942db077bc98721eabe9f3f0b8624c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083009"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Failover instellen voor meerdere Azure CDN-eindpunten met Azure Traffic Manager

Wanneer u Azure Content Delivery Network (CDN) configureert, u de optimale provider en prijscategorie voor uw behoeften selecteren. Azure CDN, met zijn wereldwijd gedistribueerde infrastructuur, creëert standaard lokale en geografische redundantie en wereldwijde taakverdeling om de beschikbaarheid en prestaties van de service te verbeteren. Als een locatie niet beschikbaar is om inhoud te serveren, worden aanvragen automatisch doorgestuurd naar een andere locatie en wordt de optimale POP (op basis van factoren zoals aanvraaglocatie en serverbelasting) gebruikt om elke clientaanvraag te dienen. 
 
Als u meerdere CDN-profielen hebt, u de beschikbaarheid en prestaties verder verbeteren met Azure Traffic Manager. U Azure Traffic Manager met Azure CDN gebruiken om de balans tussen meerdere CDN-eindpunten te laden voor failover, geo-load balancing en andere scenario's. In een typisch failoverscenario worden alle clientaanvragen eerst naar het primaire CDN-profiel geleid; Als het profiel niet beschikbaar is, worden aanvragen doorgegeven aan het secundaire CDN-profiel totdat uw primaire CDN-profiel weer online is. Als u Azure Traffic Manager op deze manier gebruikt, wordt ervoor zorgen dat uw webtoepassing altijd beschikbaar is. 

In dit artikel vindt u richtlijnen en een voorbeeld van het instellen van failover met **Azure CDN Standard van Verizon** en Azure **CDN Standard van Akamai-profielen.**

## <a name="set-up-azure-cdn"></a>Azure CDN instellen 
Maak twee of meer Azure CDN-profielen en eindpunten met verschillende providers.

1. Maak een **Azure CDN-standaard vanuit Verizon** en Azure **CDN Standard vanuit het Akamai-profiel** door de stappen te volgen in [Een nieuw CDN-profiel maken.](cdn-create-new-endpoint.md#create-a-new-cdn-profile)
 
   ![CDN meerdere profielen](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Maak in elk van de nieuwe profielen ten minste één eindpunt door de stappen te volgen in [Een nieuw CDN-eindpunt maken.](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)

## <a name="set-up-azure-traffic-manager"></a>Azure Traffic Manager instellen
Maak een Azure Traffic Manager-profiel en stel taakverdeling in op uw CDN-eindpunten. 

1. Maak een Azure Traffic Manager-profiel door de stappen te volgen in [Een Traffic Manager-profiel maken](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    Selecteer **Prioriteit** **voor routeringsmethode**.

2. Voeg uw CDN-eindpunten toe aan uw Traffic Manager-profiel door de stappen te volgen in [Eindpunten voor Verkeersbeheer toevoegen](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    Selecteer voor **Tekst** **externe eindpunten**. Voer **voor Prioriteit**een getal in.

    Maak bijvoorbeeld *cdndemo101akamai.azureedge.net* met een prioriteit van *1* en *cdndemo101verizon.azureedge.net* met een prioriteit van *2*.

   ![EINDPUNTEN van CDN-verkeersbeheerder](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Aangepast domein instellen in Azure CDN en Azure Traffic Manager
Nadat u uw CDN- en Traffic Manager-profielen hebt ingesteld, voert u deze stappen uit om DNS-toewijzing toe te voegen en aangepast domein te registreren aan de CDN-eindpunten. Voor dit voorbeeld, de aangepaste domeinnaam is *cdndemo101.dustydogpetcare.online*.

1. Ga naar de website voor de domeinprovider van uw aangepaste domein, zoals GoDaddy, en maak twee DNS CNAME-vermeldingen. 

    a. Breng voor het eerste CNAME-item uw aangepaste domein, met het subdomein cdnverify, in uw CDN-eindpunt. Dit item is een vereiste stap om het aangepaste domein te registreren bij het CDN-eindpunt dat u in stap 2 aan Traffic Manager hebt toegevoegd.

      Bijvoorbeeld: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Breng voor het tweede CNAME-item uw aangepaste domein, zonder het subdomein cdnverify, in uw CDN-eindpunt. In dit item wordt het aangepaste domein toegewezen aan Traffic Manager. 

      Bijvoorbeeld: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Als uw domein momenteel live is en niet kan worden onderbroken, voert u deze stap als laatste uit. Controleer of de CDN-eindpunten en traffic manager-domeinen live zijn voordat u uw aangepaste domein-DNS bijwerkt naar Traffic Manager.
    >


2.  Selecteer in uw Azure CDN-profiel het eerste CDN-eindpunt (Akamai). Selecteer **Aangepaste domein toevoegen** en *cdndemo101.dustydogpetcare.online invoeren*. Controleer of het vinkje om het aangepaste domein te valideren groen is. 

    Azure CDN gebruikt het *subdomein cdnverify* om de DNS-toewijzing te valideren om dit registratieproces te voltooien. Zie [Een DNS-record voor CNAME maken](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)voor meer informatie. Met deze stap kan Azure CDN het aangepaste domein herkennen, zodat het kan reageren op zijn verzoeken.
    
    > [!NOTE]
    > Als u SSL op een **Azure CDN van Akamai-profielen** wilt inschakelen, moet u het aangepaste domein rechtstreeks aan uw eindpunt koppelen. cdnverify voor het inschakelen van SSL wordt nog niet ondersteund. 
    >

3.  Ga terug naar de website voor de domeinprovider van uw aangepaste domein en werk de eerste DNS-toewijzing bij waarin u hebt gemaakt, zodat het aangepaste domein wordt toegewezen aan uw tweede CDN-eindpunt.
                             
    Bijvoorbeeld: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Selecteer in uw Azure CDN-profiel het tweede CDN-eindpunt (Verizon) en herhaal stap 2. Selecteer **Aangepaste domein toevoegen**en voer *cdndemo101.dustydogpetcare.online in.*
 
Nadat u deze stappen hebt voltooid, wordt uw multi-CDN-service met failovermogelijkheden ingesteld met Azure Traffic Manager. U hebt toegang tot de test-URL's vanuit uw aangepaste domein. Als u de functionaliteit wilt testen, schakelt u het primaire CDN-eindpunt uit en controleert u of de aanvraag correct is verplaatst naar het secundaire CDN-eindpunt. 

## <a name="next-steps"></a>Volgende stappen
U ook andere routeringsmethoden instellen, zoals geografisch, om de belasting tussen verschillende CDN-eindpunten in evenwicht te brengen. Zie [De methode voor het routeren van geografisch verkeer configureren met Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)voor meer informatie.



