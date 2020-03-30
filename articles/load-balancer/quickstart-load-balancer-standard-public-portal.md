---
title: Snelstart:Een openbare load balancer maken - Azure-portal
titleSuffix: Azure Load Balancer
description: In deze quickstart ziet u hoe u een load balancer maakt met behulp van de Azure-portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: b424fe315737b84479283eed2d77398c8ce4f148
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78898832"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Snelstart: een load balancer maken om vm's met balans te laden met behulp van de Azure-portal

Taakverdeling zorgt voor een hogere beschikbaarheid en betere schaalbaarheid door binnenkomende aanvragen te spreiden over meerdere virtuele machines. U kunt in Azure Portal een load balancer maken om taken van virtuele machines (VM's) te verdelen. Deze quickstart laat zien hoe u vm's met balans laadt met behulp van een openbare load balancer.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="create-a-load-balancer"></a>Een load balancer maken

In deze sectie maakt u een load balancer waarmee virtuele machines in balans kunnen worden gebracht. U een openbare load balancer of een interne load balancer maken. Wanneer u een openbare load balancer maakt, moet u ook een nieuw openbaar IP-adres maken dat is geconfigureerd als frontend (standaard *loadbalancerfrontend* genoemd) voor de Load Balancer.

1. Selecteer linksboven in het scherm de optie **Een resource** > **Netwerklastbalancer****Networking** > maken .
2. Voer op het tabblad **Basis** van de pagina **Load balancer maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **Nieuw maken** en typ *myResourceGroupSLB* in het tekstvak.|
    | Name                   | *myLoadBalancer*                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | Select **Openbaar**.                                        |
    | SKU           | Selecteer **Standaard** of **Basic**. Microsoft raadt Standaard aan voor productieworkloads. |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. Als u een bestaand openbaar IP hebt dat u wilt gebruiken, selecteert u **Bestaande** |
    | Naam openbaar IP-adres              | Typ *myPublicIP* in het tekstvak.   Gebruiken ```-SKU Basic``` om een basisip-ip-adres te maken. Basis-IP's zijn niet compatibel met **standaard** load balancer. Microsoft raadt aan **om Standaard** te gebruiken voor productieworkloads.|
    | Beschikbaarheidszone | Typ *Zone-redundant* om een veerkrachtige load balancer te maken. Als u een zonale load balancer wilt maken, selecteert u een specifieke zone uit 1, 2 of 3 |

> [!IMPORTANT]
> De rest van deze quickstart gaat ervan uit dat **standaard** SKU is gekozen tijdens het sku-selectieproces hierboven.


3. Selecteer op het tabblad **Controleren + maken** de optie **Maken**.   

    ![Een Load Balancer van het type Standard maken](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Resources load balancer maken

In deze sectie configureert u de instellingen van load balancer voor een backend-adresgroep, een statussonde, en geeft u een balancerregel op.

### <a name="create-a-backend-pool"></a>Een backend-pool maken

Als u verkeer wilt distribueren naar de VM's, bevat een backend-adresgroep de IP-adressen van de virtuele (NIC's) die zijn verbonden met de Load Balancer. Maak de backend-adresgroep *myBackendPool* met virtuele machines voor het balanceren van internetverkeer.

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en selecteer **vervolgens myLoadBalancer** in de lijst resources.
2. Selecteer **onder Instellingen**de optie **Backend-groepen**en selecteer **Toevoegen**.
3. Typ *myBackendPool*op de pagina **Een backend pool** toevoegen voor naam als naam voor uw backendpool en selecteer **Vervolgens Toevoegen**.

### <a name="create-a-health-probe"></a>Een statustest maken

Als u wilt dat de load balancer de status van uw app kan controleren, gebruikt u een statussonde. De statussonde voegt dynamisch VM's toe of verwijdert vm's uit de rotatie van load balancer op basis van hun reactie op gezondheidscontroles. Maak een statustest (*myHealthProbe*) om de status van de VM's te bewaken.

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en selecteer **vervolgens myLoadBalancer** in de lijst resources.
2. Selecteer **onder Instellingen**de optie **Sondes van status**en selecteer **Toevoegen**.
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer *mijnHealthProbe in*. |
    | Protocol | Selecteer **HTTP**. |
    | Poort | Voer *80*in .|
    | Interval | Voer *15* in voor het aantal **interval in** seconden tussen sondepogingen. |
    | Ongezonde drempelwaarde | Selecteer **2** voor het aantal **ongezonde drempelwaarde** of opeenvolgende sondefouten die moeten optreden voordat een virtuele machine als ongezond wordt beschouwd.|
    | | |
4. Selecteer **OK**.

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken
Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Maak een Load Balancer-regel *myLoadBalancerRuleWeb* voor het luisteren naar poort 80 in de *frontend FrontendLoadBalancer* en het verzenden van load-balanced netwerkverkeer naar de backend-adrespool *myBackEndPool* ook met behulp van poort 80. 

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en selecteer **vervolgens myLoadBalancer** in de lijst resources.
2. Selecteer **onder Instellingen**de **taakverdelingsregels**laden en selecteer **Toevoegen**.
3. Gebruik deze waarden om de taakverdelingsregel te configureren:
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer *mijnhttpregel in*. |
    | Protocol | selecteer **TCP**. |
    | Poort | Voer *80*in .|
    | Backend-poort | Voer *80*in . |
    | Back-endpool | Selecteer *myBackendPool*.|
    | Statustest | Selecteer *myHealthProbe*. |
4. Laat de rest van de standaardinstellingen en selecteer **OK**.


## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie maakt u een virtueel netwerk, maakt u drie virtuele machines voor de backendpool van de Load Balancer en installeert u IIS op de virtuele machines om de Load Balancer te testen.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie moet u de volgende parameters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resourcegroepnaam>**  | myResourceGroupSLB |
| **\<>van de naam van het virtuele netwerk** | myVNet          |
| **\<regionaam>**          | Europa -west      |
| **\<IPv4-adresruimte>**   | 10.1.0.0\16          |
| **\<subnetnaam>**          | myBackendSubnet        |
| **\<subnet-adresbereik>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Virtuele machines maken
Openbare IP-SKU's en Load Balancer SKU's moeten overeenkomen. Gebruik voor Standard Load Balancer VM's met standaard IP-adressen in de backendpool. In deze sectie maakt u drie VM's *(myVM1,* *myVM2* en *myVM3)* met een standaard openbaar IP-adres in drie verschillende zones *(Zone 1,* *Zone 2*en *Zone 3)* die later worden toegevoegd aan de backendpool van de Load Balancer die eerder is gemaakt. Als u Basic hebt geselecteerd, gebruikt u VM's met basisIP-adressen.

1. Selecteer linksboven in de portal **de** > optie Een**resource Compute** > **Windows Server 2019-datacenter maken**. 
   
1. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:
   - **Subscription** > **Abonnementsbrongroep**: Selecteer **myResourceGroupSLB**.
   - **Instantie details** > **Virtuele machine naam:** Typ *myVM1*.
   - **Instantiedetails** > **regio** > **Selecteer West-Europa**.
   - **Instance Details** > **Beschikbaarheidsopties voor instantie** details > **Beschikbaarheidszones selecteren**. 
   - **Instantie details** > **Beschikbaarheidzone** > Selecteren **1**.
   - **Beheerdersaccount**> Voer de **wachtwoordgegevens gebruikersnaam,** **wachtwoord** en **bevestigen in.**
   - Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
1. Controleer op het tabblad **Netwerken** of de volgende opties zijn geselecteerd:
   - **Virtueel netwerk**: *myVnet*
   - **Subnet**: *myBackendSubnet*
   - **Openbare IP->** selecteer **Nieuw maken**en selecteer in het venster **Openbaar IP-adres maken** voor **SKU,** selecteer **Standaard**en voor **beschikbaarheidszone,** selecteer **Zone-redundant**en selecteer **vervolgens OK**. Als u een Basisbalans hebt gemaakt, selecteert u Basic. Microsoft raadt aan om Standaard SKU te gebruiken voor productieworkloads.
   - Als u een nieuwe netwerkbeveiligingsgroep (NSG) wilt maken, een soort firewall, selecteert u onder **Netwerkbeveiligingsgroep** de optie **Geavanceerd**. 
       1. Selecteer in het veld **Netwerkbeveiligingsgroep configureren** de optie **Nieuwe maken**. 
       1. Typ *myNetworkSecurityGroup*en selecteer **OK**.
   - Voer de volgende stappen uit om van de VM deel uit te maken van de backendpool van de Load Balancer:
        - In **Load Balancing**, voor **Plaats deze virtuele machine achter een bestaande load balancing oplossing?**, selecteer **Ja**.
        - Selecteer **Azure load balancer**voor **taakverdelingsopties voor taakverdelingsopties**. **Load balancing settings**
        - Voor **Selecteer een load balancer**, *myLoadBalancer*.
        - Selecteer het tabblad **Beheer** of selecteer **Volgende** > **Beheer**.
2. Stel op het tabblad **Beheer** onder **Controleren** **de diagnose Opstarten** in op **Uit**. 
1. Selecteer **Controleren + maken**.   
1. Controleer de instellingen en selecteer vervolgens **Maken**.
1. Volg de stappen 2 tot en met 6 om twee extra VM's te maken met de volgende waarden en alle andere instellingen hetzelfde als *myVM1:*

    | Instelling | VM 2| VM 3|
    | ------- | ----- |---|
    | Name |  *myVM2* |*myVM3*|
    | Beschikbaarheidszone | 2 |3|
    |Openbare IP| **Standaard** Sku|**Standaard** Sku|
    | Openbare IP - Beschikbaarheidszone| **Zone overbodig** |**Zone overbodig**|
    | Netwerkbeveiligingsgroep | Selecteer de bestaande *myNetworkSecurity-groep*| Selecteer de bestaande *myNetworkSecurity-groep*|

 ### <a name="create-nsg-rule"></a>Een NSG-regel maken

In deze sectie maakt u een regel voor netwerkbeveiligingom binnenkomende verbindingen toe te staan met HTTP.

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en selecteer vervolgens **myNetworkSecurityGroup** die zich in de brongroep **myResourceGroupSLB bevindt.**
2. Selecteer onder **Instellingen****Inkomende beveiligingsregels** en selecteer vervolgens **Toevoegen**.
3. Voer deze waarden in voor de inkomende beveiligingsregel met de naam *myHTTPRule* om een binnenkomende HTTP-verbinding via poort 80 toe te staan:
    - **Bron**: *Servicetag*
    -  **Bronservicetag**: *Internet*
    - **Bestemmingspoortbereik**: *80*
    - **Protocol**: *TCP*
    - **Actie**: *Toestaan*
    - **Prioriteit**: *100*
    - **Naam**: *mijnHTTPRule* 
    - **Beschrijving**: "*HTTP toestaan* 
4. Selecteer **Toevoegen**.
5. Herhaal indien nodig de stappen voor de binnenkomende RDP-regel met de volgende verschillende waarden:
   - **Poortbereiken van doel**: typ *3389*.
   - **Prioriteit**: typ *200*. 
   - **Naam**: typ *MyRDPRule*. 
   - **Beschrijving**: typ *RDP toestaan*. 
 
### <a name="install-iis"></a>IIS installeren

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en selecteer vervolgens **myVM1** die zich in de brongroep *myResourceGroupSLB* bevindt.
2. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** om extern verbinding te maken met de VM.
5. Meld u aan bij de virtuele machine met de referenties die u hebt opgegeven tijdens het maken van deze virtuele machine. Hiermee wordt een sessie met extern bureaublad met virtuele machine *myVM1* gestart.
6. Navigeer op het serverbureaublad naar **Windows-beheerprogramma's**>**Windows PowerShell**.
7. Voer in het venster PowerShell de volgende opdrachten uit om de IIS-server te installeren, het standaardbestand iisstart.htm te verwijderen en een nieuw bestand iisstart.htm toe te voegen dat de naam van de VM weergeeft:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Sluit de RDP-sessie met *myVM1*.
7. Herhaal stappen 1 tot en met 6 om IIS en het bijgewerkte bestand iisstart.htm te installeren op *myVM2* en *myVM3*.

## <a name="test-the-load-balancer"></a>Test de load balancer
1. Zoek op het scherm **Overzicht** het openbare IP-adres voor de load balancer. Selecteer **Alle services** in het linkermenu, selecteer Alle **bronnen**en selecteer **vervolgens myPublicIP**.

2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

   ![IIS-webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Als u wilt zien dat de load balancer verkeer over alle drie de VM's distribueert, u de standaardpagina van de IIS-webserver van elke VM aanpassen en vervolgens uw webbrowser van de clientmachine vernieuwen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, verwijdert u de resourcegroep, Load Balancer en alle gerelateerde resources. Selecteer hiervoor de resourcegroep (*myResourceGroupSLB*) die de load balancer bevat en selecteer **Vervolgens Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Standaard Load Balancer gemaakt, VM's aan gekoppeld, de verkeersregel Load Balancer geconfigureerd, statussonde en vervolgens de Load Balancer getest. Ga voor meer informatie over Azure Load Balancer verder naar [azure load balancer-zelfstudies](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Meer informatie over [de zones Load Balancer en Availability](load-balancer-standard-availability-zones.md).
