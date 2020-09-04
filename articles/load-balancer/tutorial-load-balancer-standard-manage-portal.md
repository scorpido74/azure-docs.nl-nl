---
title: 'Zelfstudie: Taakverdeling voor internetverkeer naar virtuele machines instellen - Azure Portal'
titleSuffix: Azure Load Balancer
description: In deze zelfstudie vindt u informatie over het maken en beheren van een Standard Load Balancer via Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 8961a50490bdbf8b456e87e1c00577c2c8afd050
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "80240367"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Zelfstudie: Taakverdeling voor internetverkeer naar virtuele machines instellen met behulp van de Azure-portal

Taakverdeling zorgt voor een hogere beschikbaarheid en betere schaalbaarheid door binnenkomende aanvragen te spreiden over meerdere virtuele machines. In deze zelfstudie leert u meer over de verschillende onderdelen van Azure Standard Load Balancer die internetverkeer verdelen naar VM's en zorgen voor hoge beschikbaarheid. In deze zelfstudie leert u procedures om het volgende te doen:


> [!div class="checklist"]
> * Een Azure Load Balancer maken
> * Resources voor de load balancer maken
> * Virtuele machines maken en IIS-server installeren
> * Een load balancer in actie zien
> * VM's toevoegen aan en verwijderen uit een load balancer

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Een Load Balancer van het type Standard maken

In dit gedeelte maakt u een Standard Load Balancer die helpt bij het verdelen van taken van virtuele machines. Standard Load Balancer biedt alleen ondersteuning voor een standaard, openbaar IP-adres. Als u een Standard-load balancer maakt, moet u ook een nieuw, standaard, openbaar IP-adres maken dat als de front-end (standaard *LoadBalancerFrontend* genoemd) wordt geconfigureerd voor de Standard-load balancer. 

1. Klik linksboven in het scherm op **Een resource maken** > **Netwerken** > **Load balancer**.
2. Voer op het tabblad **Basis** van de pagina **Load balancer maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **Nieuwe maken** en typ *myResourceGroupSLB* in het tekstvak.|
    | Naam                   | *myLoadBalancer*                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | Selecteer **Openbaar**.                                        |
    | SKU           | selecteer **Standaard**.                          |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. |
    | Naam openbaar IP-adres              | Typ *myPublicIP* in het tekstvak.   |
    |Beschikbaarheidszone| Selecteer **Zone-redundant**.    |

3. Klik op het tabblad **Beoordelen en maken** op **Maken**.

   ![Een Load Balancer van het type Standard maken](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Resources voor de load balancer maken

In deze sectie configureert u de instellingen van de load balancer voor een back-endadresgroep en een statustest en geeft u een regel voor de load balancer op.

### <a name="create-a-backend-address-pool"></a>Een back-endadresgroep maken

Om verkeer te distribueren naar de VM's bevat een back-endadresgroep de IP-adressen van de virtuele netwerkinterfacekaarten (NIC's) die zijn verbonden met de load balancer. Maak de back-endadres groep *myBackendPool* om virtuele machines voor het verdelen van internetverkeer in te bewaren.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Klik vervolgens op **myLoadBalancer** en in de lijst met resources.
2. Klik onder **Instellingen** op **Back-endpools** en vervolgens op **Toevoegen**.
3. Typ op de pagina **Back-endpool toevoegen** *myBackendPool* als naam voor de back-endpool. Selecteer vervolgens **Toevoegen**.

### <a name="create-a-health-probe"></a>Een statustest maken

U gebruikt een statustest om de load balancer de status van uw app te laten bewaken. De statustest voegt dynamisch VM's toe aan de load balancer-rotatie of verwijdert ze, op basis van hun reactie op statuscontroles. Maak een statustest (*myHealthProbe*) om de status van de VM's te bewaken.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Klik vervolgens op **myLoadBalancer** en in de lijst met resources.
2. Klik onder **Instellingen** op **Tests** en klik op **Toevoegen**.
3. Gebruik deze waarden om de statustest te maken:
     
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *myHealthProbe* in. |
    | Protocol | Selecteer **HTTP**. |
    | Poort | Voer *80* in.|
    | Interval | Voer *15* in als waarde voor het **Interval** in seconden tussen tests. |
    | Drempelwaarde voor beschadigde status | Selecteer *2* als het aantal voor de **Drempelwaarde voor beschadigde status** of het aantal opeenvolgende mislukte tests dat moet optreden voordat wordt besloten dat een VM een beschadigde status heeft.|
    
4. Selecteer **OK**.

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Maak de regel *myLoadBalancerRuleWeb* voor de load balancer voor het luisteren naar poort 80 in de front-end *FrontendLoadBalancer* en het verzenden van netwerkverkeer met gelijke taakverdeling naar de back-endadresgroep *myBackEndPool* waarbij ook van poort 80 gebruik wordt gemaakt.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Klik vervolgens op **myLoadBalancer** en in de lijst met resources.
2. Klik onder **Instellingen** op **Taakverdelingsregels** en vervolgens op **Toevoegen**.
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
| **\<resource-group-name>**  | myResourceGroupSLB (selecteer bestaande resourcegroep) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa -west      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Virtuele machines maken

Standard Load Balancer ondersteunt alleen VM's met standaard IP-adressen in de back-endpool. In dit gedeelte maakt u drie VM's (*myVM1*, *myVM2* en *myVM3*) met een standaard openbaar IP-adres in drie verschillende zones (*Zone 1*, *Zone 2*en *Zone 3*) die worden toegevoegd aan de back-endpool van de Standard Load Balancer die eerder is gemaakt.

1. Selecteer **Een resource maken** > **Compute** > **Windows Server 2016 Datacenter** linksboven in de portal. 
   
1. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:
   - **Abonnement** > **Resourcegroep**: Selecteer **myResourceGroupSLB**.
   - **Instantiedetails** > **Naam van virtuele machine**: Typ *myVM1*.
   - **Instantiedetails** > **Regio** > selecteer **Europa - west**.
   - **Instantiedetails** > **Beschikbaarheidsopties** > selecteer **Beschikbaarheidszones**. 
   - **Instantiedetails** > **Beschikbaarheidszone** > selecteert **1**.
  
1. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**. 
   
   - Zorg ervoor dat de volgende opties zijn geselecteerd:
       - **Virtueel netwerk**: **myVnet**
       - **Subnet**: **myBackendSubnet**
       - **Openbaar IP** > selecteer **Nieuwe maken** en selecteer in het venster **Openbaar IP-adres maken** bij **SKU** de optie **standard**, en selecteer bij **Beschikbaarheidszone** de optie **Zone-redundant**
      
   - Als u een nieuwe netwerkbeveiligingsgroep (NSG) wilt maken, een soort firewall, selecteert u onder **Netwerkbeveiligingsgroep** de optie **Geavanceerd**. 
       1. Selecteer in het veld **Netwerkbeveiligingsgroep configureren** de optie **Nieuwe maken**. 
       1. Typ *myNetworkSecurityGroup* en selecteer **OK**.

   - Voer de volgende stappen uit om de VM onderdeel te maken van de back-endpool van de load balancer:
        - Selecteer onder **Taakverdeling** bij **Wilt u deze virtuele machine achter een bestaande taakverdelingsoplossing plaatsen?** de optie **Ja**.
        - Selecteer in **Instellingen voor taakverdeling** bij **Opties voor taakverdeling** de optie **Azure Load Balancer**.
        - Bij **Een load balancer selecteren** kiest u *myLoadBalancer*. 
1. Selecteer het tabblad **Beheer** of selecteer **Volgende** > **Beheer**. Stel bij **Bewaking** **Diagnostische gegevens over opstarten** in op **Uit**. 
1. Selecteer **Controleren + maken**.   
1. Controleer de instellingen en selecteer vervolgens **Maken**.
1. Volg de stappen om twee aanvullende virtuele machines te maken - *myVM2* en *myVM3*, met een standaard SKU openbaar IP-adres in respectievelijk **Beschikbaarheidszone** **2** en **3** en alle andere instellingen hetzelfde als *myVM1*.  

### <a name="create-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

In deze sectie maakt u een NSG-regel (netwerkbeveiligingsgroep) om inkomende verbindingen via HTTP toe te staan.

1. Selecteer in het linkermenu **Alle services**, selecteer vervolgens **Alle resources** en klik daarna in de lijst met resources op **myNetworkSecurityGroup**, die zich in de resourcegroep **myResourceGroupSLB** bevindt.
2. Klik onder **Instellingen** op **Inkomende beveiligingsregels** en vervolgens op **Toevoegen**.
3. Voer deze waarden in voor de inkomende beveiligingsregel met de naam *myHTTPRule* om een binnenkomende HTTP-verbinding via poort 80 toe te staan:
    - *Service Tag* bij **Bron**.
    - *Internet* bij **Bronservicetag**
    - *80* bij **Poortbereiken van doel**
    - *TCP* bij **Protocol**
    - *Allow* bij **Actie**
    - *100* bij **Prioriteit**
    - *myHTTPRule* als naam
    - *Allow HTTP* als beschrijving
4. Selecteer **Toevoegen**.

### <a name="install-iis-on-vms"></a>IIS installeren op VM's

1. Selecteer in het linkermenu **Alle services**, selecteer vervolgens **Alle resources** en klik daarna in de lijst met resources op **myVM1**, die zich in de resourcegroep *myResourceGroupSLB* bevindt.
2. Klik op de pagina **Overzicht** op **Verbinding maken** om extern verbinding te maken met de VM.
3. Selecteer in het pop-upvenster **Verbinding maken met virtuele machine****RDP-bestand downloaden**, en open vervolgens het gedownloade RDP-bestand.
4. In het venster **Verbinding met extern bureaublad** klikt u op **Verbinden**.
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
1. Zoek op het scherm **Overzicht** het openbare IP-adres voor de load balancer. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Klik vervolgens op **myPublicIP**.

2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

      ![IIS-webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

U kunt het vernieuwen van de webbrowser forceren om te zien hoe het verkeer met behulp van de load balancer wordt verdeeld over de drie VM's waarop de app wordt uitgevoerd.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Virtuele machines toevoegen aan of verwijderen uit de back-endpool
Het is mogelijk dat u onderhoud moet uitvoeren op de VM's waarop uw app wordt uitgevoerd, zoals het installeren van besturingssysteemupdates. U moet mogelijk extra VM's toevoegen vanwege toegenomen verkeer naar uw app. In dit gedeelte wordt beschreven hoe u een VM (*myVM1*) aan de load balancer toevoegt of ervan verwijdert.

### <a name="remove-vm-from-a-backend-pool"></a>Virtuele machine uit een back-endpool verwijderen
Voer de volgende stappen uit om *myVM1* uit de back-endpool te verwijderen:

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Klik vervolgens op **myLoadBalancer** en in de lijst met resources.
2. Klik onder **Instellingen** op **Back-endpools**. Klik vervolgens in de lijst van de back-endpool op **myBackendPool**.
3. Selecteer op de pagina **myBackendPool**, om *VM1* te verwijderen, het verwijderpictogram aan het einde van de rij waarin *myVM1* wordt weergegeven en klik op **Opslaan**.

Nu *myVM1* niet meer in de back-endadresgroep zit, kunt u onderhoudstaken uitvoeren op *myVM1*, zoals het installeren van software-updates. Omdat *VM1* niet meer beschikbaar is, wordt de belasting verdeeld over *myVM2* en *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>VM aan een back-endpool toevoegen
Voer de volgende stappen uit om *myVM1* weer aan de back-endpool toe te voegen:

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myVM1** in de lijst met resources.
2. Selecteer op de pagina **VM1** onder **Instellingen** de optie **Netwerken**.
3. Selecteer op de pagina **Netwerken** het tabblad **Taakverdeling** en selecteer vervolgens **Taakverdeling toevoegen**.
4. Ga als volgt te werk op de pagina **Taakverdeling toevoegen**:
   1. Voor **Opties voor taakverdeling** selecteert u **Azure Load Balancer**.
   2. Voor **Een load balancer selecteren** selecteert u *myLoadBalancer*.
   3. Voor **Een back-endpool selecteren** selecteert u *MyBackendPool*. 

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep, de load balancer en alle gerelateerde resources, wanneer u deze niet meer nodig hebt. Als u dit wilt doen, selecteert u de resourcegroep *myResourceGroupSLB* die de load balancer bevat en selecteert u **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Standard Load Balancer gemaakt, hieraan VM's gekoppeld, een regel geconfigureerd voor verkeer van deze load balancer, een statustest gemaakt en de load balancer vervolgens getest. U hebt ook een VM verwijderd uit de set load balancers en deze weer toegevoegd aan de back-endadresgroep. Voor meer informatie over Azure Load Balancer gaat u verder met de zelfstudies voor Azure Load Balancer.

> [!div class="nextstepaction"]
> [Zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
