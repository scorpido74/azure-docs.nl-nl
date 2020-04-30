---
title: 'Quick Start: een persoonlijke koppelings service maken met behulp van de Azure Portal'
titlesuffix: Azure Private Link
description: Meer informatie over het maken van een privé koppelings service met behulp van de Azure Portal in deze Snelstartgids
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: f21b440ee0e2c53d9824300e85b683629c1575da
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78252547"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Snelstartgids: een persoonlijke koppelings service maken met behulp van de Azure Portal

Een Azure Private Link-service verwijst naar uw eigen service die wordt beheerd door een persoonlijke koppeling. U kunt persoonlijke koppelingen toegang verlenen tot de service of resource die achter Azure Standard Load Balancer werkt. Consumenten van uw service kunnen deze privé vanuit hun eigen virtuele netwerken benaderen. In deze Quick Start leert u hoe u een persoonlijke koppelings service kunt maken met behulp van de Azure Portal.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Een interne load balancer maken

Maak eerst een virtueel netwerk. Maak vervolgens een interne load balancer om te gebruiken met de privé koppelings service.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en para meters

In deze sectie gaat u een virtueel netwerk maken. U maakt ook het subnet om de load balancer te hosten die toegang heeft tot uw persoonlijke koppelings service.

In deze sectie moet u de volgende para meters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<de naam van het virtuele netwerk>** | myVNet          |
| **\<regio-naam>**          | VS - oost 2      |
| **\<IPv4-adres ruimte>**   | 10.3.0.0 \ 16          |
| **\<>van subnet naam**          | myBackendSubnet        |
| **\<>van het subnet-adres bereik** | 10.3.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Een standaardversie van een load balancer maken

Gebruik de portal om een standaard interne load balancer te maken. De naam en het IP-adres die u opgeeft, worden automatisch geconfigureerd als de front-end van de load balancer.

1. Selecteer in de linkerbovenhoek van de portal **een resource** > **maken netwerk** > **Load Balancer**.

1. Op het tabblad **basis principes** van de pagina **Load Balancer maken** , voert u de volgende gegevens in of selecteert u deze:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | **Abonnement**               | Selecteer uw abonnement.    |
    | **Resourcegroep**         | Selecteer **myResourceGroupLB** in het vak.|
    | **Naam**                   | Voer **myLoadBalancer**in.                                   |
    | **Regio**         | Selecteer **VS - oost 2**.                                        |
    | **Type**          | selecteer **Intern**.                                        |
    | **SKU**           | Selecteer **standaard**.                          |
    | **Virtueel netwerk**           | Selecteer **myVNet**.                          |
    | **Toewijzing van IP-adres**              | Selecteer **Statisch**.   |
    | **Privé IP-adres**|Geef een adres op dat zich in de adres ruimte van uw virtuele netwerk en subnet bevindt. Een voor beeld is 10.3.0.7.  |

1. Accepteer de standaard waarden voor de overige instellingen en selecteer vervolgens **controleren + maken** .

1. Selecteer op het tabblad **controleren en maken** de optie **maken**.

### <a name="create-standard-load-balancer-resources"></a>Standaard load balancer-resources maken

In deze sectie configureert u de instellingen voor de load balancer voor een back-endadresgroep en een statustest. U kunt ook load balancer regels opgeven.

#### <a name="create-a-back-end-pool"></a>Een back-end-pool maken

Een back-end-adres groep bevat de IP-adressen van de virtuele Nic's die zijn verbonden met de load balancer. Met deze groep kunt u verkeer naar uw resources distribueren. Maak de back-end-adres groep met de naam **myBackendPool** om resources te bevatten die verkeer verdelen.

1. Selecteer **alle services** in het meest linkse menu.
1. Selecteer **alle resources**en selecteer vervolgens **myLoadBalancer** in de lijst met resources.
1. Selecteer onder **Instellingen** de optie **Back-endpools** en selecteer vervolgens **Toevoegen**.
1. Voer op de pagina **een back-endserver toevoegen** **myBackendPool** in als de naam voor de back-end-pool en selecteer vervolgens **toevoegen**.

#### <a name="create-a-health-probe"></a>Een statustest maken

Gebruik een status test om de resource status load balancer te controleren. Op basis van een resource reactie op status controles voegt de Health prob dynamisch resources toe of verwijdert deze uit de load balancer draaiing.

Een status test maken om de status van de resources te controleren:

1. Selecteer **alle resources** in het meest linkse menu en selecteer vervolgens **myLoadBalancer** in de lijst met resources.

1. Selecteer onder **Instellingen** de optie **Statustests** en selecteer vervolgens **Toevoegen**.

1. Voer op de pagina **een status test toevoegen** de volgende waarden in of Selecteer deze:

   - **Naam**: Voer **myHealthProbe**in.
   - **Protocol**: selecteer **TCP**.
   - **Poort**: Voer **80**in.
   - **Interval**: Voer **15**in. Deze waarde is het aantal seconden tussen test pogingen.
   - **Drempel waarde voor onjuiste status**: Voer **2**in. Deze waarde is het aantal opeenvolgende test fouten dat zich voordoet voordat een virtuele machine als een slechte status wordt beschouwd.

1. Selecteer **OK**.

#### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer regel definieert hoe verkeer naar resources wordt gedistribueerd. De regel definieert:

- De front-end-IP-configuratie voor binnenkomend verkeer.
- De back-end-IP-groep om het verkeer te ontvangen.
- De vereiste bron-en doel poorten.

De load balancer regel met de naam **myLoadBalancerRule** luistert naar poort 80 in de front-end van **LoadBalancerFrontEnd** . De regel verzendt netwerk verkeer naar de **myBackendPool** back-end-adres groep op dezelfde poort 80.

Een load balancer regel maken:

1. Selecteer **alle resources** in het meest linkse menu en selecteer vervolgens **myLoadBalancer** in de lijst met resources.

1. Selecteer onder **instellingen**de optie **taakverdelings regels**en selecteer vervolgens **toevoegen**.

1. Voer op de pagina **taakverdelings regel toevoegen** de volgende waarden in of Selecteer deze als deze nog niet aanwezig zijn:

   - **Naam**: Voer **myLoadBalancerRule**in.
   - **Frontend-IP-adres:** Voer **LoadBalancerFrontEnd**in.
   - **Protocol**: selecteer **TCP**.
   - **Poort**: Voer **80**in.
   - **Backend-poort**: Voer **80**in.
   - **Back-end-pool**: Selecteer **myBackendPool**.
   - **Status test**: Selecteer **myHealthProbe**. 

1. Selecteer **OK**.

## <a name="create-a-private-link-service"></a>Een Private Link-service maken

In deze sectie maakt u een persoonlijke koppelings service achter een standaard load balancer.

1. Selecteer op het linkerbovenhoek van de pagina in het Azure Portal **een resource** > **maken netwerk** > **persoonlijk koppelings centrum (preview)**. U kunt ook het zoekvak van de portal gebruiken om te zoeken naar een privé-koppeling.

1. In het **persoonlijke koppelings centrum-overzicht** > geeft**u uw eigen service weer zodat anderen verbinding kunnen maken**. Klik vervolgens op **Start**.

1. Typ of Selecteer onder **een persoonlijke koppelings service maken**de volgende gegevens:

    | Instelling           | Waarde                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Project Details:  |                                                                              |
    | **Abonnement**      | Selecteer uw abonnement.                                                     |
    | **Resource groep**    | Selecteer **myResourceGroupLB**.                                                    |
    | Exemplaar Details: |                                                                              |
    | **Naam**              | Voer **myPrivateLinkService**in. |
    | **Regio**            | Selecteer **VS - oost 2**.                                                        |

1. Selecteer **volgende: uitgaande instellingen**.

1. Onder **een persoonlijke-koppelings service maken-instellingen voor uitgaand**, voert u de volgende gegevens in of selecteert u deze:

    | Instelling                           | Waarde                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Load Balancer**                     | Selecteer **myLoadBalancer**.                                                           |
    | **Load Balancer frontend-IP-adres** | Selecteer het front-end-IP-adres van **myLoadBalancer**.                                |
    | **Virtueel netwerk van de bron-NAT**        | Selecteer **myVNet**.                                                                   |
    | **Bron-NAT-subnet**                 | Selecteer **myBackendSubnet**.                                                          |
    | **TCP-proxy v2 inschakelen**               | Selecteer **Ja** of **Nee** , afhankelijk van of uw toepassing een TCP proxy v2-header verwacht. |
    | **Instellingen voor privé IP-adres**       | Configureer de toewijzings methode en het IP-adres voor elke NAT-IP.                  |

1. Selecteer **volgende: toegang tot beveiliging**.

1. Selecteer onder **een persoonlijke koppelings service maken-toegangs beveiliging de**optie **zicht baarheid**en kies vervolgens **alleen op rollen gebaseerd toegangs beheer**.
  
1. Selecteer **volgende: Labels** > **bekijken + maken** of kies het tabblad **controleren + maken** boven aan de pagina.

1. Controleer uw gegevens en selecteer **maken**.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het gebruik van de persoonlijke koppelings service, verwijdert u de resource groep om de resources op te schonen die in deze Quick Start worden gebruikt.

1. Voer **myResourceGroupLB** in het zoekvak boven aan de portal in en selecteer **myResourceGroupLB** in de zoek resultaten.
1. Selecteer **Resourcegroep verwijderen**.
1. Typ **myResourceGroup**in **Typ de naam van de resource groep**.
1. Selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een interne Azure load balancer en een persoonlijke koppelings service gemaakt. U kunt ook meer informatie over [het maken van een persoonlijk eind punt met behulp van de Azure Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
