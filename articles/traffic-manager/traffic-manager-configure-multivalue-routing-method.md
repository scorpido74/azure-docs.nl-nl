---
title: Beheer van multivalue-verkeer configureren - Azure Traffic Manager
description: In dit artikel wordt uitgelegd hoe u Traffic Manager configureert om verkeer naar A/AAAA-eindpunten te routeren.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: rohink
ms.openlocfilehash: daf7d09916d276130e337f7acea738228ee23707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938785"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>MultiValue-routeringsmethode configureren in Verkeersbeheer

In dit artikel wordt beschreven hoe u de methode voor het routeren van het multiwaardeverkeer configureert. Met de multivalue-verkeersrouteringsmethode u meerdere gezonde eindpunten retourneren en helpt u de betrouwbaarheid van uw toepassing te verhogen, omdat clients meer opties hebben om opnieuw te proberen zonder dat u nog een DNS-lookup hoeft uit te voeren. **Multivalue** MultiValue-routering is alleen ingeschakeld voor profielen waarvoor al hun eindpunten zijn opgegeven met IPv4- of IPv6-adressen. Wanneer een query voor dit profiel wordt ontvangen, worden alle gezonde eindpunten geretourneerd op basis van het configureerbare maximale aantal rendementen dat is opgegeven. 

>[!NOTE]
> Op dit moment wordt het toevoegen van eindpunten met IPv4- of IPv6-adressen alleen ondersteund voor eindpunten van het type **Extern** en daarom wordt MultiValue-routering ook alleen voor dergelijke eindpunten ondersteund.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure 

Meld u aan bij Azure Portal op https://portal.azure.com.
## <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resourcegroep voor het profiel Traffic Manager.
1. Selecteer **Resourcegroepen**in het linkerdeelvenster van de Azure-portal .
2. Selecteer in **resourcegroepen**boven aan de pagina de optie **Toevoegen**.
3. Typ in **de naam resourcegroep**een naam *myResourceGroupTM1*. Selecteer **oost-VS**voor **locatie resourcegroep**en selecteer **OK**.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken
Maak een Traffic Manager-profiel dat gebruikersverkeer leidt door ze naar het eindpunt met de laagste latentie te sturen.

1. Selecteer linksboven in het scherm de optie Een**netwerkverkeersbeheerprofiel** > maken voor een > **resourcenetwerkverkeer** > **maken**. **Create a resource**
2. Voer in **Het profiel Verkeer beheer maken**de volgende gegevens in of selecteer, accepteer de standaardinstellingen voor de overige instellingen en selecteer Vervolgens **Maken:**
    
    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Name                   | Deze naam moet uniek zijn binnen de zone trafficmanager.net en resulteert in de DNS-naam, trafficmanager.net, die wordt gebruikt voor het openen van uw Traffic Manager-profiel.                                   |
    | Routeringsmethode          | Selecteer de routeringsmethode **voor multiwaarde.**                                       |
    | Abonnement            | Selecteer uw abonnement.                          |
    | Resourcegroep          | Selecteer *myResourceGroupTM1*. |
    | Locatie                | Deze instelling verwijst naar de locatie van de resourcegroep en heeft geen invloed op het Traffic Manager-profiel dat wereldwijd wordt geïmplementeerd.                              |
   |        |           | 
  
   ![Een Traffic Manager-profiel maken](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen

Voeg twee IP-adressen toe als externe eindpunten aan het MultiValue Traffic Manager-profiel dat u in de vorige stap hebt gemaakt.

1. Zoek in de zoekbalk van de portal de naam van het Traffic Manager-profiel dat u in de vorige sectie hebt gemaakt en selecteer het profiel in de weergegeven resultaten.
2. Klik in **Traffic Manager-profiel**, in de sectie **Instellingen**, op **Eindpunten** en vervolgens op **Toevoegen**.
3. Voer de volgende gegevens in of selecteer de volgende gegevens, accepteer de standaardinstellingen voor de overige instellingen en selecteer **OK:**

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Type                    | Extern eindpunt                                   |
    | Name           | myEndpoint1                                        |
    | Volledig gekwalificeerde domeinnaam (FQDN) of IP           | Typ het openbare IP-adres van het eindpunt dat u wilt toevoegen aan dit Traffic Manager-profiel                         |
    |        |           |

4. Herhaal stap 2 en 3 om een ander eindpunt met de naam *myEndpoint2*toe te voegen, voor **volledig gekwalificeerde domeinnaam (FQDN) of IP**, voer het openbare IP-adres van het tweede eindpunt in.
5. Als beide eindpunten zijn toegevoegd, worden ze weergegeven in **Traffic Manager-profiel**, samen met de controlestatus **Online**.

   ![Traffic Manager-eindpunt toevoegen](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gewogen verkeersrouteringsmethode](traffic-manager-configure-weighted-routing-method.md).
- Meer informatie over [de routeringsmethode met prioriteit](traffic-manager-configure-priority-routing-method.md).
- Meer informatie over [de methode voor het routeren van prestaties](traffic-manager-configure-performance-routing-method.md)
- Meer informatie over [geografische verkeersrouteringsmethode](traffic-manager-configure-geographic-routing-method.md).


