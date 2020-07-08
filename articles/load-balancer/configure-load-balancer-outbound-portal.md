---
title: Configureer taak verdeling en uitgaande regels met behulp van de Azure Portal
titleSuffix: Azure Load Balancer
description: In dit artikel wordt beschreven hoe u taak verdeling en uitgaande regels in Standard Load Balancer kunt configureren met behulp van de Azure Portal.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 2dff916bf005b307f27264ad7a17864fbba50872
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367390"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Configureer taak verdeling en uitgaande regels in Standard Load Balancer door gebruik te maken van de Azure Portal

In dit artikel leest u hoe u uitgaande regels in Standard Load Balancer kunt configureren met behulp van de Azure Portal.  

De load balancer resource bevat twee front-ends en de bijbehorende regels. U hebt één front-end voor inkomend verkeer en een andere front-end voor uitgaand verkeer.  

Elke front-end verwijst naar een openbaar IP-adres. In dit scenario verschilt het open bare IP-adres voor inkomend verkeer van het adres voor uitgaand verkeer.   De taakverdelings regel biedt alleen binnenkomende taak verdeling. De regel voor uitgaand verkeer bepaalt de uitgaande Network Address Translation (NAT) voor de virtuele machine.  

In het scenario worden twee back-endservers gebruikt: één voor binnenkomend verkeer en één voor uitgaand verkeer. Deze groepen illustreren de mogelijkheden en bieden flexibiliteit voor het scenario.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Een load balancer maken

In deze sectie maakt u een load balancer waarmee de virtuele machines worden verdeeld. U kunt een open bare load balancer of een interne load balancer maken. Wanneer u een open bare load balancer maakt, maakt u een nieuw openbaar IP-adres dat is geconfigureerd als de front-end voor de load balancer. De front-end krijgt standaard de naam **LoadBalancerFrontEnd** .

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Load balancer**.
2. Voer op het tabblad **basis beginselen** van de pagina **Load Balancer maken** de volgende informatie in of Selecteer deze:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **Nieuwe maken** en typ **myResourceGroupSLB** in het tekstvak.|
    | Naam                   | **myLoadBalancer**                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | Selecteer **Openbaar**.                                        |
    | SKU           | selecteer **Standaard**. |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. Als u een bestaand openbaar IP-adres hebt dat u wilt gebruiken, selecteert u **bestaande gebruiken**.  Een bestaand openbaar IP-adres moet een **standaard** -SKU zijn.  Algemene open bare Ip's zijn niet compatibel met de **standaard** SKU Load Balancer.  |
    | Naam openbaar IP-adres              | Typ **myPublicIP** in het tekstvak.|
    | Beschikbaarheidszone | Selecteer **zone-redundant** voor het maken van een flexibele Load Balancer. Als u een zonegebonden load balancer wilt maken, selecteert u een specifieke zone uit 1, 2 of 3 |

3. Accepteer de standaard waarden voor de rest van de configuratie.
4. Selecteer **controleren + maken**

    > [!IMPORTANT]
    > In de rest van deze quickstart wordt ervan uitgegaan dat de **Standard** SKU wordt gekozen tijdens het bovenstaande SKU-selectieproces.

5. Selecteer in het deelvenster **Beoordelen en maken** de optie **Maken**.   

    ![Een Load Balancer van het type Standard maken](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Resources voor load balancer maken

In deze sectie configureert u de instellingen van de load balancer voor een back-endadresgroep en een statustest en geeft u een regel voor de load balancer op.

### <a name="create-a-backend-pool"></a>Een back-endpool maken

Een back-end-adres groep bevat de IP-adressen van de virtuele Nic's in de back-end-pool. Maak de back-endadres groep **myBackendPool** om virtuele machines voor het verdelen van internetverkeer in te bewaren.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.
2. Selecteer onder **Instellingen** de optie **Back-endpools** en selecteer vervolgens **Toevoegen**.
3. Typ op de pagina **Back-endpool toevoegen** **myBackendPool** als naam voor de back-endpool. Selecteer vervolgens **Toevoegen**.

### <a name="create-a-health-probe"></a>Een statustest maken

Een status test wordt gebruikt om de status van uw app te bewaken. Met de status test worden virtuele machines toegevoegd aan of verwijderd uit de load balancer op basis van hun reactie op status controles. Maak een statustest (**myHealthProbe**) om de status van de VM's te bewaken.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.
2. Selecteer onder **Instellingen** de optie **Statustests** en selecteer vervolgens **Toevoegen**.
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer **myHealthProbe** in. |
    | Protocol | Selecteer **HTTP**. |
    | Poort | Voer **80** in.|
    | Interval | Voer **15** in als waarde voor het **Interval** in seconden tussen tests. |
    | Drempelwaarde voor beschadigde status | Selecteer **2** als het aantal voor de **Drempelwaarde voor beschadigde status** of het aantal opeenvolgende mislukte tests dat moet optreden voordat wordt besloten dat een VM een beschadigde status heeft.|
    | | |
4. Selecteer **OK**.

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken
Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. 

U definieert het volgende:
 - Front-end-IP-configuratie voor het binnenkomende verkeer.
 - De back-end-IP-adres groep voor het ontvangen van het verkeer.
 - De vereiste bron-en doel poort. 

In het volgende gedeelte maakt u een:
 - De Load Balancer-regel **myhttprule als** voor het Luis teren naar poort 80.
 - Front- **LoadBalancerFrontEnd**.
 - Back- **myBackEndPool** maken ook gebruik van poort 80. 

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.
2. Selecteer onder **Instellingen** **Taakverdelingsregels** en selecteer vervolgens **Toevoegen**.
3. Gebruik deze waarden om de taakverdelings regel te configureren:
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myHTTPRule** in. |
    | Protocol | selecteer **TCP**. |
    | Poort | Voer **80** in.|
    | Back-endpoort | Voer **80** in. |
    | Back-end-pool | Selecteer **myBackendPool**.|
    | Statustest | Selecteer **myHealthProbe**. |
    | Impliciete uitgaande regels maken | Selecteer **Nee**. We maken in een later stadium uitgaande regels met behulp van een speciaal openbaar IP-adres. |
4. Laat de overige standaardwaarden staan en selecteer **OK**.

## <a name="create-outbound-rule-configuration"></a>Configuratie van de uitgaande regel maken
Met uitgaande regels van Load Balancer wordt de uitgaande SNAT voor virtuele machines in de back-end-pool geconfigureerd. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Een uitgaand openbaar IP-adres en een front-end maken

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer bij **instellingen**de optie **frontend IP-configuratie**en selecteer vervolgens **toevoegen**.

3. Gebruik deze waarden om de front-end-IP-configuratie voor uitgaand verkeer te configureren:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **LoadBalancerFrontEndOutbound**in. |
    | IP-versie | Selecteer **IPv4**. |
    | IP-type | Selecteer **IP-adres**.|
    | Openbaar IP-adres | Selecteer **Nieuw maken**. Voer in het **myPublicIPOutbound** **een openbaar IP-adres toevoegen**in.  Selecteer **OK**. |

4. Selecteer **Toevoegen**.

### <a name="create-an-outbound-backend-pool"></a>Een uitgaande back-end-pool maken

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Back-endpools** en selecteer vervolgens **Toevoegen**.

3. Op de pagina **een back-endserver toevoegen** typt u **myBackendPoolOutbound**, als naam voor uw back-end-pool en selecteert u vervolgens **toevoegen**.

### <a name="create-outbound-rule"></a>Uitgaande regel maken

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **instellingen**de optie **Uitgaande regels**en selecteer vervolgens **toevoegen**.

3. Gebruik deze waarden om de uitgaande regels te configureren:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myOutboundRule**in. |
    | Frontend-IP-adres | Selecteer **LoadBalancerFrontEndOutbound**. |
    | Time-out voor inactiviteit (minuten) | Verplaats de schuif regelaar naar * * 15 minuten.|
    | TCP opnieuw instellen | Selecteer **Ingeschakeld**.|
    | Back-end-pool | **MyBackendPoolOutbound** selecteren |
    | Poort toewijzing-toewijzing van > poort | Selecteer **hand matig aantal uitgaande poorten kiezen** |
    | Uitgaande poorten-> kiezen voor | **Poorten per instantie** selecteren |
    | Uitgaande poorten-> poorten per instantie | Voer **10.000**in. |

4. Selecteer **Toevoegen**.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep, de load balancer en alle gerelateerde resources, wanneer u deze niet meer nodig hebt. Selecteer de **myResourceGroupSLB** van de resource groep die de Load Balancer bevat en selecteer vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel:
 - U hebt een standaard load balancer gemaakt.
 - De regels voor binnenkomende en uitgaande Load Balancer-verkeer zijn geconfigureerd.
 - Er is een status test geconfigureerd voor de virtuele machines in de back-end-pool. 

Ga verder met de [zelf studies voor Azure Load Balancer voor](tutorial-load-balancer-standard-public-zone-redundant-portal.md)meer informatie.
