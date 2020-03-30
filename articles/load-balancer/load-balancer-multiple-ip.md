---
title: Taakverdeling op meerdere IP-configuraties - Azure-portal
titleSuffix: Azure Load Balancer
description: Lees in dit artikel over load balancing voor primaire en secundaire IP-configuraties met behulp van de Azure-portal.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 4bf74986462ecb2659505f8a1261b9b24aba3fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74077010"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Taakverdeling op meerdere IP-configuraties met behulp van de Azure-portal

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [Powershell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)


In dit artikel laten we u zien hoe u Azure Load Balancer gebruiken met meerdere IP-adressen op een secundaire netwerkinterfacecontroller (NIC). Het volgende diagram illustreert ons scenario:

![Load balancer-scenario](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

In ons scenario gebruiken we de volgende configuratie:

- Twee virtuele machines (VM's) waarop Windows wordt uitgevoerd.
- Elke VM heeft een primaire en een secundaire NIC.
- Elke secundaire NIC heeft twee IP-configuraties.
- Elke VM host twee websites: contoso.com en fabrikam.com.
- Elke website is gebonden aan een IP-configuratie op de secundaire NIC.
- Azure Load Balancer wordt gebruikt om twee front-end IP-adressen bloot te leggen, één voor elke website. De front-end adressen worden gebruikt om verkeer te distribueren naar de respectievelijke IP-configuratie voor elke website.
- Hetzelfde poortnummer wordt gebruikt voor zowel front-end IP-adressen als back-end pool IP-adressen.

## <a name="prerequisites"></a>Vereisten

In ons scenariovoorbeeld wordt ervan uitgegaan dat u een resourcegroep met de naam **contosofabrikam** hebt die als volgt is geconfigureerd:

- De resourcegroep bevat een virtueel netwerk met de naam **myVNet**.
- Het **myVNet-netwerk** bevat twee VM's met de naam **VM1** en **VM2.**
- VM1 en VM2 bevinden zich in dezelfde beschikbaarheidsset met de naam **myAvailset**. 
- VM1 en VM2 hebben elk een primaire NIC met de naam **VM1NIC1** en **VM2NIC1,** respectievelijk. 
- VM1 en VM2 hebben elk een secundaire NIC met de naam **VM1NIC2** en **VM2NIC2,** respectievelijk.

Zie [Een vm maken met meerdere NIC's met PowerShell](../virtual-machines/windows/multiple-nics.md)voor meer informatie over het maken van VM's met meerdere NIC's.

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Load balancing uitvoeren op meerdere IP-configuraties

Voer de volgende stappen uit om het in dit artikel beschreven scenario te bereiken.

### <a name="step-1-configure-the-secondary-nics"></a>Stap 1: De secundaire NIC's configureren

Voeg voor elke virtuele virtuele virtuele machine de IP-configuratie voor de secundaire NIC toe:  

1. Blader naar de https://portal.azure.comAzure-portal: . Meld u aan met uw Azure-account.

2. Selecteer linksboven in het scherm het pictogram **Resourcegroep.** Selecteer vervolgens de resourcegroep waar uw VM's zich bevinden (bijvoorbeeld **contosofabrikam).** In het deelvenster **Resourcegroepen** worden alle bronnen en NIC's voor de VM's weergegeven.

3. Voeg voor de secundaire NIC van elke VM de IP-configuratie toe:

    1. Selecteer de secundaire NIC die u wilt configureren.
    
    2. Selecteer **IP-configuraties**. Selecteer In het volgende deelvenster, bovenaan, de optie **Toevoegen**.

    3. Voeg **onder IP-configuraties toevoegen**een tweede IP-configuratie toe aan de NIC: 

        1. Voer een naam in voor de secundaire IP-configuratie. (Voor VM1 en VM2, noem bijvoorbeeld respectievelijk de IP-configuratie **VM1NIC2-ipconfig2** en **VM2NIC2-ipconfig2.)**

        2. Selecteer Statisch voor het **privé-IP-adres** **, Toewijzingsinstelling** **.**

        3. Selecteer **OK**.

Nadat de tweede IP-configuratie voor de secundaire NIC is voltooid, wordt deze weergegeven onder de **IP-configuratiesinstellingen** voor de opgegeven NIC.

### <a name="step-2-create-the-load-balancer"></a>Stap 2: De load balancer maken

Maak uw load balancer voor de configuratie:

1. Blader naar de https://portal.azure.comAzure-portal: . Meld u aan met uw Azure-account.

2. Selecteer linksboven in het scherm De optie **Een resource** > **NetwerkloadBalancer****Networking** > maken . Selecteer vervolgens **Maken**.

3. Typ **onder Load balancer maken**een naam voor uw load balancer. In dit scenario gebruiken we de naam **mylb.**

4. Maak **onder Openbaar IP-adres**een nieuw openbaar IP-adres genaamd **PublicIP1**.

5. Selecteer **onder Resourcegroep**de bestaande resourcegroep voor uw VM's (bijvoorbeeld **contosofabrikam).** Selecteer de locatie waaru de load balancer wilt implementeren en selecteer **OK**.

De load balancer begint te implementeren. Het kan enkele minuten duren voordat de implementatie is voltooid. Nadat de implementatie is voltooid, wordt de load balancer weergegeven als een resource in uw resourcegroep.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Stap 3: De ip-pool aan de voorkant configureren

Configureer voor elke website (contoso.com en fabrikam.com) de front-end IP-pool op uw load balancer:

1. Selecteer **meer services**in de portal . Typ in het filtervak **Openbaar IP-adres** en selecteer **Vervolgens Openbare IP-adressen**. Selecteer In het volgende deelvenster, bovenaan, de optie **Toevoegen**.

2. Configureer twee openbare IP-adressen **(PublicIP1** en **PublicIP2)** voor beide websites (contoso.com en fabrikam.com):

   1. Typ een naam voor uw front-end IP-adres.

   2. Selecteer **voor Resource Group**de bestaande resourcegroep voor uw VM's (bijvoorbeeld **contosofabrikam).**

   3. Selecteer **bij Locatie**dezelfde locatie als de VM's.

   4. Selecteer **OK**.

      Nadat de openbare IP-adressen zijn gemaakt, worden ze weergegeven onder de **openbare IP-adressen.**

3. <a name="step3-3"></a>Selecteer **meer services**in de portal . Typ in het filtervak **load balancer** en selecteer **Load Balancer**. 

4. Selecteer de load balancer **(mylb)** waaraan u de front-end IP-pool wilt toevoegen.

5. Selecteer **onder Instellingen**de **IP-configuratie van Frontend**. Selecteer In het volgende deelvenster, bovenaan, de optie **Toevoegen**.

6. Typ een naam voor uw front-end IP-adres (bijvoorbeeld **contosofe** of **fabrikamfe).**

7. <a name="step3-7"></a>Selecteer **IP-adres**. Selecteer **onder Openbaar IP-adres kiezen**de IP-adressen voor uw front-end **(PublicIP1** of **PublicIP2).**

8. Maak het tweede front-end IP-adres door <a href="#step3-3">stap 3</a> tot <a href="#step3-7">en met stap 7</a> in deze sectie te herhalen.

Nadat de front-end pool is geconfigureerd, worden de IP-adressen weergegeven onder de IP-configuratie-instellingen van de load balancer **Frontend.** 
    
### <a name="step-4-configure-the-back-end-pool"></a>Stap 4: De back-endpool configureren

Configureer voor elke website (contoso.com en fabrikam.com) de back-end adresgroep op uw load balancer:
        
1. Selecteer **meer services**in de portal . Typ in het filtervak **load balancer** en selecteer **Load Balancer**.

2. Selecteer de load balancer **(mylb)** waaraan u de back-endpool wilt toevoegen.

3. Selecteer **Backend-groepen**onder **Instellingen**. Typ een naam voor uw back-end pool (bijvoorbeeld **contosopool** of **fabrikampool).** Selecteer In het volgende deelvenster, bovenaan, de optie **Toevoegen**. 

4. Selecteer **Beschikbaarheidsset** **selecteren voor Gekoppeld aan**.

5. Selecteer **myAvailset**voor **beschikbaarheid .**

6. Voeg de IP-configuraties van het doelnetwerk voor beide VM's toe: 

    ![Back-endpools configureren voor load balancer](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. Selecteer **voor virtuele machine Target**de VM die u wilt toevoegen aan de back-endpool (bijvoorbeeld **VM1** of **VM2).**

    2. Selecteer **voor netwerk-IP-configuratie**de IP-configuratie van de secundaire NIC voor de VM die u in de vorige stap hebt geselecteerd (bijvoorbeeld **VM1NIC2-ipconfig2** of **VM2NIC2-ipconfig2**).

7. Selecteer **OK**.

Nadat de back-endpool is geconfigureerd, worden de adressen weergegeven onder de instellingen van de **backendpool** van de load balancer.

### <a name="step-5-configure-the-health-probe"></a>Stap 5: De statussonde configureren

Een statussonde configureren voor uw load balancer:

1. Selecteer **meer services**in de portal . Typ in het filtervak **load balancer** en selecteer **Load Balancer**.

2. Selecteer de load balancer **(mylb)** waaraan u de statussonde wilt toevoegen.

3. Selecteer onder **Instellingen**de optie **Sonde gezondheid**. Selecteer In het volgende deelvenster, bovenaan, de optie **Toevoegen**. 

4. Typ een naam voor de statussonde (bijvoorbeeld **HTTP**). Selecteer **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Stap 6: Regels voor taakverdeling configureren

Configureer voor elke website (contoso.com en fabrikam.com) de regels voor het balanceren van de lastentaak:
    
1. <a name="step6-1"></a>Selecteer **onder Instellingen**de optie **Taakverdelingsregels**. Selecteer In het volgende deelvenster, bovenaan, de optie **Toevoegen**. 

2. Typ bij **Naam**een naam voor de regel voor taakverdeling (bijvoorbeeld **HTTPc** voor contoso.com of **HTTPf** voor fabrikam.com).

3. Selecteer **voor Frontend IP-adres**het front-end IP-adres dat u eerder hebt gemaakt (bijvoorbeeld **contosofe** of **fabrikamfe).**

4. Voor **poort-** en **backendpoort**behoudt u de standaardwaarde **80**.

5. Selecteer **Uitgeschakeld** **voor Zwevend IP (direct serverretour).**

6. <a name="step6-6"></a>Selecteer **OK**.

7. Maak de regel van de tweede load balancer door <a href="#step6-1">stap 1</a> tot en met <a href="#step6-6">stap 6</a> in deze sectie te herhalen.

Nadat de regels zijn geconfigureerd, worden ze weergegeven onder de instellingen van de regels **voor het balanceren van** de load balancer.

### <a name="step-7-configure-dns-records"></a>Stap 7: DNS-records configureren

Configureer als laatste stap uw DNS-bronrecords om te wijzen op de respectievelijke front-end IP-adressen voor uw load balancer. U uw domeinen hosten in Azure DNS. Zie [Azure DNS gebruiken met andere Azure-services](../dns/dns-for-azure-services.md)voor meer informatie over het gebruik van Azure DNS met Load Balancer.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het combineren van load balancing-services in Azure in [het gebruik van load-balancing services in Azure.](../traffic-manager/traffic-manager-load-balancing-azure.md)
- Meer informatie over hoe u verschillende typen logboeken gebruiken voor het beheren en oplossen van load balancer in [Azure Monitor-logboeken voor Azure Load Balancer.](../load-balancer/load-balancer-monitor-log.md)
