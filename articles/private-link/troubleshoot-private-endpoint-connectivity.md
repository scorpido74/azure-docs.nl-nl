---
title: Problemen met connectiviteit van Azure private endpoint oplossen
description: Stapsgewijze richt lijnen voor het vaststellen van de connectiviteit van privé-eind punten
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 1c3fc4ebaffe2347a9c46b626b94042d1c9cfe11
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031853"
---
# <a name="troubleshoot-private-endpoint-connectivity-problems"></a>Problemen met connectiviteit van particuliere endpoint oplossen

Deze hand leiding bevat stapsgewijze instructies voor het valideren en diagnosticeren van uw connectiviteits instellingen voor het particuliere eind punt. 

Persoonlijk Azure-eind punt is een netwerk interface die u privé en veilig verbindt met een privé koppelings service. Met deze oplossing kunt u uw werk belastingen in azure beveiligen door particuliere verbinding te bieden met uw Azure-service resources vanuit uw virtuele netwerk. Dit brengt deze services effectief naar uw virtuele netwerk. 

Hier vindt u de verbindings scenario's die beschikbaar zijn voor privé-eind punten 
- virtueel netwerk uit dezelfde regio 
- regionaal peered virtuele netwerken
- wereld wijd gekoppelde virtuele netwerken
- on-premises klant via VPN-of Express route-circuits

## <a name="diagnosing-connectivity-problems"></a>Verbindings problemen vaststellen 
Ga naar de onderstaande stappen om ervoor te zorgen dat alle gebruikelijke configuraties naar verwachting verbindings problemen oplossen met de installatie van uw persoonlijke eind punt.

1. Bekijk de configuratie van het persoonlijke eind punt door te bladeren door de resource 

    a) Ga naar het **persoonlijke koppelings centrum**

      ![Persoonlijk koppelings centrum](./media/private-endpoint-tsg/private-link-center.png)

    b) Selecteer persoonlijke eind punten in het navigatie deel venster links
    
      ![Privé-eind punten](./media/private-endpoint-tsg/private-endpoints.png)

    c) filter en selecteer het persoonlijke eind punt dat u wilt diagnosticeren

    d) Controleer het virtuele netwerk en de DNS-gegevens
    
     - De status van de verbinding valideren is **goedgekeurd**
     - Zorg ervoor dat de virtuele machine verbinding heeft met het VNet dat als host fungeert voor de privé-eind punten
     - Er zijn FQDN-gegevens (kopie) en privé-IP-adres toegewezen
    
       ![VNet-en DNS-configuratie](./media/private-endpoint-tsg/vnet-dns-configuration.png)    
    
2. [**Azure monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) gebruiken om gegevens te controleren, loopt stroom

    a) op de privé-eindpunt resource, selecteer **monitor**
     - Selecteer gegevens-in of gegevens en controleer of de gegevens stromen wanneer er wordt geprobeerd verbinding te maken met het persoonlijke eind punt. Er wordt een vertraging van ongeveer 10 minuten verwacht.
    
       ![Telemetrie van privé-eind punt controleren](./media/private-endpoint-tsg/private-endpoint-monitor.png)

3. Problemen met VM-verbinding oplossen van **Network Watcher**

    a) Selecteer de client-VM

    b) Selecteer de sectie **problemen met verbinding oplossen** , tabblad **uitgaand verbinding**
    
      ![Uitgaande verbindingen Network Watcher-testen](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c) Selecteer **Network Watcher gebruiken voor het traceren van de detail verbinding**
    
      ![Network Watcher verbindings problemen oplossen](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d) Selecteer **testen op FQDN**
     - De FQDN van de persoonlijke eindpunt resource plakken
     - Geef een poort*op (meestal 443 voor Azure Storage of Cosmos, 1336 voor SQL...* )

    e) Klik op **testen** en valideer de test resultaten
    
      ![Network Watcher-test resultaten](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
4. De DNS-omzetting van de test resultaten moet hetzelfde persoonlijke IP-adres hebben dat is toegewezen aan het persoonlijke eind punt

    a) als de DNS-instellingen onjuist zijn, gaat u als volgt te werk
     - Privé zone gebruiken: 
       - Zorg ervoor dat VNet van de client-VM is gekoppeld aan de privé zone
       - Controleren of de Privé-DNS zone record bestaat, maken indien niet bestaande
    
     - Aangepaste DNS gebruiken:
       - Controleer de DNS-instellingen van uw klant en controleer of de DNS-configuratie juist is.
       Raadpleeg het [overzicht van privé-eind punten-DNS-configuratie](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration) voor hulp.

    b) als de connectiviteit mislukt vanwege NSG/Udr's
     - Controleer de uitgaande regels voor NSG en maak de juiste regels voor uitgaande verbindingen om verkeer toe te staan
    
       ![NSG uitgaande regels](./media/private-endpoint-tsg/nsg-outbound-rules.png)

5. Als de verbinding resultaten heeft gevalideerd, kan het connectiviteits probleem te maken hebben met andere aspecten zoals geheimen, tokens en wacht woorden op de toepassingslaag.
   - In dit geval controleert u de configuratie van de persoonlijke koppelings resource die aan het persoonlijke eind punt is gekoppeld. Raadpleeg de [hand leiding voor het oplossen van problemen met persoonlijke koppelingen](https://docs.microsoft.com/azure/private-link/private-link-connectivity-troubleshooting). 

6. Neem contact op met het [ondersteunings](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) team van Azure als uw probleem nog steeds niet is opgelost en er nog steeds een verbindings probleem is 

## <a name="next-steps"></a>Volgende stappen

 * [Een persoonlijk eind punt maken op het bijgewerkte subnet (Azure Portal)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)

 * [Gids voor probleem oplossing voor persoonlijke koppelingen](https://docs.microsoft.com/azure/private-link/private-link-connectivity-troubleshooting)
