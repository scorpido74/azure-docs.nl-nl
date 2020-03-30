---
title: Snelstart - Een Private Link-service maken met behulp van de Azure-portal
titlesuffix: Azure Private Link
description: Meer informatie over het maken van een Private Link-service met behulp van de Azure-portal in deze quickstart
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: f21b440ee0e2c53d9824300e85b683629c1575da
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78252547"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Snelstart: een Private Link-service maken met behulp van de Azure-portal

Een Azure Private Link-service verwijst naar uw eigen service die wordt beheerd door Private Link. U Private Link toegang geven tot de service of resource die achter Azure Standard Load Balancer werkt. Consumenten van uw dienst hebben er privé toegang toe via hun eigen virtuele netwerken. In deze quickstart leert u hoe u een Private Link-service maakt met behulp van de Azure-portal.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Een interne load balancer maken

Maak eerst een virtueel netwerk. Maak vervolgens een interne load balancer die u gebruiken met de Private Link-service.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie gaat u een virtueel netwerk maken. U maakt ook het subnet om de load balancer te hosten die toegang heeft tot uw Private Link-service.

In deze sectie moet u de volgende parameters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resourcegroepnaam>**  | myResourceGroupLB |
| **\<>van de naam van het virtuele netwerk** | myVNet          |
| **\<regionaam>**          | VS - oost 2      |
| **\<IPv4-adresruimte>**   | 10.3.0.0\16          |
| **\<subnetnaam>**          | myBackendSubnet        |
| **\<subnet-adresbereik>** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Een standaardversie van een load balancer maken

Gebruik de portal om een standaard interne load balancer te maken. De naam en het IP-adres dat u opgeeft, worden automatisch geconfigureerd als de front-end van de load balancer.

1. Selecteer linksboven in de portal **de** > optie Een**resource NetwerkloadBalancer****Networking** > maken .

1. Voer op het tabblad **Basisbeginselen** van de pagina **Laadbalansr maken** de volgende gegevens in of selecteer deze:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | **Abonnement**               | Selecteer uw abonnement.    |
    | **Resourcegroep**         | Selecteer **myResourceGroupLB** in het vak.|
    | **Naam**                   | Voer **mijnLoadBalancer in**.                                   |
    | **Regio**         | Selecteer **VS - oost 2**.                                        |
    | **Type**          | selecteer **Intern**.                                        |
    | **Sku**           | Selecteer **Standaard**.                          |
    | **Virtueel netwerk**           | Selecteer **myVNet**.                          |
    | **Toewijzing ip-adres**              | Selecteer **Statisch**.   |
    | **Privé IP-adres**|Voer een adres in dat zich in de adresruimte van uw virtuele netwerk en subnet bevindt. Een voorbeeld is 10.3.0.7.  |

1. Accepteer de standaardinstellingen voor de overige instellingen en selecteer **Controleren + maken**

1. Selecteer op het tabblad **Controleren + maken** de optie **Maken**.

### <a name="create-standard-load-balancer-resources"></a>Standaardresources voor laadbalansresources maken

In deze sectie configureert u de instellingen voor de load balancer voor een back-endadresgroep en een statustest. U geeft ook regels voor load balancer op.

#### <a name="create-a-back-end-pool"></a>Een back-endpool maken

Een back-end adresgroep bevat de IP-adressen van de virtuele NIC's die zijn aangesloten op de load balancer. Met deze groep u verkeer distribueren naar uw resources. Maak de back-end adresgroep met de naam **myBackendPool** met middelen die het saldoverkeer laden.

1. Selecteer **Alle services** in het meest linkse menu.
1. Selecteer **Alle resources**en selecteer **myLoadBalancer** in de lijst met resources.
1. Selecteer onder **Instellingen** de optie **Back-endpools** en selecteer vervolgens **Toevoegen**.
1. Voer op de pagina **Een backendpool** toevoegen **in** als naam voor uw back-endpool en selecteer **Vervolgens Toevoegen**.

#### <a name="create-a-health-probe"></a>Een statustest maken

Gebruik een statussenvan de status van de load balancer. Op basis van de reactie van resources op statuscontroles voegt de statussonde dynamisch resources toe of verwijdert deze uit de rotatie van de load balancer.

Ga als volgt te werk om een statusssonde te maken om de status van de resources te controleren:

1. Selecteer **Alle bronnen** in het meest linkse menu en selecteer **myLoadBalancer** in de bronlijst.

1. Selecteer onder **Instellingen** de optie **Statustests** en selecteer vervolgens **Toevoegen**.

1. Voer op de pagina **Een statussonde** toevoegen de volgende waarden in of selecteer deze:

   - **Naam:** Voer **myHealthProbe in**.
   - **Protocol**: selecteer **TCP**.
   - **Poort**: Voer **80**in .
   - **Interval**: Voer **15**in . Deze waarde is het aantal seconden tussen sondepogingen.
   - **Ongezonde drempelwaarde**: Voer **2**in . Deze waarde is het aantal opeenvolgende sondefouten dat optreedt voordat een virtuele machine als ongezond wordt beschouwd.

1. Selecteer **OK**.

#### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een regel voor load balancer bepaalt hoe verkeer wordt gedistribueerd naar resources. De regel definieert:

- De front-end IP-configuratie voor binnenkomend verkeer.
- De back-end IP-pool om het verkeer te ontvangen.
- De vereiste bron- en bestemmingspoorten.

De regel van de load balancer met de naam **myLoadBalancerRule** luistert naar poort 80 in de **LoadBalancerFrontEnd** front-end. The rule sends network traffic to the **myBackendPool** back-end address pool on the same port 80.

Ga als lid van het werk om een regel voor load balancer te maken:

1. Selecteer **Alle bronnen** in het meest linkse menu en selecteer **myLoadBalancer** in de bronlijst.

1. Selecteer **onder Instellingen**de optie Regels voor het **balanceren**van de belasting en selecteer Vervolgens **Toevoegen**.

1. Voer op de pagina **Regel voor taakverdeling toevoegen** de volgende waarden in of selecteer deze als deze nog niet aanwezig zijn:

   - **Naam:** Voer **myLoadBalancerRule in**.
   - **Frontend IP-adres:** Voer **LoadBalancerFrontEnd in**.
   - **Protocol**: selecteer **TCP**.
   - **Poort**: Voer **80**in .
   - **Backend-poort**: Voer **80**in .
   - **Backend pool**: Selecteer **myBackendPool**.
   - **Gezondheidssonde**: Selecteer **myHealthProbe**. 

1. Selecteer **OK**.

## <a name="create-a-private-link-service"></a>Een Private Link-service maken

In deze sectie maakt u een Private Link-service achter een standaard load balancer.

1. Selecteer linksboven op de pagina in de Azure-portal > de optie Een > **bronnetwerkclientsprivatekoppelingscentrum****(Voorbeeld)** **maken**. U ook het zoekvak van de portal gebruiken om te zoeken naar Privékoppeling.

1. In **Private Link Center - Overzicht** > **Stel uw eigen service bloot zodat anderen verbinding kunnen maken,** selecteer **Start**.

1. Voer deze informatie in of selecteer deze informatie onder **Een privékoppelingsservice maken - Basisbeginselen:**

    | Instelling           | Waarde                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Projectdetails:  |                                                                              |
    | **Abonnement**      | Selecteer uw abonnement.                                                     |
    | **Resourcegroep**    | Selecteer **myResourceGroupLB**.                                                    |
    | Bijzonderheden uit de instantie: |                                                                              |
    | **Naam**              | Voer **myPrivateLinkService in**. |
    | **Regio**            | Selecteer **VS - oost 2**.                                                        |

1. Selecteer **Volgende: Uitgaande instellingen**.

1. Voer deze informatie in of selecteer deze informatie onder **Een privékoppelingsservice maken - Uitgaande instellingen:**

    | Instelling                           | Waarde                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Load balancer**                     | Selecteer **myLoadBalancer**.                                                           |
    | **IP-adres load Balancer frontend** | Selecteer het front-end IP-adres van **myLoadBalancer**.                                |
    | **Bron NAT Virtueel netwerk**        | Selecteer **myVNet**.                                                                   |
    | **Bron NAT-subnet**                 | Selecteer **myBackendSubnet**.                                                          |
    | **TCP-proxy v2 inschakelen**               | Selecteer **JA** of **NEE,** afhankelijk van of uw toepassing een TCP-proxy v2-header verwacht. |
    | **Instellingen voor privé-IP-adres**       | Configureer de toewijzingsmethode en het IP-adres voor elk NAT-IP.Configure erin the allocation method and IP address for each NAT IP. Configureer the allocation method and IP                  |

1. Selecteer **Volgende: Toegangsbeveiliging**.

1. Selecteer onder **Een privékoppelingsservice maken - Toegang tot beveiliging**, selecteer **Zichtbaarheid**en kies alleen op rollen **gebaseerd toegangsbeheer**.
  
1. Selecteer **Volgende: Tags** > **Review + maak** of kies het tabblad Controleren + **maken** boven aan de pagina.

1. Bekijk uw gegevens en selecteer **Maken**.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het gebruik van de privékoppelingsservice, verwijdert u de brongroep om de resources op te schonen die in deze snelstart worden gebruikt.

1. Typ **myResourceGroupLB** in het zoekvak boven aan de portal en selecteer **myResourceGroupLB** in de zoekresultaten.
1. Selecteer **Resourcegroep verwijderen**.
1. Voer **myResourceGroup**in **in DE NAAM VAN DE RESOURCEGROEP**.
1. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een interne Azure-load balancer en een Private Link-service gemaakt. U ook leren hoe [u een privéeindpunt maakt met behulp van de Azure-portal.](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
