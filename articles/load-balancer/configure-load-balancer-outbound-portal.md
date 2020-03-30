---
title: Load balancing en outbound rules configureren met behulp van de Azure-portal
titleSuffix: Azure Load Balancer
description: In dit artikel ziet u hoe u load balancing en outbound rules configureert in Standard Load Balancer met behulp van de Azure-portal.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b75f49155991bfc71f788ad88f166c0bec281841
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590011"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Load balancing en outbound rules configureren in Standard Load Balancer met behulp van de Azure portal

In dit artikel ziet u hoe u uitgaande regels configureert in Standard Load Balancer met behulp van de Azure-portal.  

De resource load balancer bevat twee front-ends en de bijbehorende regels. U hebt één front-end voor binnenkomend verkeer en een andere front-end voor uitgaand verkeer.  

Elke front-end verwijst naar een openbaar IP-adres. In dit scenario verschilt het openbare IP-adres voor binnenkomend verkeer van het adres voor uitgaand verkeer.   De regel voor het balanceren van de regel biedt alleen inkomende taakverdeling. De regel voor uitgaande geldt voor de uitgaande netwerkadresvertaling (NAT) voor de VM.  

Het scenario maakt gebruik van twee back-endpools: een voor binnenkomend verkeer en een voor uitgaand verkeer. Deze pools illustreren de mogelijkheden en bieden flexibiliteit voor het scenario.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="create-a-load-balancer"></a>Een load balancer maken

In deze sectie maakt u een load balancer die virtuele machines met balans laadt. U een public load balancer of een interne load balancer maken. Wanneer u een openbare load balancer maakt, maakt u een nieuw OPENBAAR IP-adres dat is geconfigureerd als frontend voor de load balancer. De frontend krijgt standaard de naam **LoadBalancerFrontEnd.**

1. Selecteer linksboven in het scherm de optie **Een resource** > **Netwerklastbalancer****Networking** > maken .
2. Voer op het tabblad **Basisbeginselen** van de pagina **Lastenbalans maken** de volgende gegevens in of selecteer deze:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **Nieuw maken** en typ **myResourceGroupSLB** in het tekstvak.|
    | Name                   | **myLoadBalancer**                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | Select **Openbaar**.                                        |
    | SKU           | Selecteer **Standaard** of **Basic**. Microsoft raadt Standaard aan voor productieworkloads. |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. Als u een bestaand openbaar IP-ip hebt dat u wilt gebruiken, selecteert u **Bestaande gebruiken**.  Bestaande openbare IP moet **standaard** SKU zijn.  Basisip-IP's zijn niet compatibel met **standaard** SKU-load balancer.  |
    | Naam openbaar IP-adres              | Typ **myPublicIP** in het tekstvak.|
    | Beschikbaarheidszone | Selecteer **Zoneredundant** om een veerkrachtige load balancer te maken. Als u een zonale load balancer wilt maken, selecteert u een specifieke zone uit 1, 2 of 3 |

3. Accepteer de standaardinstellingen voor de rest van de configuratie.
4. Selecteer **Controleren + maken**

    > [!IMPORTANT]
    > De rest van deze quickstart gaat ervan uit dat **standaard** SKU is gekozen tijdens het sku-selectieproces hierboven.

5. Selecteer op het tabblad **Controleren + maken** de optie **Maken**.   

    ![Een Load Balancer van het type Standard maken](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Resources voor load balancer maken

In deze sectie configureert u de instellingen van de load balancer voor een back-endadresgroep en een statustest en geeft u een regel voor de load balancer op.

### <a name="create-a-backend-pool"></a>Een back-endpool maken

Een backend-adresgroep bevat de IP-adressen van de virtuele NIC's in de backendpool. Maak de backend-adresgroep **myBackendPool** met virtuele machines voor het balanceren van internetverkeer.

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en selecteer **vervolgens myLoadBalancer** in de lijst resources.
2. Selecteer **onder Instellingen**de optie **Backend-groepen**en selecteer **Toevoegen**.
3. Typ **myBackendPool**op de pagina **Een backend pool** toevoegen voor naam als naam voor uw backendpool en selecteer **Vervolgens Toevoegen**.

### <a name="create-a-health-probe"></a>Een statustest maken

Een statussonde wordt gebruikt om de status van uw app te controleren. De statussonde voegt VM's uit de load balancer toe of verwijdert deze op basis van hun reactie op gezondheidscontroles. Maak een statustest (**myHealthProbe**) om de status van de VM's te bewaken.

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en selecteer **vervolgens myLoadBalancer** in de lijst resources.
2. Selecteer **onder Instellingen**de optie **Sondes van status**en selecteer **Toevoegen**.
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer **mijnHealthProbe in**. |
    | Protocol | Selecteer **HTTP**. |
    | Poort | Voer **80**in .|
    | Interval | Voer **15** in voor het aantal **interval in** seconden tussen sondepogingen. |
    | Ongezonde drempelwaarde | Selecteer **2** voor het aantal **ongezonde drempelwaarde** of opeenvolgende sondefouten die moeten optreden voordat een virtuele machine als ongezond wordt beschouwd.|
    | | |
4. Selecteer **OK**.

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken
Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. 

U definieert de:
 - Frontend IP-configuratie voor het binnenkomende verkeer.
 - De backend IP-pool om het verkeer te ontvangen.
 - De vereiste bron- en bestemmingshaven. 

In de volgende sectie maakt u een:
 - Load balancer regel **mijnHTTPRule** voor het luisteren naar poort 80.
 - Frontend **LoadBalancerFrontEnd**.
 - Backend adres pool **myBackEndPool** ook met behulp van poort 80. 

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en selecteer **vervolgens myLoadBalancer** in de lijst resources.
2. Selecteer **onder Instellingen**de **taakverdelingsregels**laden en selecteer **Toevoegen**.
3. Gebruik deze waarden om de regel voor het balanceren van de lastentaak te configureren:
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer **mijnhttpregel in**. |
    | Protocol | selecteer **TCP**. |
    | Poort | Voer **80**in .|
    | Backend-poort | Voer **80**in . |
    | Back-endpool | Selecteer **myBackendPool**.|
    | Statustest | Selecteer **myHealthProbe**. |
    | Impliciete uitgaande regels maken | Selecteer **Nee**. We maken uitgaande regels in een later gedeelte met behulp van een speciaal openbaar IP.We'll create outbound rules in a later section using a dedicated public IP. |
4. Laat de rest van de standaardinstellingen en selecteer **OK**.

## <a name="create-outbound-rule-configuration"></a>Uitgaande regelconfiguratie maken
Uitgaande regels voor load balancer configureren uitgaande SNAT voor VM's in de backendpool. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Een uitgaand openbaar IP-adres en frontend maken

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en selecteer **vervolgens myLoadBalancer** in de lijst resources.

2. Selecteer **onder Instellingen**de **IP-configuratie van Frontend**en selecteer **Toevoegen**.

3. Gebruik deze waarden om de IP-configuratie aan de frontend te configureren voor uitgaande:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer **LoadBalancerFrontEndOutbound in**. |
    | IP-versie | Selecteer **IPv4**. |
    | IP-type | Selecteer **IP-adres**.|
    | Openbaar IP-adres | Selecteer **Nieuw maken**. Voer **myPublicIPOutbound**in in het **IP-adres Toevoegen**.  Selecteer **OK**. |

4. Selecteer **Toevoegen**.

### <a name="create-an-outbound-backend-pool"></a>Een uitgaande backendpool maken

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en selecteer **vervolgens myLoadBalancer** in de lijst resources.

2. Selecteer **onder Instellingen**de optie **Backend-groepen**en selecteer **Toevoegen**.

3. Typ **myBackendPoolOutbound**op de pagina **Een backendpool** toevoegen voor naam als naam voor uw backendpool en selecteer **Vervolgens Toevoegen**.

### <a name="create-outbound-rule"></a>Uitgaande regel maken

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en selecteer **vervolgens myLoadBalancer** in de lijst resources.

2. Selecteer **Onder Instellingen** **Uitgaande regels**en selecteer Vervolgens **Toevoegen**.

3. Gebruik deze waarden om de uitgaande regels te configureren:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer **mijnOutboundrule in**. |
    | Frontend IP-adres | Selecteer **LoadBalancerFrontEndOutbound**. |
    | Niet-actieve time-out (minuten) | Schuifverplaatsen naar **15 minuten.|
    | TCP-reset | Selecteer **Ingeschakeld**.|
    | Back-endpool | **MyBackendPoolOutbound selecteren** |
    | Haventoewijzing -> Toewijzing van poorten | Handmatig het **aantal uitgaande poorten selecteren** |
    | Uitgaande poorten -> kiezen op | **Poorten per instantie selecteren** |
    | Uitgaande poorten -> poorten per instantie | Voer **10.000**in . |

4. Selecteer **Toevoegen**.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep, de load balancer en alle gerelateerde resources, wanneer u deze niet meer nodig hebt. Selecteer de resourcegroep **myResourceGroupSLB** die de load balancer bevat en selecteer **Vervolgens Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel:
 - U hebt een standaard load balancer gemaakt.
 - Geconfigureerd voor zowel inkomende als uitgaande regels voor load-balancer.
 - Een statussonde geconfigureerd voor de VM's in de back-endpool. 

Ga voor meer informatie verder naar de [zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
