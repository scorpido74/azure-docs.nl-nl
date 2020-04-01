---
title: Beschikbaarheidsgroeplistener configureren voor SQL Server op virtuele RHEL-machines in Azure - Virtuele Linux-machines | Microsoft Documenten
description: Meer informatie over het instellen van een bronvan beschikbaarheidsgroeplistener in SQL Server op Virtuele RHEL-machines in Azure
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 80557eb3776ba17a4922d1fc384b87419ffbd67e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79096595"
---
# <a name="tutorial-configure-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Zelfstudie: Beschikbaarheidsgroeplistener configureren voor SQL Server op virtuele RHEL-machines in Azure

> [!NOTE]
> De tutorial gepresenteerd is in **openbare preview**. 
>
> We gebruiken SQL Server 2017 met RHEL 7.6 in deze zelfstudie, maar het is mogelijk om SQL Server 2019 te gebruiken in RHEL 7 of RHEL 8 om HA te configureren. De opdrachten voor het configureren van bronnen voor beschikbaarheidsgroepen zijn gewijzigd in RHEL 8 en u wilt het artikel, [Beschikbaarheidsgroepbron maken](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) en RHEL 8-resources bekijken voor meer informatie over de juiste opdrachten.

In deze zelfstudie worden stappen doorlopen over het maken van een authenticiteitsgroeplistener voor uw SQL-servers op virtuele RHEL-machines in Azure. U leert het volgende:

> [!div class="checklist"]
> - Een load balancer maken in de Azure-portal
> - De back-endpool configureren voor de load balancer
> - Een sonde maken voor de load balancer
> - De regels voor het balanceren van de lastenverdeling instellen
> - De load balancer-bron in het cluster maken
> - De authenticiteit van de beschikbaarheidsgroep maken
> - Test verbinding maken met de luisteraar
> - Een failover testen

## <a name="prerequisite"></a>Vereiste

Voltooide [ **zelfstudie: beschikbaarheidsgroepen configureren voor SQL Server op virtuele RHEL-machines in Azure**](sql-server-linux-rhel-ha-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>De load balancer maken in de Azure-portal

De volgende instructies nemen u deel aan stappen 1 tot en met 4 van de [load balancer maken en configureren in het azure-portalgedeelte](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal) van het artikel [Load balancer - Azure-portal.](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)

### <a name="create-the-load-balancer"></a>Load balancer maken

1. Open in de Azure-portal de brongroep die de virtuele SQL Server-machines bevat. 

2. Klik in de resourcegroep op **Toevoegen**.

3. Zoek naar **load balancer** en selecteer vervolgens in de zoekresultaten **Load Balancer**, die wordt gepubliceerd door **Microsoft**.

4. Klik op het blade **Load Balancer** op **Maken**.

5. Configureer in het dialoogvenster **Load balancer maken** de load balancer als volgt:

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Een tekstnaam die de load balancer vertegenwoordigt. Bijvoorbeeld **SQLLB**. |
   | **Type** |**Intern** |
   | **Virtueel netwerk** |De standaard VNet die is gemaakt, moet **VM1VNET de naam VM1VNET krijgen.** |
   | **Subnet** |Selecteer het subnet waarin de SQL Server-instanties zich bevinden. De standaardinstelling moet **VM1Subnet**zijn.|
   | **Toewijzing ip-adres** |**Statisch** |
   | **Privé IP-adres** |Gebruik `virtualip` het IP-adres dat in het cluster is gemaakt. |
   | **Abonnement** |Gebruik het abonnement dat is gebruikt voor uw resourcegroep. |
   | **Resourcegroep** |Selecteer de brongroep waarin de SQL Server-instanties zich bevinden. |
   | **Locatie** |Selecteer de Azure-locatie waarin de SQL Server-instanties zich bevinden. |

### <a name="configure-the-back-end-pool"></a>De back-endpool configureren
Azure roept de back-end-end pool *backend pool*. In dit geval is de back-endpool de adressen van de drie SQL Server-exemplaren in uw beschikbaarheidsgroep. 

1. Klik in uw resourcegroep op de load balancer die u hebt gemaakt. 

2. Klik **op Instellingen**op **Backend-groepen**.

3. Klik op **Backend-groepen**op **Toevoegen** om een back-endadresgroep te maken. 

4. Typ **in Backend pool toevoegen**onder **Naam**een naam voor de back-endpool.

5. Selecteer Virtuele machine **onder Gekoppeld aan**, selecteer Virtuele **machine**. 

6. Selecteer elke virtuele machine in de omgeving en koppel het juiste IP-adres aan elke selectie.

    :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-backend-pool.png" alt-text="Backendpool toevoegen":::

7. Klik op**toevoegen**. 

### <a name="create-a-probe"></a>Een sonde maken

De sonde definieert hoe Azure verifieert welke SQL Server-exemplaren momenteel eigenaar is van de listener van de beschikbaarheidsgroep. Azure sondes de service op basis van het IP-adres op een poort die u definieert wanneer u de sonde maakt.

1. Klik op het pagina's met de **lastenbalans instellingen** op **Sondes gezondheid**. 

2. Klik op het blad **Health probes** op **Toevoegen**.

3. Configureer de sonde op het **sondeblad toevoegen.** Gebruik de volgende waarden om de sonde te configureren:

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Een tekstnaam die de sonde voorstelt. **SQLAlwaysOnEndPointProbe**. |
   | **Protocol** |**TCP** |
   | **Poort** |U elke beschikbare poort gebruiken. Bijvoorbeeld *59999*. |
   | **Interval** |*5* |
   | **Ongezonde drempelwaarde** |*2* |

4.  Klik op **OK**. 

5. Meld u aan bij al uw virtuele machines en open de sondepoort met de volgende opdrachten:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Azure maakt de sonde en gebruikt deze vervolgens om te testen welke SQL Server-instantie de listener heeft voor de beschikbaarheidsgroep.

### <a name="set-the-load-balancing-rules"></a>De regels voor het balanceren van de lastenverdeling instellen

De regels voor taakverdeling configureren hoe de load balancer verkeer doorstuurt naar de SQL Server-instanties. Voor deze load balancer schakelt u direct serverretour in omdat slechts één van de drie SQL Server-exemplaren tegelijkertijd eigenaar is van de bron van de beschikbaarheidsgroeplistener.

1. Klik op het **pagina-instellingenblad van** de lastenbalans op **Taakverdelingsregels**. 

2. Klik op het blad **Taakverdelingsregels** op **Toevoegen**.

3. Configureer op het blad **Regelregels voor lastenverdeling toevoegen** de regel voor het balanceren van de lasten. Gebruik de volgende instellingen: 

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Een tekstnaam die de regels voor het balanceren van de last en de tijd weergeeft. **SQLAlwaysOnEndPointListener**. |
   | **Protocol** |**TCP** |
   | **Poort** |*1433* |
   | **Backend-poort** |*1433*. Deze waarde wordt genegeerd omdat deze regel **Zwevend IP (direct serverretour)** gebruikt. |
   | **Sonde** |Gebruik de naam van de sonde die u voor deze load balancer hebt gemaakt. |
   | **Sessiepersistentie** |**Geen** |
   | **Niet-actieve time-out (minuten)** |*4* |
   | **Zwevend IP (direct server retour)** |**Ingeschakeld** |

   :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-load-balancing-rule.png" alt-text="Regel voor taakverdeling toevoegen":::

4. Klik op **OK**. 
5. Azure configureert de regel voor het balanceren van de regel. Nu is de load balancer geconfigureerd om verkeer te routeren naar het SQL Server-exemplaar dat de listener host voor de beschikbaarheidsgroep. 

Op dit moment heeft de resourcegroep een load balancer die verbinding maakt met alle SQL Server-machines. De load balancer bevat ook een IP-adres voor de SQL Server Always On availability group listener, zodat elke machine kan reageren op verzoeken om de beschikbaarheidsgroepen.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>De load balancer-bron in het cluster maken

1. Log in op de primaire virtuele machine. We moeten de bron maken om de Azure load balancer probe-poort in te schakelen (59999 wordt in ons voorbeeld gebruikt). Voer de volgende opdracht uit:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Maak een groep `virtualip` die `azure_load_balancer` de bron en de resource bevat:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Beperkingen toevoegen

1. Er moet een colocatiebeperking worden geconfigureerd om ervoor te zorgen dat het IP-adres azure load balancer en de AG-bron op hetzelfde knooppunt worden uitgevoerd. Voer de volgende opdracht uit:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Maak een bestelbeperking om ervoor te zorgen dat de AG-bron actief is vóór het IP-adres van azure load balancer. Hoewel de colocatiebeperking een bestelbeperking impliceert, dwingt dit deze af.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. Voer de volgende opdracht uit om de beperkingen te verifiëren:

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

## <a name="create-the-availability-group-listener"></a>De authenticiteit van de beschikbaarheidsgroep maken

1. Voer op het primaire knooppunt de volgende opdracht uit in SQLCMD of SSMS:

    - Vervang het IP-adres `virtualip` dat hieronder wordt gebruikt door het IP-adres.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Log in op elk VM-knooppunt. Gebruik de volgende opdracht om het hosts-bestand te `ag1-listener` openen en de oplossing van de hostnaam in te stellen voor elke machine.

    ```
    sudo vi /etc/hosts
    ```

    Voeg in de `i` **vi-editor** tekst in en voeg op een `ag1-listener`lege regel het IP van de . Voeg `ag1-listener` vervolgens na een spatie naast het IP-adres toe.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    Als u de **vi-editor** wilt afsluiten, drukt `:wq` u eerst op de **Esc-toets** en voert u de opdracht in om het bestand te schrijven en af te sluiten. Doe dit op elk knooppunt.

## <a name="test-the-listener-and-a-failover"></a>Test de luisteraar en een failover

### <a name="test-logging-into-sql-server-using-the-availability-group-listener"></a>Logboekregistratie in SQL Server testen met de listener van de beschikbaarheidsgroep

1. Gebruik SQLCMD om u aan te melden bij het primaire knooppunt van SQL Server met de naam van de listener voor beschikbaarheidsgroepen:

    - Gebruik een login die eerder `<YourPassword>` is gemaakt en vervang het juiste wachtwoord. In het onderstaande voorbeeld wordt de `sa` aanmelding gebruikt die met de SQL Server is gemaakt.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Controleer de naam van de server waarmee u bent verbonden. Voer de volgende opdracht uit in SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Uw uitvoer moet het huidige primaire knooppunt weergeven. Dit zou `VM1` moeten zijn als u nog nooit een failover getest.

    Sluit de SQL-sessie `exit` af door de opdracht te typen.

### <a name="test-a-failover"></a>Een failover testen

1. Voer de volgende opdracht uit om de `<VM2>` primaire replica handmatig naar of een andere replica te failoveren. Vervang `<VM2>` door de waarde van uw servernaam.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Als u uw beperkingen controleert, ziet u dat er een andere beperking is toegevoegd vanwege de handmatige failover:

    ```bash
    sudo pcs constraint list --full
    ```

    U ziet dat er `cli-prefer-ag_cluster-master` een beperking met id is toegevoegd.

1. Verwijder de beperking `cli-prefer-ag_cluster-master` met ID met de volgende opdracht:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Controleer uw clusterbronnen `sudo pcs resource`met de opdracht en u moet `<VM2>`zien dat de primaire instantie nu .

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Gebruik SQLCMD om u aan te melden bij uw primaire replica met de naam van de listener:

    - Gebruik een login die eerder `<YourPassword>` is gemaakt en vervang het juiste wachtwoord. In het onderstaande voorbeeld wordt de `sa` aanmelding gebruikt die met de SQL Server is gemaakt.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Controleer de server waarmee u bent verbonden. Voer de volgende opdracht uit in SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    U moet zien dat u nu bent verbonden met de VM waarmee u het niet hebt overgemaakt.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over load balancers in Azure:

> [!div class="nextstepaction"]
> [Een load balancer configureren voor een beschikbaarheidsgroep op Azure SQL Server VM's](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)
