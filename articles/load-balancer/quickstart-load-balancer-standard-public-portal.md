---
title: 'Quickstart: een openbare load balancer maken - Azure-portal'
titleSuffix: Azure Load Balancer
description: In deze quickstart vindt u informatie over het maken van een load balancer via de Azure-portal.
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
ms.openlocfilehash: 99a2de4cd8a19d3f05b9dc37f3bcd08cd84b2e68
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052741"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Quickstart: Een load balancer maken om taken van VM's te verdelen via de Azure-portal

Taakverdeling zorgt voor een hogere beschikbaarheid en betere schaalbaarheid door binnenkomende aanvragen te spreiden over meerdere virtuele machines. U kunt in Azure Portal een load balancer maken om taken van virtuele machines (VM's) te verdelen. In deze quickstart wordt getoond hoe u taken van VM's kunt verdelen met een openbare load balancer.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Een load balancer maken

In deze sectie maakt u een load balancer die helpt bij het verdelen van taken van virtuele machines. U kunt een openbare load balancer of een interne load balancer maken. Wanneer u een openbare load balancer maakt, moet u ook een nieuw openbaar IP-adres maken dat als de front-end (standaard *LoadBalancerFrontend* genoemd) voor de load balancer wordt geconfigureerd.

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Load balancer**.
2. Voer op het tabblad **Basis** van de pagina **Load balancer maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **Nieuwe maken** en typ *myResourceGroupSLB* in het tekstvak.|
    | Naam                   | *myLoadBalancer*                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | Selecteer **Openbaar**.                                        |
    | SKU           | Selecteer **Standard** of **Basic**. Microsoft adviseert Standard voor productiewerkbelastingen. |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. Als u een bestaand openbaar IP-adres hebt dat u wilt gebruiken, selecteert u **Bestaande gebruiken** |
    | Naam openbaar IP-adres              | Typ *myPublicIP* in het tekstvak.   Gebruik ```-SKU Basic``` om een openbare basis-IP te maken. Openbare basis-IP's zijn niet compatibel met een **Standard-** load balancer. Microsoft raadt aan om **Standard** te gebruiken voor productiewerkbelastingen.|
    | Beschikbaarheidszone | Typ *Zone-redundant* om een tolerante load balancer te maken. Als u een zonegebonden load balancer wilt maken, selecteert u een specifieke zone uit 1, 2 of 3 |

> [!IMPORTANT]
> In de rest van deze quickstart wordt ervan uitgegaan dat de **Standard** SKU wordt gekozen tijdens het bovenstaande SKU-selectieproces.


3. Selecteer in het deelvenster **Beoordelen en maken** de optie **Maken**.   

    ![Een Load Balancer van het type Standard maken](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Resources voor de load balancer maken

In deze sectie configureert u de instellingen van de load balancer voor een back-endadresgroep en een statustest en geeft u een regel voor de load balancer op.

### <a name="create-a-backend-pool"></a>Een back-endpool maken

Om verkeer te distribueren naar de VM's bevat een back-endadresgroep de IP-adressen van de virtuele netwerkinterfacekaarten (NIC's) die zijn verbonden met de load balancer. Maak de back-endadres groep *myBackendPool* om virtuele machines voor het verdelen van internetverkeer in te bewaren.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.
2. Selecteer onder **Instellingen** de optie **Back-endpools** en selecteer vervolgens **Toevoegen**.
3. Typ op de pagina **Back-endpool toevoegen** *myBackendPool* als naam voor de back-endpool. Selecteer vervolgens **Toevoegen**.

### <a name="create-a-health-probe"></a>Een statustest maken

U gebruikt een statustest om de load balancer de status van uw app te laten bewaken. De statustest voegt dynamisch VM's toe aan de load balancer-rotatie of verwijdert ze, op basis van hun reactie op statuscontroles. Maak een statustest (*myHealthProbe*) om de status van de VM's te bewaken.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.
2. Selecteer onder **Instellingen** de optie **Statustests** en selecteer vervolgens **Toevoegen**.
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *myHealthProbe* in. |
    | Protocol | Selecteer **HTTP**. |
    | Poort | Voer *80* in.|
    | Interval | Voer *15* in als waarde voor het **Interval** in seconden tussen tests. |
    | Drempelwaarde voor beschadigde status | Selecteer **2** als het aantal voor de **Drempelwaarde voor beschadigde status** of het aantal opeenvolgende mislukte tests dat moet optreden voordat wordt besloten dat een VM een beschadigde status heeft.|
    | | |
4. Selecteer **OK**.

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken
Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Maak de regel *myLoadBalancerRuleWeb* voor de load balancer voor het luisteren naar poort 80 in de front-end *FrontendLoadBalancer* en het verzenden van netwerkverkeer met gelijke taakverdeling naar de back-endadresgroep *myBackEndPool* waarbij ook van poort 80 gebruik wordt gemaakt. 

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.
2. Selecteer onder **Instellingen** **Taakverdelingsregels** en selecteer vervolgens **Toevoegen**.
3. Gebruik deze waarden om de taakverdelingsregel te configureren:
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *myHTTPRule* in. |
    | Protocol | selecteer **TCP**. |
    | Poort | Voer *80* in.|
    | Back-endpoort | Voer *80* in. |
    | Back-end-pool | Selecteer *myBackendPool*.|
    | Statustest | Selecteer *myHealthProbe*. |
4. Laat de overige standaardwaarden staan en selecteer **OK**.


## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie maakt u een virtueel netwerk en drie virtuele machines voor de back-endpool van de load balancer en installeert u IIS op de virtuele machines om de load balancer te testen.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie moet u de volgende parameters in de stappen vervangen door onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupSLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa -west      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Virtuele machines maken
SKU's voor openbare IP-adressen en SKU's voor load balancers moeten overeenkomen. Gebruik voor Standard Load Balancer VM's met Standard IP-adressen in de back-endpool. In deze sectie maakt u drie VM's (*myVM1*, *myVM2* en *myVM3*) met een Standard openbaar IP-adres in drie verschillende zones (*Zone 1*, *Zone 2*en *Zone 3*) die later worden toegevoegd aan de back-endpool van de load balancer die eerder is gemaakt. Als u Basic hebt geselecteerd, gebruikt u VM's met Basic IP-adressen.

1. Selecteer **Een resource maken** > **Compute** > **Windows Server 2019 Datacenter** linksboven in de portal. 
   
1. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:
   - **Abonnement** > **Resourcegroep**: Selecteer **myResourceGroupSLB**.
   - **Instantiedetails** > **Naam van virtuele machine**: Typ *myVM1*.
   - **Instantiedetails** > **Regio** > selecteer **Europa - west**.
   - **Instantiedetails** > **Beschikbaarheidsopties** > selecteer **Beschikbaarheidszones**. 
   - **Instantiedetails** > **Beschikbaarheidszone** > selecteert **1**.
   - **Beheerdersaccount** > voer de gegevens voor **Gebruikersnaam**, **Wachtwoord** en **Wachtwoord bevestigen** in.
   - Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
1. Zorg ervoor dat de volgende opties zijn geselecteerd op het tabblad **Netwerken**:
   - **Virtueel netwerk**: *myVnet*
   - **Subnet**: *myBackendSubnet*
   - **Openbaar IP** > selecteer **Nieuwe maken** en selecteer in het venster **Openbaar IP-adres maken** bij **SKU** de optie **standard**, en selecteer bij **Beschikbaarheidszone** de optie **Zone-redundant** en vervolgens **OK**. Als u een Basic load balancer hebt gemaakt, selecteert u Basic. Microsoft raadt aan om Standard SKU te gebruiken voor productiewerkbelastingen.
   - Als u een nieuwe netwerkbeveiligingsgroep (NSG) wilt maken, een soort firewall, selecteert u onder **Netwerkbeveiligingsgroep** de optie **Geavanceerd**. 
       1. Selecteer in het veld **Netwerkbeveiligingsgroep configureren** de optie **Nieuwe maken**. 
       1. Typ *myNetworkSecurityGroup* en selecteer **OK**.
   - Voer de volgende stappen uit om de VM onderdeel te maken van de back-endpool van de load balancer:
        - Selecteer onder **Taakverdeling** bij **Wilt u deze virtuele machine achter een bestaande taakverdelingsoplossing plaatsen?** de optie **Ja**.
        - Selecteer in **Instellingen voor taakverdeling** bij **Opties voor taakverdeling** de optie **Azure Load Balancer**.
        - Bij **Een load balancer selecteren** kiest u *myLoadBalancer*.
        - Selecteer het tabblad **Beheer** of selecteer **Volgende** > **Beheer**.
2. Op het tabblad **Beheer** onder **Bewaking** stelt u **Diagnostische gegevens over opstarten** in op **Uit**. 
1. Selecteer **Controleren + maken**.   
1. Controleer de instellingen en selecteer vervolgens **Maken**.
1. Volg de stappen 2 tot en met 6 om twee extra VM's te maken met de volgende waarden en alle andere instellingen hetzelfde als *myVM1*:

    | Instelling | VM 2| VM 3|
    | ------- | ----- |---|
    | Naam |  *myVM2* |*myVM3*|
    | Beschikbaarheidszone | 2 |3|
    |Openbare IP| **Standard** SKU|**Standard** SKU|
    | Openbaar IP - beschikbaarheidszone| **Zone-redundant** |**Zone-redundant**|
    | Netwerkbeveiligingsgroep | Selecteer de bestaande *myNetworkSecurityGroup*| Selecteer de bestaande *myNetworkSecurityGroup*|

 ### <a name="create-nsg-rule"></a>Een NSG-regel maken

In deze sectie maakt u een NSG-regel (netwerkbeveiligingsgroep) om inkomende verbindingen via HTTP toe te staan.

1. Selecteer in het linkermenu **Alle services**, selecteer vervolgens **Alle resources** en selecteer daarna in de lijst met resources **myNetworkSecurityGroup**, die zich in de resourcegroep **myResourceGroupSLB** bevindt.
2. Selecteer onder **Instellingen** **Inkomende beveiligingsregels** en selecteer vervolgens **Toevoegen**.
3. Voer deze waarden in voor de inkomende beveiligingsregel met de naam *myHTTPRule* om een binnenkomende HTTP-verbinding via poort 80 toe te staan:
    - **Bron**: *Servicetag*
    -  **Bronservicetag**: *Internet*
    - **Poortbereiken van doel**: *80*
    - **Protocol**: *TCP*
    - **Actie**: *Toestaan*
    - **Prioriteit**: *100*
    - **Naam**: *myHTTPRule* 
    - **Beschrijving**: *HTTP toestaan* 
4. Selecteer **Toevoegen**.
5. Herhaal de stappen voor de inkomende RDP-regel, indien nodig, met de volgende, afwijkende waarden:
   - **Poortbereiken van doel**: typ *3389*.
   - **Prioriteit**: typ *200*. 
   - **Naam**: typ *MyRDPRule*. 
   - **Beschrijving**: typ *RDP toestaan*. 
 
### <a name="install-iis"></a>IIS installeren

1. Selecteer in het linkermenu **Alle services**, selecteer vervolgens **Alle resources** en selecteer daarna in de lijst met resources **myVM1**, die zich in de resourcegroep *myResourceGroupSLB* bevindt.
2. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** om extern verbinding te maken met de VM.
5. Meld u aan bij de virtuele machine met de referenties die u hebt opgegeven tijdens het maken van deze virtuele machine. Hiermee wordt een sessie met extern bureaublad met virtuele machine *myVM1* gestart.
6. Ga op de serverdesktop naar **Windows Systeembeheer**>**Windows Powershell**.
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

## <a name="test-the-load-balancer"></a>Load balancer testen
1. Zoek op het scherm **Overzicht** het openbare IP-adres voor de load balancer. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myPublicIP**.

2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

   ![IIS-webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

U kunt de standaardpagina van de IIS-webserver van elke virtuele machine aanpassen en vervolgens uw webbrowser geforceerd vernieuwen vanaf de clientcomputer om te zien hoe de load balancer verkeer verdeelt over alle drie de virtuele machines.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep, de load balancer en alle gerelateerde resources, wanneer u deze niet meer nodig hebt. Als u dit wilt doen, selecteert u de resourcegroep (*myResourceGroupSLB*) die de load balancer bevat en selecteert u **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Standard load balancer gemaakt, VM's daaraan gekoppeld, een regel voor het load balancer-verkeer geconfigureerd, een statustest gemaakt en vervolgens de load balancer getest. Voor meer informatie over Azure Load Balancer gaat u verder met [Zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Meer informatie over [Load Balancer en beschikbaarheidszones](load-balancer-standard-availability-zones.md).
