---
title: "Zelfstudie: Internetverkeer voor de balans laden naar VM's - Azure-portal"
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
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80240367"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Zelfstudie: Taakverdeling voor internetverkeer naar virtuele machines instellen met behulp van de Azure-portal

Taakverdeling zorgt voor een hogere beschikbaarheid en betere schaalbaarheid door binnenkomende aanvragen te spreiden over meerdere virtuele machines. In deze zelfstudie leert u meer over de verschillende onderdelen van Azure Standard Load Balancer die internetverkeer verdelen naar VM's en zorgen voor hoge beschikbaarheid. Procedures voor:


> [!div class="checklist"]
> * Een Azure Load Balancer maken
> * Resources load balancer maken
> * Virtuele machines maken en IIS-server installeren
> * Load Balancer in actie weergeven
> * VM's toevoegen en verwijderen uit een load balancer

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint. 

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="create-a-standard-load-balancer"></a>Een Load Balancer van het type Standard maken

In deze sectie maakt u een Standaard Load Balancer die helpt bij het in balans brengen van virtuele machines. Standard Load Balancer biedt alleen ondersteuning voor een standaard, openbaar IP-adres. Als u een Standard-load balancer maakt, moet u ook een nieuw, standaard, openbaar IP-adres maken dat als de front-end (standaard *LoadBalancerFrontend* genoemd) wordt geconfigureerd voor de Standard-load balancer. 

1. Klik linksboven in het scherm op **Een resource** > **Netwerklastbalancer****Networking** > maken .
2. Voer op het tabblad **Basis** van de pagina **Load balancer maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **Nieuw maken** en typ *myResourceGroupSLB* in het tekstvak.|
    | Name                   | *myLoadBalancer*                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | Select **Openbaar**.                                        |
    | SKU           | Selecteer **Standaard**.                          |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. |
    | Naam openbaar IP-adres              | Typ *myPublicIP* in het tekstvak.   |
    |Beschikbaarheidszone| Selecteer **Zone-redundant**.    |

3. Klik op het tabblad **Beoordelen en maken** op **Maken**.

   ![Een Load Balancer van het type Standard maken](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Resources load balancer maken

In deze sectie configureert u de instellingen van load balancer voor een backend-adresgroep, een statussonde, en geeft u een balancerregel op.

### <a name="create-a-backend-address-pool"></a>Een back-endadresgroep maken

Als u verkeer wilt distribueren naar de VM's, bevat een backend-adresgroep de IP-adressen van de virtuele (NIC's) die zijn verbonden met de Load Balancer. Maak de backend-adresgroep *myBackendPool* met virtuele machines voor het balanceren van internetverkeer.

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en klik vervolgens op **myLoadBalancer** in de lijst resources.
2. Klik onder **Instellingen** op **Back-endpools** en vervolgens op **Toevoegen**.
3. Typ *myBackendPool*op de pagina **Een backend pool** toevoegen voor naam als naam voor uw backendpool en selecteer **Vervolgens Toevoegen**.

### <a name="create-a-health-probe"></a>Een statustest maken

Als u wilt dat de load balancer de status van uw app kan controleren, gebruikt u een statussonde. De statussonde voegt dynamisch VM's toe of verwijdert vm's uit de rotatie van load balancer op basis van hun reactie op gezondheidscontroles. Maak een statustest (*myHealthProbe*) om de status van de VM's te bewaken.

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en klik vervolgens op **myLoadBalancer** in de lijst resources.
2. Klik onder **Instellingen** op **Tests** en klik op **Toevoegen**.
3. Gebruik deze waarden om de statustest te maken:
     
    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer *mijnHealthProbe in*. |
    | Protocol | Selecteer **HTTP**. |
    | Poort | Voer *80*in .|
    | Interval | Voer *15* in voor het aantal **interval in** seconden tussen sondepogingen. |
    | Ongezonde drempelwaarde | Selecteer *2* voor het aantal **ongezonde drempelwaarde** of opeenvolgende sondefouten die moeten optreden voordat een virtuele machine als ongezond wordt beschouwd.|
    
4. Selecteer **OK**.

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Maak een Load Balancer-regel *myLoadBalancerRuleWeb* voor het luisteren naar poort 80 in de *frontend FrontendLoadBalancer* en het verzenden van load-balanced netwerkverkeer naar de backend-adrespool *myBackEndPool* ook met behulp van poort 80.

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en klik vervolgens op **myLoadBalancer** in de lijst resources.
2. Klik onder **Instellingen** op **Taakverdelingsregels** en vervolgens op **Toevoegen**.
3. Gebruik deze waarden om de regel voor het balanceren van de lastentaak te configureren:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer *mijnhttpregel in*. |
    | Protocol | selecteer **TCP**. |
    | Poort | Voer *80*in .|
    | Backend-poort | Voer *80*in . |
    | Back-endpool | Selecteer *myBackendPool*.|
    | Statustest | Selecteer *myHealthProbe*. |
    
4. Laat de overige standaardwaarden staan en selecteer **OK**.

## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie maakt u een virtueel netwerk, maakt u drie virtuele machines voor de backendpool van de Load Balancer en installeert u IIS op de virtuele machines om de Load Balancer te testen.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie moet u de volgende parameters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resourcegroepnaam>**  | myResourceGroupSLB (Bestaande resourcegroep selecteren) |
| **\<>van de naam van het virtuele netwerk** | myVNet          |
| **\<regionaam>**          | Europa -west      |
| **\<IPv4-adresruimte>**   | 10.1.0.0/16          |
| **\<subnetnaam>**          | mySubnet        |
| **\<subnet-adresbereik>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Virtuele machines maken

Standard Load Balancer ondersteunt alleen VM's met standaard IP-adressen in de backendpool. In deze sectie maakt u drie VM's *(myVM1,* *myVM2*en *myVM3)* met een standaard openbaar IP-adres in drie verschillende zones *(Zone 1,* *Zone 2*en *Zone 3)* die worden toegevoegd aan de backendpool van de standaardloadbalansdie eerder is gemaakt.

1. Selecteer linksboven in de portal **de** > optie Een resource**Compute** > **Windows Server 2016-datacenter maken**. 
   
1. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:
   - **Subscription** > **Abonnementsbrongroep**: Selecteer **myResourceGroupSLB**.
   - **Instantie details** > **Virtuele machine naam:** Typ *myVM1*.
   - **Instantiedetails** > **regio** > **Selecteer West-Europa**.
   - **Instance Details** > **Beschikbaarheidsopties voor instantie** details > **Beschikbaarheidszones selecteren**. 
   - **Instantie details** > **Beschikbaarheidzone** > Selecteren **1**.
  
1. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**. 
   
   - Zorg ervoor dat de volgende opties zijn geselecteerd:
       - **Virtueel netwerk**: **myVnet**
       - **Subnet**: **myBackendSubnet**
       - **Openbare IP->** selecteer **Nieuw maken**en selecteer in het venster **Openbaar IP-adres maken** voor **SKU**, selecteer **Standaard**en voor **beschikbaarheidszone,** selecteer **Zone-redundant**
      
   - Als u een nieuwe netwerkbeveiligingsgroep (NSG) wilt maken, een soort firewall, selecteert u onder **Netwerkbeveiligingsgroep** de optie **Geavanceerd**. 
       1. Selecteer in het veld **Netwerkbeveiligingsgroep configureren** de optie **Nieuwe maken**. 
       1. Typ *myNetworkSecurityGroup*en selecteer **OK**.

   - Voer de volgende stappen uit om van de VM deel uit te maken van de backendpool van de Load Balancer:
        - In **Load Balancing**, voor **Plaats deze virtuele machine achter een bestaande load balancing oplossing?**, selecteer **Ja**.
        - Selecteer **Azure load balancer**voor **taakverdelingsopties voor taakverdelingsopties**. **Load balancing settings**
        - Voor **Selecteer een load balancer**, *myLoadBalancer*. 
1. Selecteer het tabblad **Beheer** of selecteer **Volgende** > **Beheer**. Stel bij **Bewaking****Diagnostische gegevens over opstarten** in op **Uit**. 
1. Selecteer **Controleren + maken**.   
1. Controleer de instellingen en selecteer vervolgens **Maken**.
1. Volg de stappen om twee extra VM's te maken - *myVM2* en *myVM3*, met een standaard SKU openbaar IP-adres in **availability zone** **2** en **3** respectievelijk, en alle andere instellingen hetzelfde als *myVM1*.  

### <a name="create-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

In deze sectie maakt u een regel voor netwerkbeveiligingom binnenkomende verbindingen toe te staan met HTTP.

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en klik vervolgens in de lijst resources op **myNetworkSecurityGroup** die zich in de brongroep **myResourceGroupSLB** bevindt.
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

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en klik vervolgens in de lijst resources op **myVM1** die zich in de brongroep *myResourceGroupSLB bevindt.*
2. Klik op de pagina **Overzicht** op **Verbinding maken** om extern verbinding te maken met de VM.
3. Selecteer in het pop-upvenster **Verbinding maken met virtuele machine****RDP-bestand downloaden**, en open vervolgens het gedownloade RDP-bestand.
4. In het venster **Verbinding met extern bureaublad** klikt u op **Verbinden**.
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
1. Zoek op het scherm **Overzicht** het openbare IP-adres voor de load balancer. Selecteer **Alle services** in het linkermenu, selecteer Alle **bronnen**en klik op **myPublicIP**.

2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

      ![IIS-webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Als u wilt zien dat de load balancer het verkeer distribueert over de drie VM's waarop uw app wordt uitgevoerd, u uw webbrowser forceren.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Virtuele machines toevoegen aan of verwijderen uit de back-endpool
Het is mogelijk dat u onderhoud moet uitvoeren op de VM's waarop uw app wordt uitgevoerd, zoals het installeren van besturingssysteemupdates. U moet mogelijk extra VM's toevoegen vanwege toegenomen verkeer naar uw app. In deze sectie ziet u hoe u een VM *(myVM1)* verwijderen of toevoegen uit de load balancer.

### <a name="remove-vm-from-a-backend-pool"></a>VM uit een backendpool verwijderen
Voer de volgende stappen uit om *myVM1* uit de backendpool te verwijderen:

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources**en klik vervolgens op **myLoadBalancer** in de lijst resources.
2. Klik onder **Instellingen** op **Back-endpools**. Klik vervolgens in de lijst van de back-endpool op **myBackendPool**.
3. Als u *vm1* wilt verwijderen, selecteert u op de pagina **myBackendPool** het pictogram verwijderen aan het einde van de rij waarop *myVM1*wordt weergegeven en klikt u vervolgens op **Opslaan**.

Nu *myVM1* niet meer in de back-endadresgroep zit, kunt u onderhoudstaken uitvoeren op *myVM1*, zoals het installeren van software-updates. Bij afwezigheid van *VM1*is de belasting nu in evenwicht tussen *myVM2* en *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>VM toevoegen aan een backendpool
Voer de volgende stappen uit om *myVM1* terug te voegen aan de backendpool:

1. Selecteer **Alle services** in het linkermenu, selecteer Alle **bronnen**en selecteer **vervolgens myVM1** in de lijst met resources.
2. Selecteer op de **VM1-pagina** onder **Instellingen**de optie **Netwerken**.
3. Selecteer **op** de pagina Netwerken het tabblad **Taakverdeling** en selecteer **Laadverdeling toevoegen**.
4. Ga als volgt te werk op de pagina **Taakverdeling toevoegen:**
   1. Selecteer **Azure load balancer**voor **taakverdelingsopties**.
   2. Selecteer *myLoadBalancer*voor **Een load balancer**selecteren .
   3. Selecteer *myBackendPool*voor **Een backendpool selecteren**. 

## <a name="clean-up-resources"></a>Resources opschonen

Als ze niet meer nodig zijn, verwijdert u de brongroep, Load Balancer en alle gerelateerde resources. Selecteer hiervoor de *brongroep myResouceGroupSLB* die de Load Balancer bevat en selecteer **Vervolgens Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een StandaardLoad Balancer gemaakt, VM's aan gekoppeld, de verkeersregel Load Balancer, de statussonde geconfigureerd en vervolgens de load balancer getest. U hebt ook een VM verwijderd uit de set load balancers en deze weer toegevoegd aan de back-endadresgroep. Voor meer informatie over Azure Load Balancer gaat u verder met de zelfstudies voor Azure Load Balancer.

> [!div class="nextstepaction"]
> [Zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
