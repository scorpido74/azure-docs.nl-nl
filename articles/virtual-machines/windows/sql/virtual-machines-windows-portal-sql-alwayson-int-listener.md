---
title: Listeners voor de beschikbaarheids groep configureren & load balancer (Azure Portal)
description: Stapsgewijze instructies voor het maken van een listener voor een always on-beschikbaarheids groep voor SQL Server op virtuele machines in azure
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: aefd7a55090da7f55404d6f551ab61268582ff5a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039651"
---
# <a name="configure-a-load-balancer-for-an-availability-group-on-azure-sql-server-vms"></a>Een load balancer configureren voor een beschikbaarheids groep op Azure SQL Server Vm's
In dit artikel wordt uitgelegd hoe u een load balancer maakt voor een SQL Server AlwaysOn-beschikbaarheids groep in azure virtual machines die worden uitgevoerd met Azure Resource Manager. Een beschikbaarheids groep vereist een load balancer wanneer de SQL Server exemplaren zich op Azure virtual machines bevinden. Met de load balancer wordt het IP-adres voor de beschikbaarheids groep-listener opgeslagen. Als een beschikbaarheids groep meerdere regio's omvat, moet elke regio een load balancer hebben.

Als u deze taak wilt volt ooien, moet u beschikken over een SQL Server-beschikbaarheids groep die is geïmplementeerd op virtuele machines van Azure die worden uitgevoerd met Resource Manager. Beide SQL Server virtuele machines moeten deel uitmaken van dezelfde beschikbaarheidsset. U kunt de [micro soft-sjabloon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) gebruiken om automatisch de beschikbaarheids groep te maken in Resource Manager. Met deze sjabloon wordt automatisch een intern load balancer voor u gemaakt. 

Als u wilt, kunt u [een beschikbaarheids groep hand matig configureren](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Voor dit artikel moet uw beschikbaarheids groepen al zijn geconfigureerd.  

Verwante onderwerpen zijn onder andere:

* [AlwaysOn-beschikbaarheids groepen configureren in azure VM (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Een VNet-naar-VNet-verbinding configureren met behulp van Azure Resource Manager en Power shell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Door dit artikel te door lopen, maakt en configureert u een load balancer in het Azure Portal. Nadat het proces is voltooid, configureert u het cluster voor het gebruik van het IP-adres uit de load balancer voor de beschikbaarheids groep-listener.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>De load balancer in de Azure Portal maken en configureren
In dit gedeelte van de taak gaat u als volgt te werk:

1. Maak in de Azure Portal de load balancer en configureer het IP-adres.
2. Configureer de back-end-pool.
3. Maak de test. 
4. De taakverdelings regels instellen.

> [!NOTE]
> Als de SQL Server exemplaren zich in meerdere resource groepen en regio's bevinden, voert u elke stap twee keer uit en eenmaal in elke resource groep.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Stap 1: de load balancer maken en het IP-adres configureren
Maak eerst de load balancer. 

1. Open in de Azure Portal de resource groep die de SQL Server virtuele machines bevat. 

2. Klik in de resource groep op **toevoegen**.

3. Zoek naar **Load Balancer** en selecteer in de zoek resultaten **Load Balancer**, dat wordt gepubliceerd door **micro soft**.

4. Klik op de Blade **Load Balancer** op **maken**.

5. Configureer in het dialoog venster **Load Balancer maken** de Load Balancer als volgt:

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Een tekst naam die de load balancer vertegenwoordigt. Bijvoorbeeld **sqlLB**. |
   | **Type** |**Intern**: de meeste implementaties gebruiken een interne Load Balancer, waarmee toepassingen binnen hetzelfde virtuele netwerk verbinding kunnen maken met de beschikbaarheids groep.  </br> **Extern**: Hiermee kunnen toepassingen verbinding maken met de beschikbaarheids groep via een open bare Internet verbinding. |
   | **Virtueel netwerk** |Selecteer het virtuele netwerk waarin de SQL Server exemplaren zich bevinden. |
   | **Subnet** |Selecteer het subnet waarin de SQL Server exemplaren zich bevinden. |
   | **Toewijzing van IP-adres** |**Storing** |
   | **Privé-IP-adres** |Geef een beschikbaar IP-adres op in het subnet. Gebruik dit IP-adres wanneer u een listener op het cluster maakt. In een Power shell-script, verderop in dit artikel, gebruikt u dit adres voor de variabele `$ILBIP`. |
   | **Abonnement** |Als u meerdere abonnementen hebt, kan dit veld worden weer gegeven. Selecteer het abonnement dat u aan deze resource wilt koppelen. Het is normaal gesp roken hetzelfde abonnement als alle resources voor de beschikbaarheids groep. |
   | **Resourcegroep** |Selecteer de resource groep waarvan de SQL Server exemplaren zich bevinden. |
   | **Locatie** |Selecteer de Azure-locatie waar de SQL Server exemplaren zich bevinden. |

6. Klik op **Create**. 

Azure maakt de load balancer. De load balancer hoort bij een specifiek netwerk, subnet, resource groep en locatie. Nadat de taak door Azure is voltooid, controleert u de load balancer-instellingen in Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Stap 2: de back-end-pool configureren
Azure roept de back-end *-back-* mailadres groep aan. In dit geval is de back-end-pool de adressen van de twee SQL Server exemplaren in uw beschikbaarheids groep. 

1. Klik in de resource groep op het load balancer dat u hebt gemaakt. 

2. Klik bij **instellingen**op **back-end-Pools**.

3. Klik op **toevoegen** op back-end- **Pools**om een back-end-adres groep te maken. 

4. Typ bij **naam**de **groep back-end toevoegen**een naam voor de back-end-pool.

5. Klik onder **virtuele machines**op **een virtuele machine toevoegen**. 

6. Klik onder **virtuele machines kiezen**op **een beschikbaarheidsset kiezen**en geef vervolgens de beschikbaarheidsset op waarvan de SQL Server virtuele machines deel uitmaken.

7. Nadat u de beschikbaarheidsset hebt gekozen, klikt u op **de virtuele machines kiezen**, selecteert u de twee virtuele machines die als host fungeren voor de SQL Server exemplaren in de beschikbaarheids groep en klikt u vervolgens op **selecteren**. 

8. Klik op **OK** om de Blades voor het **kiezen van virtuele machines**te sluiten en de **back-endadresgroep toe te voegen**. 

De instellingen voor de back-end-adres groep worden door Azure bijgewerkt. Uw beschikbaarheidsset heeft nu een groep van twee SQL Server exemplaren.

### <a name="step-3-create-a-probe"></a>Stap 3: een test maken
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

> [!NOTE]
> Zorg ervoor dat de poort die u opgeeft, is geopend op de firewall van beide exemplaren van SQL Server. Voor beide exemplaren is een binnenkomende regel vereist voor de TCP-poort die u gebruikt. Zie [firewall regel toevoegen of bewerken](https://technet.microsoft.com/library/cc753558.aspx)voor meer informatie. 
> 
> 

Azure maakt de test en gebruikt deze om te testen welke SQL Server instantie de listener voor de beschikbaarheids groep heeft.

### <a name="step-4-set-the-load-balancing-rules"></a>Stap 4: de taakverdelings regels instellen
De taakverdelings regels configureren hoe de load balancer verkeer naar de SQL Server-instanties routeert. Voor deze load balancer schakelt u Direct Server Return in, omdat slechts een van de twee SQL Server exemplaren de listener-resource van de beschikbaarheids groep tegelijk heeft.

1. Klik op de Blade load balancer **instellingen** op taakverdelings **regels**. 

2. Klik op de Blade taakverdelings **regels** op **toevoegen**.

3. Configureer op de Blade taakverdelings **regels toevoegen** de taakverdelings regel. Gebruik de volgende instellingen: 

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Een tekst naam die de taakverdelings regels vertegenwoordigt. Bijvoorbeeld **SQLAlwaysOnEndPointListener**. |
   | **Protocol** |**TCP** |
   | **Poort** |*1433* |
   | **Backend-poort** |*1433*. deze waarde wordt genegeerd omdat deze regel een **zwevend IP-adres (direct server return)** gebruikt. |
   | **Probe** |Gebruik de naam van de test die u hebt gemaakt voor deze load balancer. |
   | **Sessie persistentie** |**Geen** |
   | **Time-out voor inactiviteit (minuten)** |*4* |
   | **Zwevend IP (Direct Server Return)** |**Ingeschakeld** |

   > [!NOTE]
   > Mogelijk moet u omlaag schuiven op de Blade om alle instellingen weer te geven.
   > 

4. Klik op **OK**. 
5. De taakverdelings regel wordt door Azure geconfigureerd. Nu is de load balancer geconfigureerd om verkeer te routeren naar het SQL Server-exemplaar dat als host fungeert voor de listener voor de beschikbaarheids groep. 

Op dit moment heeft de resource groep een load balancer die verbinding maakt met beide SQL Server machines. De load balancer bevat ook een IP-adres voor de SQL Server always on-beschikbaarheids groep, zodat de computer kan reageren op aanvragen voor de beschikbaarheids groepen.

> [!NOTE]
> Als uw SQL Server exemplaren zich in twee verschillende regio's bevinden, herhaalt u de stappen in de andere regio. Voor elke regio is een load balancer vereist. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Het cluster configureren voor het gebruik van het load balancer IP-adres
De volgende stap is het configureren van de listener op het cluster en het online brengen van de listener. Ga als volgt te werk: 

1. Maak de beschikbaarheids groep-listener op het failovercluster. 

2. De listener online zetten.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Stap 5: de beschikbaarheids groep-listener maken op het failovercluster
In deze stap maakt u hand matig de beschikbaarheids groep-listener in Failoverclusterbeheer en SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>De configuratie van de listener controleren

Als de cluster bronnen en afhankelijkheden correct zijn geconfigureerd, kunt u de listener in SQL Server Management Studio weer geven. Ga als volgt te werk om de listener-poort in te stellen:

1. Start SQL Server Management Studio en maak vervolgens verbinding met de primaire replica.

2. Ga naar AlwaysOn- **beschikbaarheids groepen** voor **hoge beschik baarheid** >  > listeners voor de **beschikbaarheids groep**.  
    U ziet nu de naam van de listener die u hebt gemaakt in Failoverclusterbeheer. 

3. Klik met de rechter muisknop op de naam van de listener en klik vervolgens op **Eigenschappen**.

4. Geef in het vak **poort** het poort nummer voor de beschikbaarheids groep-listener op met behulp van de $EndpointPort die u eerder hebt gebruikt (1433 was de standaard instelling) en klik vervolgens op **OK**.

U hebt nu een beschikbaarheids groep in azure virtual machines die worden uitgevoerd in de modus Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>De verbinding met de listener testen
Test de verbinding door de volgende handelingen uit te voeren:

1. RDP naar een SQL Server-exemplaar dat zich in hetzelfde virtuele netwerk bevindt, maar geen eigenaar is van de replica. Deze server kan het andere SQL Server-exemplaar in het cluster zijn.

2. Gebruik het **Sqlcmd** -hulp programma om de verbinding te testen. Met het volgende script wordt bijvoorbeeld een **Sqlcmd** -verbinding met de primaire replica tot stand gebracht via de listener met Windows-verificatie:
   
        sqlcmd -S <listenerName> -E

De SQLCMD-verbinding maakt automatisch verbinding met het SQL Server-exemplaar dat als host fungeert voor de primaire replica. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Een IP-adres maken voor een extra beschikbaarheids groep

Elke beschikbaarheids groep maakt gebruik van een afzonderlijke listener. Elke listener heeft een eigen IP-adres. Gebruik hetzelfde load balancer om het IP-adres voor extra listeners te bewaren. Nadat u een beschikbaarheids groep hebt gemaakt, voegt u het IP-adres toe aan de load balancer en configureert u vervolgens de listener.

Ga als volgt te werk om een IP-adres toe te voegen aan een load balancer met de Azure Portal:

1. Open in de Azure Portal de resource groep met de load balancer en klik vervolgens op de load balancer. 

2. Klik onder **instellingen**op **frontend IP-adres groep**en klik vervolgens op **toevoegen**. 

3. Wijs onder **IP-adres van frontend toevoegen**een naam toe aan de front-end. 

4. Controleer of het **virtuele netwerk** en het **subnet** hetzelfde zijn als de SQL Server exemplaren.

5. Stel het IP-adres in voor de listener. 
   
   >[!TIP]
   >U kunt het IP-adres instellen op statisch en een adres typen dat momenteel niet wordt gebruikt in het subnet. U kunt het IP-adres ook instellen op dynamisch en de nieuwe front-end-IP-groep opslaan. Wanneer u dit doet, wijst de Azure Portal automatisch een beschikbaar IP-adres toe aan de groep. U kunt de front-end-IP-adres groep vervolgens opnieuw openen en de toewijzing wijzigen in statisch. 

6. Sla het IP-adres voor de listener op. 

7. Voeg een status test toe met behulp van de volgende instellingen:

   |Instelling |Waarde
   |:-----|:----
   |**Naam** |Een naam om de test te identificeren.
   |**Protocol** |TCP
   |**Poort** |Een ongebruikte TCP-poort, die op alle virtuele machines beschikbaar moet zijn. Het kan niet voor andere doel einden worden gebruikt. Er kunnen niet twee listeners gebruikmaken van dezelfde test poort. 
   |**Interval** |De hoeveelheid tijd tussen de test pogingen. Gebruik de standaard waarde (5).
   |**Drempel waarde voor onjuiste status** |Het aantal opeenvolgende drempel waarden dat moet mislukken voordat een virtuele machine als een slechte status wordt beschouwd.

8. Klik op **OK** om de test op te slaan. 

9. Een taakverdelings regel maken. Klik op **taakverdelings regels**en klik vervolgens op **toevoegen**.

10. Configureer de nieuwe regel voor taak verdeling met behulp van de volgende instellingen:

    |Instelling |Waarde
    |:-----|:----
    |**Naam** |Een naam om de taakverdelings regel te identificeren. 
    |**Frontend-IP-adres** |Selecteer het IP-adres dat u hebt gemaakt. 
    |**Protocol** |TCP
    |**Poort** |Gebruik de poort die de SQL Server exemplaren gebruiken. Een standaard exemplaar gebruikt poort 1433, tenzij u deze hebt gewijzigd. 
    |**Backend-poort** |Gebruik dezelfde waarde als **poort**.
    |**Back-end-groep** |De groep die de virtuele machines bevat met de SQL Server exemplaren. 
    |**Status test** |Kies de test die u hebt gemaakt.
    |**Sessie persistentie** |None
    |**Time-out voor inactiviteit (minuten)** |Standaard (4)
    |**Zwevend IP (Direct Server Return)** | Ingeschakeld

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>De beschikbaarheids groep configureren voor het gebruik van het nieuwe IP-adres

Als u de configuratie van het cluster wilt volt ooien, herhaalt u de stappen die u hebt gevolgd tijdens het maken van de eerste beschikbaarheids groep. Dat wil zeggen dat u het [cluster moet configureren voor het gebruik van het nieuwe IP-adres](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Nadat u een IP-adres voor de listener hebt toegevoegd, configureert u de extra beschikbaarheids groep door het volgende te doen: 

1. Controleer of de test poort voor het nieuwe IP-adres op beide SQL Server virtuele machines is geopend. 

2. [Voeg in Cluster beheer het client toegangs punt toe](#addcap).

3. [Configureer de IP-resource voor de beschikbaarheids groep](#congroup).

   >[!IMPORTANT]
   >Wanneer u het IP-adres maakt, gebruikt u het IP-adres dat u hebt toegevoegd aan de load balancer.  

4. [Zorg ervoor dat de resource van de SQL Server-beschikbaarheids groep afhankelijk is van het client toegangs punt](#dependencyGroup).

5. [Maak de bron van het client toegangs punt afhankelijk van het IP-adres](#listname).
 
6. [Stel de cluster parameters in Power shell in](#setparam).

Nadat u de beschikbaarheids groep hebt geconfigureerd voor het gebruik van het nieuwe IP-adres, configureert u de verbinding met de listener. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Taakverdelings regel toevoegen voor gedistribueerde beschikbaarheids groep

Als een beschikbaarheids groep deelneemt aan een gedistribueerde beschikbaarheids groep, heeft de load balancer een extra regel nodig. Deze regel slaat de poort op die wordt gebruikt door de gedistribueerde beschikbaarheids groep-listener.

>[!IMPORTANT]
>Deze stap is alleen van toepassing als de beschikbaarheids groep deelneemt aan een [gedistribueerde beschikbaarheids groep](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Maak op elke server die deel uitmaakt van de gedistribueerde beschikbaarheids groep, een regel voor binnenkomend verkeer op de TCP-poort van de gedistribueerde beschikbaarheids groep. In veel voor beelden maakt documentatie gebruik van 5022. 

1. Klik in de Azure Portal op de load balancer en klik op **taakverdelings regels**en klik vervolgens op **+ toevoegen**. 

1. Maak de taakverdelings regel met de volgende instellingen:

   |Instelling |Waarde
   |:-----|:----
   |**Naam** |Een naam voor het identificeren van de taakverdelings regel voor de gedistribueerde beschikbaarheids groep. 
   |**Frontend-IP-adres** |Gebruik hetzelfde frontend-IP-adres als de beschikbaarheids groep.
   |**Protocol** |TCP
   |**Poort** |5022-de poort voor de [eind punt-listener van de gedistribueerde beschikbaarheids groep](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Dit kan elke beschik bare poort zijn.  
   |**Backend-poort** | 5022-gebruik dezelfde waarde als **poort**.
   |**Back-end-groep** |De groep die de virtuele machines bevat met de SQL Server exemplaren. 
   |**Status test** |Kies de test die u hebt gemaakt.
   |**Sessie persistentie** |None
   |**Time-out voor inactiviteit (minuten)** |Standaard (4)
   |**Zwevend IP (Direct Server Return)** | Ingeschakeld

Herhaal deze stappen voor de load balancer voor de andere beschikbaarheids groepen die deel uitmaken van de gedistribueerde beschikbaarheids groepen.

Als u de toegang met een Azure-netwerk beveiligings groep wilt beperken, moet u ervoor zorgen dat de regels voor toestaan de back-end-SQL Server VM-IP-adressen bevatten en de load balancer zwevende IP-adressen voor de AG-listener en het IP-adres van het cluster basis, indien van toepassing.

## <a name="next-steps"></a>Volgende stappen

- [Een SQL Server always on-beschikbaarheids groep configureren op virtuele machines van Azure in verschillende regio's](virtual-machines-windows-portal-sql-availability-group-dr.md)
