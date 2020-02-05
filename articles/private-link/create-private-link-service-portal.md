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
ms.openlocfilehash: 4dd27d3fb4321c2eecf222136376bfd4b49790bb
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027774"
---
# <a name="quickstart-create-a-private-link-service-using-the-azure-portal"></a>Snelstartgids: een persoonlijke koppelings service maken met behulp van de Azure Portal

Persoonlijke koppelings service van Azure is de verwijzing naar uw eigen service die wordt aangestuurd door een persoonlijke Azure-koppeling. De service of resource die zich achter Azure Load Balancer bevindt, kan worden ingeschakeld voor toegang tot persoonlijke koppelingen. Consumenten van uw service kunnen vanaf hun eigen VNets toegang krijgen tot de service. In deze Quick Start leert u hoe u een persoonlijke koppelings service kunt maken met behulp van de Azure Portal.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Een interne load balancer maken

Maak eerst een virtueel netwerk en vervolgens een intern load balancer om met de Azure Private Link-service te gebruiken.

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een Virtual Network en het subnet voor het hosten van de load balancer die wordt gebruikt voor toegang tot uw persoonlijke koppelings service.


1. Selecteer **Een resource maken** > **Netwerken** > **Virtueel netwerk** linksboven in de portal.
   
1. In het deelvenster **Virtueel netwerk maken** typt of selecteert u de volgende waarden:
   
   - **Naam**: typ **MyVNet**.
   - **Resourcegroep**: selecteer **Nieuwe maken**, voer vervolgens **MyResourceGroupLB** in en selecteer **OK**. 
   - **Subnet** > **Naam**: typ **MyBackendSubnet**.
   
1. Selecteer **Maken**.

   ![Maak een virtueel netwerk](../load-balancer/media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-a-standard-load-balancer"></a>Een standaardversie van een load balancer maken

Maak een interne standaard load balancer met behulp van de portal. De naam en het IP-adres die u maakt, worden automatisch geconfigureerd als de front-end van de load balancer.

1. Selecteer **Een resource maken** > **Netwerken** > **Load Balancer** linksboven in het scherm.
   
2. Voer op het tabblad **Basis** van de pagina **Load balancer maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer *MyResourceGroupLB* in de vervolg keuzelijst.|
    | Name                   | *myLoadBalancer*                                   |
    | Regio         | Selecteer **US - oost 2**.                                        |
    | Type          | Selecteer **intern**.                                        |
    | SKU           | Selecteer **standaard**.                          |
    | Virtueel netwerk           | Selecteer *MyVNet*.                          |    
    | Toewijzing van IP-adres              | Selecteer **Statisch**.   |
    | Privé IP-adres|Typ een adres dat zich in de adres ruimte van uw virtuele netwerk en subnet bevindt, bijvoorbeeld *10.3.0.7*.  |

3. Klik op het tabblad **Controleren + Maken** op **Maken**. 
   

### <a name="create-standard-load-balancer-resources"></a>Standaard load balancer-resources maken

In dit gedeelte configureert u de instellingen van de load balancer voor een back-endadresgroep en een statustest, en geeft u regels voor de load balancer op.

#### <a name="create-a-backend-pool"></a>Een back-end-groep maken

Als u verkeer naar uw resources wilt distribueren, bevat een back-mailadres groep de IP-adressen van de virtuele (Nic's) die zijn verbonden met de Load Balancer. Maak de *myBackendPool* voor de back-end-adres groep zodanig dat deze resources bevat voor verkeer voor taak verdeling.

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en selecteer vervolgens **myLoadBalancer** in de lijst met resources.
2. Selecteer **Back-upgroepen**onder **instellingen**en selecteer vervolgens **toevoegen**.
3. Op de pagina **een back-endserver toevoegen** typt u *myBackendPool*, als naam voor uw back-end-pool en selecteert u vervolgens **toevoegen**.



#### <a name="create-a-health-probe"></a>Een statustest maken

Als u wilt toestaan dat de load balancer de resource status bewaken, gebruikt u een status test. Met de status test worden resources dynamisch toegevoegd aan of verwijderd uit de load balancer draaiing op basis van hun reactie op status controles. 

**Een status test maken om de status van de resources te controleren:**

1. Selecteer **Alle resources** in het linkermenu en selecteer vervolgens **MyLoadBalancer** in de lijst met resources.
   
1. Selecteer onder **Instellingen** de optie **Statustests** en selecteer vervolgens **Toevoegen**.
   
1. Op de pagina **Een statustest toevoegen** typt of selecteert u de volgende waarden:
   
   - **Naam**: typ **MyHealthProbe**.
   - **Protocol**: vervolg keuzelijst en selecteer **TCP**. 
   - **Poort**: typ **80**. 
   - **Interval**: typ **15**. Een interval is het aantal seconden tussen tests.
   - **Drempelwaarde voor onjuiste status**: typ **2**. Deze waarde duidt het aantal opeenvolgende mislukte tests aan dat moet optreden voordat wordt besloten dat een VM een onjuiste status heeft.
   
1. Selecteer **OK**.

#### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer regel definieert hoe verkeer naar resources wordt gedistribueerd. De regel definieert de front-end-IP-configuratie voor het inkomende verkeer, de back-end-IP-groep om het verkeer te ontvangen en de vereiste bron- en doelpoorten. 

De load balancer-regel met de naam **myLoadBalancerRuleWeb** luistert op poort 80 van de front-end **LoadBalancerFrontEnd**. Met de regel wordt netwerkverkeer naar de back-endadresgroep **myBackEndPool** verzonden, ook op poort 80. 

**Een load balancer-regel maken, gaat als volgt:**

1. Selecteer **Alle resources** in het linkermenu en selecteer vervolgens **MyLoadBalancer** in de lijst met resources.
   
1. Selecteer onder **instellingen**de optie **taakverdelings regels**en selecteer vervolgens **toevoegen**.
   
1. Typ of Selecteer op de pagina **taakverdelings regel toevoegen** de volgende waarden, als deze nog niet aanwezig zijn:
   
   - **Naam**: typ **MyLoadBalancerRule**.
   - **Frontend-IP-adres:** typ **LoadBalancerFrontEnd** als deze niet aanwezig is.
   - **Protocol**: selecteer **TCP**.
   - **Poort**: typ **80**.
   - **Back-endpoort**: typ **80**.
   - **Back-endpool**: selecteer **MyBackendPool**.
   - **Statustest**: selecteer **MyHealthProbe**. 
   
1. Selecteer **OK**.
   
## <a name="create-a-private-link-service"></a>Een persoonlijke koppelings service maken

In deze sectie maakt u een persoonlijke koppelings service achter een standaard load balancer.

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een resource maken** > **netwerk** > **privé koppelings centrum (preview)** . U kunt ook een persoonlijke koppeling doorzoeken via de zoek opdracht in de portal.

2. In **Private link Center: overzicht**, in de optie om **uw eigen service beschikbaar te maken, zodat anderen een verbinding met ' Start '** selecteren.

3. Typ of Selecteer in **een persoonlijke koppeling maken basis**van de service:

    | Instelling           | Waarde                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | **Project Details:**  |                                                                              |
    | Abonnement      | Selecteer uw abonnement                                                     |
    | Resourcegroep    | *MyResourceGroupLB* selecteren                                                     |
    | **EXEMPLAAR DETAILS:** |                                                                              |
    | Name              | *MyPrivateLinkService* invoeren |
    | Regio            | Selecteer *VS Oost 2*                                                         |

4. Selecteer **volgende: uitgaande instellingen**.

5. Voer in **een persoonlijke-koppelings service maken-instellingen voor uitgaand**de volgende gegevens in of Selecteer deze:


    | Instelling                           | Waarde                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | Load Balancer                     | *MyLoadBalancer* selecteren                                                           |
    | Load Balancer frontend-IP-adres | Het frontend-IP-adres van MyLoadBalancer selecteren                                |
    | Virtueel netwerk van de bron-NAT        | *MyVNET* selecteren                                                                   |
    | Bron-NAT-subnet                 | *MyBackendSubnet* selecteren                                                          |
    | TCP-proxy v2 inschakelen               | Selecteer Ja/Nee, afhankelijk van of de toepassing TCP proxy v2-header verwacht |
    | Instellingen voor privé IP-adres       | De toewijzings methode en het IP-adres voor elke NAT-IP configureren                  |

6. Selecteer **volgende: toegang tot beveiliging**.

7. In **Maak een persoonlijke koppeling service-toegang beveiliging**, voert u de volgende gegevens in of selecteert u deze:

    | Instelling                                     | Waarde                                                             |
    |---------------------------------------------|-------------------------------------------------------------------|
    | Zichtbaarheid                                  | *Alleen op rollen gebaseerd toegangs beheer* kiezen         |
  
8. Selecteer **volgende: Tags**, vervolgens **bekijken + maken** of kies het **tabblad controleren + maken** boven aan de pagina.

9. Controleer uw gegevens en selecteer **maken**.

## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer u klaar bent met het gebruik van de persoonlijke koppelings service, verwijdert u de resource groep om de resources op te schonen die in deze Quick Start worden gebruikt:

1. Voer *myResourceGroupLB* in het **zoekvak** boven aan de portal in en selecteer *myResourceGroupLB* in de zoek resultaten. 
2. Selecteer **Resourcegroep verwijderen**. 
3. Voer myResourceGroup in bij **Typ de naam van de resource groep** en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een interne Azure load balancer en een persoonlijke koppelings service gemaakt. Zie voor meer informatie over het maken van privé-eind punten [persoonlijke eind punten maken met behulp van Azure Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
