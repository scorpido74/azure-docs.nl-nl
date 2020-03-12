---
title: De listener voor de beschikbaarheids groep configureren voor SQL Server op virtuele RHEL-machines in azure-Linux Virtual Machines | Microsoft Docs
description: Meer informatie over het instellen van een beschikbaarheids groep-listener in SQL Server op virtuele RHEL-machines in azure
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 80557eb3776ba17a4922d1fc384b87419ffbd67e
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096595"
---
# <a name="tutorial-configure-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Zelf studie: een beschikbaarheids groep-listener configureren voor SQL Server op virtuele RHEL-machines in azure

> [!NOTE]
> De zelf studie wordt weer gegeven in de **open bare preview**. 
>
> In deze zelf studie gebruiken we SQL Server 2017 met RHEL 7,6, maar het is wel mogelijk om SQL Server 2019 te gebruiken in RHEL 7 of RHEL 8 om HA te configureren. De opdrachten voor het configureren van resources van de beschikbaarheids groep zijn gewijzigd in RHEL 8 en u wilt het artikel bekijken, [resource voor de beschikbaarheids groep maken](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) en RHEL 8 resources voor meer informatie over de juiste opdrachten.

Deze zelf studie gaat over de stappen voor het maken van een beschikbaarheids groep-listener voor uw SQL-servers op virtuele RHEL-machines in Azure. U leert het volgende:

> [!div class="checklist"]
> - Maak een load balancer in het Azure Portal
> - De back-end-pool voor de load balancer configureren
> - Een test maken voor de load balancer
> - De taakverdelings regels instellen
> - De load balancer-resource in het cluster maken
> - De listener voor de beschikbaarheids groep maken
> - De verbinding met de listener testen
> - Een failover testen

## <a name="prerequisite"></a>Vereiste

Voltooide [ **zelf studie: beschikbaarheids groepen configureren voor SQL Server op virtuele RHEL-machines in azure**](sql-server-linux-rhel-ha-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Maak de load balancer in het Azure Portal

De volgende instructies begeleidt u stapsgewijs door de stappen 1 tot en met 4 van de sectie [het Load Balancer maken en configureren in het Azure Portal](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal) gedeelte van het [Azure Portal artikel Load Balancer](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md) .

### <a name="create-the-load-balancer"></a>Load balancer maken

1. Open in de Azure Portal de resource groep die de SQL Server virtuele machines bevat. 

2. Klik in de resource groep op **toevoegen**.

3. Zoek naar **Load Balancer** en selecteer in de zoek resultaten **Load Balancer**, dat wordt gepubliceerd door **micro soft**.

4. Klik op de Blade **Load Balancer** op **maken**.

5. Configureer in het dialoog venster **Load Balancer maken** de Load Balancer als volgt:

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Een tekst naam die de load balancer vertegenwoordigt. Bijvoorbeeld **sqlLB**. |
   | **Type** |**Intern** |
   | **Virtueel netwerk** |Het standaard-VNet dat is gemaakt, moet de naam **VM1VNET**hebben. |
   | **Subnet** |Selecteer het subnet waarin de SQL Server exemplaren zich bevinden. De standaard waarde moet **VM1Subnet**zijn.|
   | **Toewijzing van IP-adres** |**Storing** |
   | **Privé-IP-adres** |Gebruik het `virtualip` IP-adres dat in het cluster is gemaakt. |
   | **Abonnement** |Gebruik het abonnement dat is gebruikt voor uw resource groep. |
   | **Resourcegroep** |Selecteer de resource groep waarvan de SQL Server exemplaren zich bevinden. |
   | **Locatie** |Selecteer de Azure-locatie waar de SQL Server exemplaren zich bevinden. |

### <a name="configure-the-back-end-pool"></a>De back-end-pool configureren
Azure roept de back-end *-back-* mailadres groep aan. In dit geval is de back-end-pool de adressen van de drie SQL Server exemplaren in uw beschikbaarheids groep. 

1. Klik in de resource groep op het load balancer dat u hebt gemaakt. 

2. Klik bij **instellingen**op **back-end-Pools**.

3. Klik op **toevoegen** op back-end- **Pools**om een back-end-adres groep te maken. 

4. Typ bij **naam**de **groep back-end toevoegen**een naam voor de back-end-pool.

5. Selecteer onder **gekoppeld aan**de optie **virtuele machine**. 

6. Selecteer elke virtuele machine in de omgeving en koppel het juiste IP-adres aan elke selectie.

    :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-backend-pool.png" alt-text="Back-end-pool toevoegen":::

7. Klik op **Toevoegen**. 

### <a name="create-a-probe"></a>Een test maken

De test definieert hoe Azure controleert welke van de SQL Server exemplaren momenteel eigenaar is van de beschikbaarheids groep-listener. Azure controleert de service op basis van het IP-adres op een poort die u opgeeft bij het maken van de test.

1. Klik op de Blade load balancer **instellingen** op **status controles**. 

2. Klik op de Blade **status tests** op **toevoegen**.

3. Configureer de test op de Blade **test toevoegen** . Gebruik de volgende waarden om de test te configureren:

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Een tekst naam die de test weergeeft. Bijvoorbeeld **SQLAlwaysOnEndPointProbe**. |
   | **Protocol** |**TCP** |
   | **Poort** |U kunt elke beschik bare poort gebruiken. Bijvoorbeeld *59999*. |
   | **Interval** |*5* |
   | **Drempel waarde voor onjuiste status** |*2* |

4.  Klik op **OK**. 

5. Meld u aan bij alle virtuele machines en open de test poort met de volgende opdrachten:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Azure maakt de test en gebruikt deze om te testen welke SQL Server instantie de listener voor de beschikbaarheids groep heeft.

### <a name="set-the-load-balancing-rules"></a>De taakverdelings regels instellen

De taakverdelings regels configureren hoe de load balancer verkeer naar de SQL Server-instanties routeert. Voor deze load balancer schakelt u Direct Server Return in, omdat slechts één van de drie SQL Server exemplaren de listener-resource van de beschikbaarheids groep tegelijk bezit.

1. Klik op de Blade load balancer **instellingen** op taakverdelings **regels**. 

2. Klik op de Blade taakverdelings **regels** op **toevoegen**.

3. Configureer op de Blade taakverdelings **regels toevoegen** de taakverdelings regel. Gebruik de volgende instellingen: 

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Een tekst naam die de taakverdelings regels vertegenwoordigt. Bijvoorbeeld **SQLAlwaysOnEndPointListener**. |
   | **Protocol** |**TCP** |
   | **Poort** |*1433* |
   | **Backend-poort** |*1433*. deze waarde wordt genegeerd omdat deze regel een **zwevend IP-adres (direct server return)** gebruikt. |
   | **Meet** |Gebruik de naam van de test die u hebt gemaakt voor deze load balancer. |
   | **Sessie persistentie** |**Geen** |
   | **Time-out voor inactiviteit (minuten)** |*4* |
   | **Zwevend IP (Direct Server Return)** |**Ingeschakeld** |

   :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-load-balancing-rule.png" alt-text="Taakverdelings regel toevoegen":::

4. Klik op **OK**. 
5. De taakverdelings regel wordt door Azure geconfigureerd. Nu is de load balancer geconfigureerd om verkeer te routeren naar het SQL Server-exemplaar dat als host fungeert voor de listener voor de beschikbaarheids groep. 

Op dit moment heeft de resource groep een load balancer die is verbonden met alle SQL Server machines. De load balancer bevat ook een IP-adres voor de SQL Server always on-beschikbaarheids groep, zodat elke computer kan reageren op aanvragen voor de beschikbaarheids groepen.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>De load balancer-resource in het cluster maken

1. Meld u aan bij de primaire virtuele machine. We moeten de resource maken om de Azure load balancer test poort in te scha kelen (59999 wordt in ons voor beeld gebruikt). Voer de volgende opdracht uit:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Maak een groep die de `virtualip` en `azure_load_balancer` resource bevat:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Beperkingen toevoegen

1. Er moet een beperking voor een co-locatie worden geconfigureerd om ervoor te zorgen dat de Azure load balancer IP-adres en de AG-resource op hetzelfde knoop punt worden uitgevoerd. Voer de volgende opdracht uit:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Maak een ordenings beperking om ervoor te zorgen dat de AG-bron vóór het Azure load balancer IP-adres actief is. Hoewel de beperking voor de co-locatie een volgorde beperking impliceert, wordt deze afgedwongen.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. Voer de volgende opdracht uit om de beperkingen te controleren:

    ```bash
    sudo pcs constraint list --full
    ```

    In dat geval moet de volgende uitvoer worden weergegeven:

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>De listener voor de beschikbaarheids groep maken

1. Voer op het primaire knoop punt de volgende opdracht uit in SQLCMD of SSMS:

    - Vervang het onderstaande IP-adres door het `virtualip` IP-adres.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Meld u aan bij elk VM-knoop punt. Gebruik de volgende opdracht om het hosts-bestand te openen en de omzetting van hostnamen voor de `ag1-listener` op elke computer in te stellen.

    ```
    sudo vi /etc/hosts
    ```

    Voer in de **VI** -editor `i` in om tekst in te voegen en voeg op een lege regel het IP-adres van de `ag1-listener`toe. Voeg `ag1-listener` vervolgens toe na een spatie naast het IP-adres.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    Als u de **VI** -editor wilt afsluiten, klikt u eerst op **ESC** en voert u vervolgens de opdracht `:wq` om het bestand te schrijven en af te sluiten. Doe dit op elk knoop punt.

## <a name="test-the-listener-and-a-failover"></a>De listener en een failover testen

### <a name="test-logging-into-sql-server-using-the-availability-group-listener"></a>Logboek registratie in SQL Server met de beschikbaarheids groep-listener testen

1. Gebruik SQLCMD om u aan te melden bij het primaire knoop punt van SQL Server met behulp van de naam van de beschikbaarheids groep-listener:

    - Gebruik een eerder gemaakte aanmelding en vervang `<YourPassword>` door het juiste wacht woord. In het onderstaande voor beeld wordt gebruikgemaakt van de `sa` aanmelding die is gemaakt met de SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Controleer de naam van de server waarmee u verbinding hebt. Voer de volgende opdracht uit in SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    In de uitvoer moet het huidige primaire knoop punt worden weer gegeven. Dit moet `VM1` als u een failover nooit hebt getest.

    Sluit de SQL-sessie af door de `exit` opdracht te typen.

### <a name="test-a-failover"></a>Een failover testen

1. Voer de volgende opdracht uit om de primaire replica hand matig te failoveren naar `<VM2>` of een andere replica. Vervang `<VM2>` door de waarde van de server naam.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Als u de beperkingen controleert, ziet u dat er een andere beperking is toegevoegd vanwege de hand matige failover:

    ```bash
    sudo pcs constraint list --full
    ```

    U ziet dat er een beperking met ID `cli-prefer-ag_cluster-master` is toegevoegd.

1. Verwijder de beperking met ID `cli-prefer-ag_cluster-master` met behulp van de volgende opdracht:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Controleer uw cluster bronnen met behulp van de opdracht `sudo pcs resource`en u ziet dat het primaire exemplaar nu `<VM2>`is.

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Gebruik SQLCMD om u aan te melden bij uw primaire replica met behulp van de naam van de listener:

    - Gebruik een eerder gemaakte aanmelding en vervang `<YourPassword>` door het juiste wacht woord. In het onderstaande voor beeld wordt gebruikgemaakt van de `sa` aanmelding die is gemaakt met de SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Controleer de server waarmee u bent verbonden. Voer de volgende opdracht uit in SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    U ziet nu dat u verbinding hebt met de virtuele machine waarvoor u een failover hebt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over load balancers in Azure:

> [!div class="nextstepaction"]
> [Een load balancer configureren voor een beschikbaarheids groep op Azure SQL Server Vm's](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)
