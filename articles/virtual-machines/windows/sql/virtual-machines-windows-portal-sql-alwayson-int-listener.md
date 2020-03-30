---
title: Beschikbaarheidsgroeplisteners & load balancer configureren (Azure-portal)
description: Stapsgewijze instructies voor het maken van een listener voor een groep Always On-beschikbaarheid voor SQL Server in virtuele Azure-machines
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096324"
---
# <a name="configure-a-load-balancer-for-an-availability-group-on-azure-sql-server-vms"></a>Een load balancer configureren voor een beschikbaarheidsgroep op Azure SQL Server VM's
In dit artikel wordt uitgelegd hoe u een load balancer maakt voor een SQL Server Always On-beschikbaarheidsgroep in virtuele Azure-machines die worden uitgevoerd met Azure Resource Manager. Een beschikbaarheidsgroep vereist een load balancer wanneer de SQL Server-exemplaren zich op virtuele Azure-machines bevinden. De load balancer slaat het IP-adres op voor de listener van de beschikbaarheidsgroep. Als een beschikbaarheidsgroep meerdere regio's bestrijkt, heeft elke regio een load balancer nodig.

Als u deze taak wilt voltooien, moet u een SQL Server-beschikbaarheidsgroep hebben geïmplementeerd op virtuele Azure-machines die worden uitgevoerd met Resource Manager. Beide virtuele SQL Server-machines moeten tot dezelfde beschikbaarheidsset behoren. U de [Microsoft-sjabloon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) gebruiken om automatisch de beschikbaarheidsgroep te maken in Resourcebeheer. Met deze sjabloon wordt automatisch een interne load balancer voor u gemaakt. 

Als u dat liever hebt, u [handmatig een beschikbaarheidsgroep configureren.](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

Dit artikel vereist dat uw beschikbaarheidsgroepen al zijn geconfigureerd.  

Gerelateerde onderwerpen zijn:

* [Altijd beschikbaarheidsgroepen configureren in Azure VM (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Een VNet-naar-VNet-verbinding configureren via Azure Resource Manager en PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Door dit artikel te doorlopen, maakt en configureert u een load balancer in de Azure-portal. Nadat het proces is voltooid, configureert u het cluster om het IP-adres van de load balancer te gebruiken voor de listener van de beschikbaarheidsgroep.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>De load balancer maken en configureren in de Azure-portal
Ga in dit gedeelte van de taak als volgt te werk:

1. Maak in de Azure-portal de load balancer en configureer het IP-adres.
2. Configureer de back-endpool.
3. Maak de sonde. 
4. Stel de regels voor het balanceren van de lasten in.

> [!NOTE]
> Als de SQL Server-exemplaren zich in meerdere resourcegroepen en -regio's bevinden, voert u elke stap twee keer uit, eenmaal in elke resourcegroep.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Stap 1: De load balancer maken en het IP-adres configureren
Maak eerst de load balancer. 

1. Open in de Azure-portal de brongroep die de virtuele SQL Server-machines bevat. 

2. Klik in de resourcegroep op **Toevoegen**.

3. Zoek naar **load balancer** en selecteer vervolgens in de zoekresultaten **Load Balancer**, die wordt gepubliceerd door **Microsoft**.

4. Klik op het blade **Load Balancer** op **Maken**.

5. Configureer in het dialoogvenster **Load balancer maken** de load balancer als volgt:

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Een tekstnaam die de load balancer vertegenwoordigt. Bijvoorbeeld **SQLLB**. |
   | **Type** |**Intern**: De meeste implementaties maken gebruik van een interne load balancer, waarmee toepassingen binnen hetzelfde virtuele netwerk verbinding kunnen maken met de beschikbaarheidsgroep.  </br> **Extern**: Hiermee kunnen toepassingen verbinding maken met de beschikbaarheidsgroep via een openbare internetverbinding. |
   | **Virtueel netwerk** |Selecteer het virtuele netwerk waarin de SQL Server-instanties zich bevinden. |
   | **Subnet** |Selecteer het subnet waarin de SQL Server-instanties zich bevinden. |
   | **Toewijzing ip-adres** |**Statisch** |
   | **Privé IP-adres** |Geef een beschikbaar IP-adres op in het subnet. Gebruik dit IP-adres wanneer u een listener in het cluster maakt. Gebruik dit adres voor de `$ILBIP` variabele in een PowerShell-script, later in dit artikel. |
   | **Abonnement** |Als u meerdere abonnementen hebt, wordt dit veld mogelijk weergegeven. Selecteer het abonnement dat u aan deze bron wilt koppelen. Het is normaal gesproken hetzelfde abonnement als alle resources voor de beschikbaarheidsgroep. |
   | **Resourcegroep** |Selecteer de brongroep waarin de SQL Server-instanties zich bevinden. |
   | **Locatie** |Selecteer de Azure-locatie waarin de SQL Server-instanties zich bevinden. |

6. Klik **op Maken**. 

Azure maakt de load balancer. De load balancer behoort tot een specifiek netwerk, subnet, resourcegroep en locatie. Nadat Azure de taak heeft voltooid, controleert u de instellingen van de load balancer in Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Stap 2: De back-endpool configureren
Azure roept de back-end-end pool *backend pool*. In dit geval is de back-endpool de adressen van de twee SQL Server-exemplaren in uw beschikbaarheidsgroep. 

1. Klik in uw resourcegroep op de load balancer die u hebt gemaakt. 

2. Klik **op Instellingen**op **Backend-groepen**.

3. Klik op **Backend-groepen**op **Toevoegen** om een back-endadresgroep te maken. 

4. Typ **in Backend pool toevoegen**onder **Naam**een naam voor de back-endpool.

5. Klik **onder Virtuele machines**op Een virtuele machine **toevoegen**. 

6. Klik **onder Virtuele machines kiezen**op Een **beschikbaarheidsset kiezen**en geef vervolgens de beschikbaarheidsset op waartoe de virtuele SQL Server-machines behoren.

7. Nadat u de beschikbaarheidsset hebt gekozen, klikt u op **De virtuele machines kiezen,** selecteert u de twee virtuele machines die de SQL Server-exemplaren in de beschikbaarheidsgroep hosten en klikt u vervolgens op **Selecteren**. 

8. Klik op **OK** om de bladen te sluiten **voor Virtuele machines kiezen**en **backendpool toevoegen**. 

Azure werkt de instellingen voor de back-end adresgroep bij. Nu heeft uw beschikbaarheidsset een groep van twee SQL Server-exemplaren.

### <a name="step-3-create-a-probe"></a>Stap 3: Een sonde maken
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

> [!NOTE]
> Controleer of de opgegeven poort is geopend op de firewall van beide SQL Server-exemplaren. Voor beide instanties is een binnenkomende regel vereist voor de TCP-poort die u gebruikt. Zie [Firewallregel toevoegen of bewerken voor](https://technet.microsoft.com/library/cc753558.aspx)meer informatie . 
> 
> 

Azure maakt de sonde en gebruikt deze vervolgens om te testen welke SQL Server-instantie de listener heeft voor de beschikbaarheidsgroep.

### <a name="step-4-set-the-load-balancing-rules"></a>Stap 4: De regels voor het balanceren van de lastenafweging instellen
De regels voor taakverdeling configureren hoe de load balancer verkeer doorstuurt naar de SQL Server-instanties. Voor deze load balancer schakelt u direct serverretour in omdat slechts één van de twee SQL Server-exemplaren tegelijkertijd eigenaar is van de bron van de beschikbaarheidsgroeplistener.

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

   > [!NOTE]
   > Mogelijk moet u naar beneden scrollen om alle instellingen weer te geven.
   > 

4. Klik op **OK**. 
5. Azure configureert de regel voor het balanceren van de regel. Nu is de load balancer geconfigureerd om verkeer te routeren naar het SQL Server-exemplaar dat de listener host voor de beschikbaarheidsgroep. 

Op dit moment heeft de resourcegroep een load balancer die verbinding maakt met beide SQL Server-machines. De load balancer bevat ook een IP-adres voor de SQL Server Always On availability group listener, zodat beide machines kunnen reageren op verzoeken om de beschikbaarheidsgroepen.

> [!NOTE]
> Als uw SQL Server-exemplaren zich in twee afzonderlijke regio's bevinden, herhaalt u de stappen in het andere gebied. Elke regio vereist een load balancer. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Het cluster configureren om het IP-adres van de load balancer te gebruiken
De volgende stap is het configureren van de listener op het cluster en het online brengen van de luisteraar. Ga als volgt te werk: 

1. Maak de authenticiteit van de beschikbaarheidsgroep in het failovercluster. 

2. Breng de luisteraar online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Stap 5: De listener van de beschikbaarheidsgroep maken op het failovercluster
In deze stap maakt u handmatig de listener van de beschikbaarheidsgroep in Failover Cluster Manager en SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>De configuratie van de listener verifiëren

Als de clusterbronnen en afhankelijkheden correct zijn geconfigureerd, moet u de listener in SQL Server Management Studio kunnen weergeven. Ga als volgt te werk om de listenerpoort in te stellen:

1. Start SQL Server Management Studio en maak vervolgens verbinding met de primaire replica.

2. Ga naar De beschikbaarheidsgroepen > **beschikbaarheidsgroepen**voor **beschikbaarheid van AlwaysOn voor hoge beschikbaarheid** > **Availability Groups**.  
    U moet nu de listenernaam zien die u hebt gemaakt in Failoverclusterbeheer. 

3. Klik met de rechtermuisknop op de naam van de listener en klik vervolgens op **Eigenschappen**.

4. Geef **in** het vak Poort het poortnummer op voor de listener van de beschikbaarheidsgroep met de $EndpointPort die u eerder hebt gebruikt (1433 was de standaardinstelling) en klik vervolgens op **OK**.

U hebt nu een beschikbaarheidsgroep in virtuele Azure-machines die worden uitgevoerd in de ResourceManager-modus. 

## <a name="test-the-connection-to-the-listener"></a>De verbinding met de listener testen
Test de verbinding als volgt:

1. RDP naar een SQL Server-exemplaar dat zich in hetzelfde virtuele netwerk bevindt, maar niet de eigenaar is van de replica. Deze server kan de andere SQL Server-instantie in het cluster zijn.

2. Gebruik **sqlcmd** utility om de verbinding te testen. In het volgende script wordt bijvoorbeeld een **sqlcmd-verbinding** met de primaire replica gemaakt via de listener met Windows-verificatie:
   
        sqlcmd -S <listenerName> -E

De SQLCMD-verbinding maakt automatisch verbinding met de SQL Server-instantie die de primaire replica host. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Een IP-adres maken voor een extra beschikbaarheidsgroep

Elke beschikbaarheidsgroep maakt gebruik van een aparte listener. Elke luisteraar heeft zijn eigen IP-adres. Gebruik dezelfde load balancer om het IP-adres voor extra luisteraars vast te houden. Nadat u een beschikbaarheidsgroep hebt gemaakt, voegt u het IP-adres toe aan de load balancer en configureert u de listener.

Ga als volgt te werk om een IP-adres toe te voegen aan een load balancer met de Azure-portal:

1. Open in de Azure-portal de brongroep die de load balancer bevat en klik vervolgens op de load balancer. 

2. Klik **onder INSTELLINGEN**op **Ip-pool Frontend**en klik vervolgens op **Toevoegen**. 

3. Wijs **onder Ip-adres frontend**toevoegen een naam toe voor de front-end. 

4. Controleer of het **virtuele netwerk** en het **subnet** hetzelfde zijn als de SQL Server-exemplaren.

5. Stel het IP-adres in voor de luisteraar. 
   
   >[!TIP]
   >U het IP-adres instellen op statisch en een adres typen dat momenteel niet in het subnet wordt gebruikt. U het IP-adres ook dynamisch instellen en de nieuwe front-end IP-pool opslaan. Wanneer u dit doet, wijst de Azure-portal automatisch een beschikbaar IP-adres toe aan de groep. U vervolgens de front-end IP-groep opnieuw openen en de toewijzing wijzigen in statisch. 

6. Sla het IP-adres op voor de luisteraar. 

7. Voeg een statussonde toe met de volgende instellingen:

   |Instelling |Waarde
   |:-----|:----
   |**Naam** |Een naam om de sonde te identificeren.
   |**Protocol** |TCP
   |**Poort** |Een ongebruikte TCP-poort, die beschikbaar moet zijn op alle virtuele machines. Het kan niet worden gebruikt voor een ander doel. Geen twee luisteraars kunnen dezelfde sondepoort gebruiken. 
   |**Interval** |De hoeveelheid tijd tussen sondepogingen. Gebruik de standaardinstelling (5).
   |**Ongezonde drempelwaarde** |Het aantal opeenvolgende drempelwaarden dat moet mislukken voordat een virtuele machine als ongezond wordt beschouwd.

8. Klik op **OK** om de sonde op te slaan. 

9. Maak een regel voor het balanceren van de lastenverdeling. Klik **op Taakverdelingsregels**en klik vervolgens op **Toevoegen**.

10. Configureer de nieuwe regel voor taakverdeling met de volgende instellingen:

    |Instelling |Waarde
    |:-----|:----
    |**Naam** |Een naam om de regel voor het balanceren van de last te identificeren. 
    |**Frontend IP-adres** |Selecteer het IP-adres dat u hebt gemaakt. 
    |**Protocol** |TCP
    |**Poort** |Gebruik de poort die de SQL Server-exemplaren gebruiken. Een standaardinstantie gebruikt poort 1433, tenzij u deze hebt gewijzigd. 
    |**Backend-poort** |Gebruik dezelfde waarde als **Poort**.
    |**Backend pool** |De groep die de virtuele machines bevat met de SQL Server-exemplaren. 
    |**Gezondheidssonde** |Kies de sonde die u hebt gemaakt.
    |**Sessiepersistentie** |Geen
    |**Niet-actieve time-out (minuten)** |Standaard (4)
    |**Zwevend IP (direct server retour)** | Ingeschakeld

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>De beschikbaarheidsgroep configureren om het nieuwe IP-adres te gebruiken

Als u het configureren van het cluster wilt voltooien, herhaalt u de stappen die u hebt gevolgd toen u de eerste beschikbaarheidsgroep maakte. Dat wil zeggen, configureer het [cluster om het nieuwe IP-adres te gebruiken.](#configure-the-cluster-to-use-the-load-balancer-ip-address) 

Nadat u een IP-adres voor de listener hebt toegevoegd, configureert u de groep extra beschikbaarheid als volgt: 

1. Controleer of de sondepoort voor het nieuwe IP-adres is geopend op beide virtuele SQL Server-machines. 

2. [Voeg in Clusterbeheer het toegangspunt voor de client toe.](#addcap)

3. [Configureer de IP-bron voor de beschikbaarheidsgroep](#congroup).

   >[!IMPORTANT]
   >Wanneer u het IP-adres maakt, gebruikt u het IP-adres dat u aan de load balancer hebt toegevoegd.  

4. [Maak de SQL Server-bron voor beschikbaarheid afhankelijk van het clienttoegangspunt](#dependencyGroup).

5. [Maak de bron van het toegangspunt van de client afhankelijk van het IP-adres](#listname).
 
6. [Stel de clusterparameters in PowerShell in](#setparam).

Nadat u de beschikbaarheidsgroep hebt geconfigureerd om het nieuwe IP-adres te gebruiken, configureert u de verbinding met de listener. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Regel voor taakverdeling toevoegen voor de groep gedistribueerde beschikbaarheid

Als een beschikbaarheidsgroep deelneemt aan een groep gedistribueerde beschikbaarheid, heeft de load balancer een aanvullende regel nodig. Met deze regel wordt de poort opgeslagen die wordt gebruikt door de listener van de gedistribueerde beschikbaarheidsgroep.

>[!IMPORTANT]
>Deze stap is alleen van toepassing als de beschikbaarheidsgroep deelneemt aan een [groep gedistribueerde beschikbaarheid](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Maak op elke server die deelneemt aan de groep gedistribueerde beschikbaarheid een inkomende regel voor de TCP-poort voor gedistribueerde beschikbaarheidsgroeplistener. In veel voorbeelden maakt documentatie gebruik van 5022. 

1. Klik in de Azure-portal op de load balancer en klik op **Taakverdelingsregels**en klik vervolgens op **+Toevoegen**. 

1. Maak de regel voor het balanceren van de lastenmet de volgende instellingen:

   |Instelling |Waarde
   |:-----|:----
   |**Naam** |Een naam om de regel voor het balanceren van de regel voor de gedistribueerde beschikbaarheid te identificeren. 
   |**Frontend IP-adres** |Gebruik hetzelfde frontend IP-adres als de beschikbaarheidsgroep.
   |**Protocol** |TCP
   |**Poort** |5022 - De poort voor de [endpointlistener voor gedistribueerde beschikbaarheidsgroepen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Kan elke beschikbare poort zijn.  
   |**Backend-poort** | 5022 - Gebruik dezelfde waarde als **Poort**.
   |**Backend pool** |De groep die de virtuele machines bevat met de SQL Server-exemplaren. 
   |**Gezondheidssonde** |Kies de sonde die u hebt gemaakt.
   |**Sessiepersistentie** |Geen
   |**Niet-actieve time-out (minuten)** |Standaard (4)
   |**Zwevend IP (direct server retour)** | Ingeschakeld

Herhaal deze stappen voor de load balancer op de andere beschikbaarheidsgroepen die deelnemen aan de gedistribueerde beschikbaarheidsgroepen.

Als u de toegang beperkt met een Azure Network Security Group, moet u ervoor zorgen dat de regels voor toestaan de IP-adressen van de backend SQL Server VM bevatten en de zwevende IP-adressen van de load balancer voor de AG-listener en het IP-adres van de clusterkern, indien van toepassing.

## <a name="next-steps"></a>Volgende stappen

- [Een SQL Server Always On-beschikbaarheidsgroep configureren op virtuele Azure-machines in verschillende regio's](virtual-machines-windows-portal-sql-availability-group-dr.md)
