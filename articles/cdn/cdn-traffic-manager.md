---
title: Failover over meerdere Azure CDN-eind punten met Traffic Manager
description: Meer informatie over het instellen van Azure Traffic Manager met Azure CDN-eind punten.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 3d4f77a0fb9b8005729a6e9d35f254eb522b690e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81259847"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Failover instellen op meerdere Azure CDN-eind punten met Azure Traffic Manager

Wanneer u Azure Content Delivery Network (CDN) configureert, kunt u de optimale provider en prijs categorie voor uw behoeften selecteren. Azure CDN, met de wereld wijd gedistribueerde infra structuur, maakt standaard lokale en geografische redundantie en wereld wijde taak verdeling om de beschik baarheid en prestaties van de service te verbeteren. Als een locatie niet beschikbaar is voor het leveren van inhoud, worden aanvragen automatisch doorgestuurd naar een andere locatie en wordt de optimale POP (op basis van de factoren zoals aanvraag locatie en server belasting) gebruikt voor elke client aanvraag. 
 
Als u meerdere CDN-profielen hebt, kunt u de beschik baarheid en prestaties verder verbeteren met Azure Traffic Manager. U kunt Azure Traffic Manager gebruiken met Azure CDN voor taak verdeling tussen meerdere CDN-eind punten voor failover, geo-Load Balancing en andere scenario's. In een typisch failover-scenario worden alle client aanvragen eerst doorgestuurd naar het primaire CDN-profiel. Als het profiel niet beschikbaar is, worden aanvragen vervolgens door gegeven aan het secundaire CDN-profiel totdat uw primaire CDN-profiel weer online is. Als u Azure Traffic Manager op deze manier gebruikt, zorgt u ervoor dat uw webtoepassing altijd beschikbaar is. 

Dit artikel bevat richt lijnen en een voor beeld van het instellen van failover met **Azure CDN Standard van Verizon** en **Azure CDN Standard van Akamai** -profielen.

## <a name="set-up-azure-cdn"></a>Azure CDN instellen 
Maak twee of meer Azure CDN profielen en eind punten met verschillende providers.

1. Maak een **Azure CDN-standaard van Verizon** en **Azure CDN Standard van** het profiel Akamai door de stappen in [een nieuw CDN-profiel maken](cdn-create-new-endpoint.md#create-a-new-cdn-profile)te volgen.
 
   ![Meerdere CDN-profielen](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Maak ten minste één eind punt in elk van de nieuwe profielen door de stappen in [een nieuw CDN-eind punt maken](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)te volgen.

## <a name="set-up-azure-traffic-manager"></a>Azure Traffic Manager instellen
Maak een Azure Traffic Manager-profiel en stel taak verdeling in voor uw CDN-eind punten. 

1. Maak een Azure Traffic Manager-profiel door de stappen in [een Traffic Manager profiel maken](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile)te volgen. 

    Selecteer voor **routerings methode** **prioriteit**.

2. Voeg uw CDN-eind punten toe aan uw Traffic Manager profiel door de stappen in [Traffic Manager-eind punten toevoegen](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints) te volgen

    Selecteer bij **type** **externe eind punten**. Voer bij **prioriteit**een getal in.

    Maak bijvoorbeeld *cdndemo101akamai.azureedge.net* met prioriteit *1* en *cdndemo101verizon.azureedge.net* met prioriteit *2*.

   ![CDN Traffic Manager-eind punten](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Aangepast domein instellen op Azure CDN en Azure Traffic Manager
Nadat u uw CDN-en Traffic Manager-profielen hebt ingesteld, volgt u deze stappen om DNS-toewijzing toe te voegen en aangepast domein te registreren voor de CDN-eind punten. Voor dit voor beeld is de aangepaste domein naam *cdndemo101. dustydogpetcare. online*.

1. Ga naar de website voor de domein provider van uw aangepaste domein, zoals GoDaddy, en maak twee DNS CNAME-vermeldingen. 

    a. Wijs voor de eerste CNAME-vermelding uw aangepaste domein, met het subdomein cdnverify, toe aan uw CDN-eind punt. Dit item is een vereiste stap voor het registreren van het aangepaste domein bij het CDN-eind punt dat u in stap 2 aan Traffic Manager hebt toegevoegd.

      Bijvoorbeeld: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Voor de tweede CNAME-vermelding wijst u uw aangepaste domein, zonder het subdomein cdnverify, toe aan uw CDN-eind punt. Met deze vermelding wordt het aangepaste domein toegewezen aan Traffic Manager. 

      Bijvoorbeeld: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Als uw domein momenteel Live is en niet kan worden onderbroken, voert u deze stap als laatste uit. Controleer of de CDN-eind punten en Traffic Manager-domeinen Live zijn voordat u uw aangepaste domein-DNS bijwerkt naar Traffic Manager.
    >


2.  Selecteer in uw Azure CDN profiel het eerste CDN-eind punt (Akamai). Selecteer **aangepast domein** en invoer *cdndemo101. Dustydogpetcare. online*toevoegen. Controleer of het selectie vakje voor het valideren van het aangepaste domein groen is. 

    Azure CDN gebruikt het subdomein *cdnverify* om de DNS-toewijzing te valideren om dit registratie proces te volt ooien. Zie [een CNAME DNS-record maken](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)voor meer informatie. Met deze stap zorgt u ervoor dat Azure CDN het aangepaste domein herkent zodat het kan reageren op aanvragen.
    
    > [!NOTE]
    > Als u TLS wilt inschakelen voor een **Azure CDN van Akamai** -profielen, moet u het aangepaste domein rechtstreeks naar uw eind punt CNAME. cdnverify voor het inschakelen van TLS wordt nog niet ondersteund. 
    >

3.  Ga terug naar de website voor de domein provider van uw aangepaste domein en werk de eerste DNS-toewijzing die u hebt gemaakt in, zodat het aangepaste domein wordt toegewezen aan het tweede CDN-eind punt.
                             
    Bijvoorbeeld: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Selecteer in uw Azure CDN profiel het tweede CDN-eind punt (Verizon) en herhaal stap 2. Selecteer **aangepast domein toevoegen**en voer *cdndemo101. dustydogpetcare. online*in.
 
Nadat u deze stappen hebt voltooid, wordt de multi-CDN-service met failover-mogelijkheden ingesteld met Azure Traffic Manager. U kunt toegang krijgen tot de test-Url's vanuit uw aangepaste domein. Als u de functionaliteit wilt testen, schakelt u het primaire CDN-eind punt uit en controleert u of de aanvraag juist is verplaatst naar het secundaire CDN-eind punt. 

## <a name="next-steps"></a>Volgende stappen
U kunt ook andere routerings methoden instellen, zoals geografisch, om de belasting te verdelen over verschillende CDN-eind punten. Zie [Configure the geografische verkeers routerings methode met Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)voor meer informatie.



