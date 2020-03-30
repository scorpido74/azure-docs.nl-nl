---
title: Een Oracle-database ontwerpen en implementeren op Azure | Microsoft Documenten
description: Een Oracle-database ontwerpen en implementeren in uw Azure-omgeving.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: c2c2d1a9affe13d485bfeef52c781ed259b53bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70100118"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Een Oracle-database ontwerpen en implementeren in Azure

## <a name="assumptions"></a>Veronderstellingen

- U bent van plan een Oracle-database te migreren van on-premises naar Azure.
- U beschikt over het [Diagnostisch Pakket](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) voor de Oracle Database die u wilt migreren
- U hebt inzicht in de verschillende statistieken in Oracle AWR-rapporten.
- Je hebt een basislijn inzicht in de prestaties van applicaties en het gebruik van het platform.

## <a name="goals"></a>Doelstellingen

- Meer informatie over het optimaliseren van uw Oracle-implementatie in Azure.
- Bekijk de opties voor het afstemmen van prestaties voor een Oracle-database in een Azure-omgeving.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>De verschillen tussen een on-premises en Azure-implementatie 

Hieronder volgen enkele belangrijke dingen om in gedachten te houden wanneer u on-premises toepassingen migreert naar Azure. 

Een belangrijk verschil is dat in een Azure-implementatie resources zoals VM's, schijven en virtuele netwerken worden gedeeld tussen andere clients. Bovendien kunnen resources worden beperkt op basis van de vereisten. In plaats van zich te richten op het voorkomen van falen (MTBF), is Azure meer gericht op het overleven van de fout (MTTR).

In de volgende tabel worden enkele verschillen weergegeven tussen een on-premises implementatie en een Azure-implementatie van een Oracle-database.

> 
> |  | **Lokale implementatie** | **Azure-implementatie** |
> | --- | --- | --- |
> | **Networking** |LAN/WAN  |SDN (software-defined networking)|
> | **Beveiligingsgroep** |Hulpprogramma's voor IP/poortbeperking |[Netwerkbeveiligingsgroep (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Veerkracht** |MTBF (gemiddelde tijd tussen storingen) |MTTR (gemiddelde tijd tot herstel)|
> | **Gepland onderhoud** |Patchen/upgraden|[Beschikbaarheidssets](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (patching/upgrades beheerd door Azure) |
> | **Resource** |Toegewezen  |Gedeeld met andere klanten|
> | **Regio's** |Datacenters |[Regioparen](https://docs.microsoft.com/azure/virtual-machines/windows/regions#region-pairs)|
> | **Opslag** |SAN/fysieke schijven |[Azure-beheerde opslag](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Schaal** |Verticale schaal |Horizontaal schalen|


### <a name="requirements"></a>Vereisten

- Bepaal de databasegrootte en groeisnelheid.
- Bepaal de IOPS-vereisten, die u schatten op basis van Oracle AWR-rapporten of andere hulpprogramma's voor netwerkbewaking.

## <a name="configuration-options"></a>Configuratie-opties

Er zijn vier potentiële gebieden die u afstemmen om de prestaties in een Azure-omgeving te verbeteren:

- Grootte van de virtuele machine
- Netwerkdoorvoer
- Schijftypen en -configuraties
- Instellingen voor schijfcache

### <a name="generate-an-awr-report"></a>Een AWR-rapport genereren

Als u een bestaande Oracle-database hebt en van plan bent te migreren naar Azure, hebt u verschillende opties. Als u het [Diagnostisch pakket](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) voor uw Oracle-instanties hebt, u het Oracle AWR-rapport uitvoeren om de statistieken te krijgen (IOPS, Mbps, GiBs, enzovoort). Kies vervolgens de VM op basis van de statistieken die u hebt verzameld. U ook contact opnemen met uw infrastructuurteam om vergelijkbare informatie te krijgen.

U overwegen uw AWR-rapport uit te voeren tijdens zowel reguliere als piekworkloads, zodat u vergelijken. Op basis van deze rapporten u de VM's vergroten op basis van de gemiddelde werkbelasting of de maximale werkbelasting.

Hieronder volgt een voorbeeld van het genereren van een AWR-rapport (Uw AWR-rapporten genereren met uw Oracle Enterprise Manager, als uw huidige installatie er een heeft):

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Belangrijkste statistieken

Hieronder volgen de statistieken die u verkrijgen uit het AWR-rapport:

- Totaal aantal kernen
- CPU kloksnelheid
- Totaal geheugen in GB
- CPU-gebruik
- Pieksnelheid van gegevensoverdracht
- I/O-wijzigingen (lezen/schrijven)
- Logboeksnelheid opnieuw doen (MBP's)
- Netwerkdoorvoer
- Netwerklatentiesnelheid (laag/hoog)
- Databasegrootte in GB
- Bytes ontvangen via SQL*Net van/naar client

### <a name="virtual-machine-size"></a>Grootte van de virtuele machine

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Schat vm-grootte op basis van CPU-, geheugen- en I/O-gebruik uit het AWR-rapport

Een ding dat je zou kunnen kijken is de top vijf getimede voorgrond gebeurtenissen die aangeven waar het systeem knelpunten zijn.

In het volgende diagram staat bijvoorbeeld de synchronisatie van het logboekbestand bovenaan. Het geeft het aantal wachttijden aan dat nodig is voordat de LGWR de logbuffer naar het logboekbestand opnieuw schrijft. Deze resultaten geven aan dat beter presterende opslag of schijven nodig zijn. Daarnaast toont het diagram ook het aantal CPU(cores) en de hoeveelheid geheugen.

![Schermafbeelding van de rapportpagina van AWR](./media/oracle-design/cpu_memory_info.png)

In het volgende diagram ziet u het totale I/O van lezen en schrijven. Er waren 59 GB gelezen en 247,3 GB geschreven tijdens de tijd van het rapport.

![Schermafbeelding van de rapportpagina van AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Kies een VM

Op basis van de informatie die u uit het AWR-rapport hebt verzameld, is de volgende stap het kiezen van een VM van een vergelijkbare grootte die aan uw vereisten voldoet. U vindt een lijst met beschikbare VM's in het artikel [Geheugen geoptimaliseerd.](../../linux/sizes-memory.md)

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Fine-tunen van de VM-maatvoering met een soortgelijke VM-serie op basis van de ACU

Nadat u de VM hebt gekozen, moet u aandacht besteden aan de ACU voor de VM. U een andere VM kiezen op basis van de ACU-waarde die beter bij uw vereisten past. Zie [Azure compute unit](https://docs.microsoft.com/azure/virtual-machines/windows/acu)voor meer informatie .

![Schermafbeelding van de pagina ACU-eenheden](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Netwerkdoorvoer

In het volgende diagram ziet u de relatie tussen doorvoer en IOPS:

![Schermafbeelding van doorvoer](./media/oracle-design/throughput.png)

De totale netwerkdoorvoer wordt geschat op basis van de volgende informatie:
- SQL*Netverkeer
- MBps x aantal servers (uitgaande stream zoals Oracle Data Guard)
- Andere factoren, zoals toepassingsreplicatie

![Schermafbeelding van de SQL*Net-doorvoer](./media/oracle-design/sqlnet_info.png)

Op basis van uw netwerkbandbreedtevereisten zijn er verschillende gatewaytypen waaruit u kiezen. Deze omvatten basic, VpnGw en Azure ExpressRoute. Zie de prijspagina van de [VPN-gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h)voor meer informatie.

**Aanbevelingen**

- De netwerklatentie is hoger in vergelijking met een on-premises implementatie. Het verminderen van netwerkretouren kan de prestaties aanzienlijk verbeteren.
- Om retouren te verminderen, consolideert u toepassingen met hoge transacties of 'spraakzame' apps op dezelfde virtuele machine.
- Gebruik virtuele machines met [versnelde netwerken](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) voor betere netwerkprestaties.
- Voor bepaalde Linux distrubutions, overweeg dan het inschakelen van [TRIM / UNMAP ondersteuning](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm#trimunmap-support).
- Installeer [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) op een aparte virtuele machine.
- Grote pagina's zijn standaard niet ingeschakeld op linux. Overweeg grote pagina's in te schakelen en in te stellen `use_large_pages = ONLY` op de Oracle DB. Dit kan helpen om de prestaties te verhogen. Meer informatie is [hier](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390)te vinden.

### <a name="disk-types-and-configurations"></a>Schijftypen en -configuraties

- *Standaardbesturingssysteemschijven:* deze schijftypen bieden permanente gegevens en caching. Ze zijn geoptimaliseerd voor os-toegang bij het opstarten en zijn niet ontworpen voor transactionele of datawarehouse (analytische) workloads.

- *Onbeheerde schijven:* Met deze schijftypen beheert u de opslagaccounts die de VHD-bestanden (virtual hard disk) opslaan die overeenkomen met uw VM-schijven. VHD-bestanden worden opgeslagen als paginablobs in Azure-opslagaccounts.

- *Beheerde schijven:* Azure beheert de opslagaccounts die u voor uw VM-schijven gebruikt. U geeft het schijftype (premium of standaard) en de grootte van de schijf op die u nodig hebt. Azure maakt en beheert de schijf voor u.

- *Premium opslagschijven*: Deze schijftypen zijn het meest geschikt voor productieworkloads. Premium-opslag ondersteunt VM-schijven die kunnen worden gekoppeld aan vm's uit specifieke grootte-reeksen, zoals VM's uit de DS-, DSv2-, GS- en F-serie. De premium schijf wordt geleverd met verschillende formaten, en je kiezen tussen schijven variërend van 32 GB tot 4.096 GB. Elke schijfgrootte heeft zijn eigen prestatiespecificaties. Afhankelijk van uw toepassingsvereisten u een of meer schijven aan uw vm koppelen.

Wanneer u een nieuwe beheerde schijf maakt vanuit de portal, u het **accounttype** kiezen voor het type schijf dat u wilt gebruiken. Houd er rekening mee dat niet alle beschikbare schijven worden weergegeven in het vervolgkeuzemenu. Nadat u een bepaald VM-formaat hebt gekozen, worden in het menu alleen de beschikbare PREMIUM-opslagSSKU's weergegeven die zijn gebaseerd op die VM-grootte.

![Schermafbeelding van de beheerde schijfpagina](./media/oracle-design/premium_disk01.png)

Nadat u uw opslag op een virtuele machine hebt geconfigureerd, u de schijven laden voordat u een database maakt. Als u de I/O-snelheid zowel in termen van latentie als doorvoer kennen, u bepalen of de VM's de verwachte doorvoer ondersteunen met latentiedoelen.

Er zijn een aantal tools voor het testen van de belasting van toepassingen, zoals Oracle Orion, Sysbench en Fio.

Voer de belastingstest opnieuw uit nadat u een Oracle-database hebt geïmplementeerd. Start uw normale en piekworkloads en de resultaten tonen u de basislijn van uw omgeving.

Het kan belangrijker zijn om de grootte van de opslag op basis van de IOPS-snelheid in plaats van de opslaggrootte. Als de vereiste IOPS bijvoorbeeld 5.000 is, maar u slechts 200 GB nodig hebt, u nog steeds de Premium-schijf van de P30-klasse krijgen, ook al wordt deze geleverd met meer dan 200 GB opslagruimte.

Het IOPS-tarief kan worden verkregen uit het AWR-rapport. Het wordt bepaald door de redo log, fysieke leest, en schrijft tarief.

![Schermafbeelding van de rapportpagina van AWR](./media/oracle-design/awr_report.png)

De grootte van de redo is bijvoorbeeld 12.200.000 bytes per seconde, wat gelijk is aan 11,63 MBP's.
De IOPS is 12.200.000 / 2.358 = 5.174.

Nadat u een duidelijk beeld hebt van de I/O-vereisten, u een combinatie van schijven kiezen die het meest geschikt zijn om aan deze vereisten te voldoen.

**Aanbevelingen**

- Voor gegevenstabelruimte spreidt u de I/O-workload over een aantal schijven met behulp van beheerde opslag of Oracle ASM.
- Als de I/O-blokgrootte toeneemt voor leesintensieve en schrijfintensieve bewerkingen, voegt u meer gegevensschijven toe.
- Verhoog de blokgrootte voor grote opeenvolgende processen.
- Gebruik gegevenscompressie om I/O te verminderen (voor zowel gegevens als indexen).
- Afzonderlijke redo-logboeken, systeem- en temps en ongedaan maken TS op afzonderlijke gegevensschijven.
- Zet geen toepassingsbestanden op standaard schijven van het besturingssysteem (/dev/sda). Deze schijven zijn niet geoptimaliseerd voor snelle VM-opstarttijden en bieden mogelijk geen goede prestaties voor uw toepassing.
- Wanneer u VM's uit de M-serie gebruikt op Premium-opslag, schakelt u [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) in op de schijf redo logs.

### <a name="disk-cache-settings"></a>Instellingen voor schijfcache

Er zijn drie opties voor host caching:

- *ReadOnly*: Alle aanvragen worden in de cache opgeslagen voor toekomstige reads. Alle schrijfbewerkingen worden rechtstreeks naar Azure Blob-opslag uitgevoerd.

- *ReadWrite*: Dit is een "read-ahead" algoritme. De leest en schrijft zijn cached voor toekomstige leest. Niet-schrijfbewerkingen blijven eerst in de lokale cache staan. Het biedt ook de laagste schijflatentie voor lichte workloads. Het gebruik van ReadWrite-cache met een toepassing die de vereiste gegevens niet verwerkt, kan leiden tot gegevensverlies als de VM vastloopt.

- *Geen* (uitgeschakeld): Door deze optie te gebruiken, u de cache omzeilen. Alle gegevens worden overgebracht naar de schijf en blijven bestaan naar Azure Storage. Deze methode geeft u het hoogste I/O-tarief voor I/O-intensieve workloads. U moet ook rekening houden met "transactiekosten".

**Aanbevelingen**

Om de doorvoer te maximaliseren, raden we u aan om te beginnen met **Geen** voor hostcaching. Houd er bij Premium Storage rekening mee dat u de "barrières" moet uitschakelen wanneer u het bestandssysteem monteert met de opties **ReadOnly** of **Geen.** Werk het /etc/fstab-bestand met de UUID bij naar de schijven.

![Schermafbeelding van de beheerde schijfpagina](./media/oracle-design/premium_disk02.png)

- Voor OS-schijven gebruikt u standaard **lees-/schrijfcache.**
- Voor SYSTEEM, TEMP en UNDO gebruiken **Geen** voor caching.
- Voor GEGEVENS gebruikt u **Geen** voor caching. Maar als uw database alleen-lezen of leesintensief is, gebruikt u **Alleen-lezen** caching.

Nadat de instelling van de gegevensschijf is opgeslagen, u de instelling voor hostcache niet wijzigen, tenzij u het station op het os-niveau losmaakt en vervolgens opnieuw monteert nadat u de wijziging hebt aangebracht.

## <a name="security"></a>Beveiliging

Nadat u uw Azure-omgeving hebt ingesteld en geconfigureerd, is de volgende stap het beveiligen van uw netwerk. Hier zijn enkele aanbevelingen:

- *NSG-beleid*: NSG kan worden gedefinieerd door een subnet of NIC. Het is eenvoudiger om de toegang op subnetniveau te beheren, zowel voor beveiliging als force routing voor zaken als applicatiefirewalls.

- *Jumpbox*: Voor veiligere toegang moeten beheerders niet rechtstreeks verbinding maken met de toepassingsservice of -database. Een jumpbox wordt gebruikt als media tussen de beheerdersmachine en Azure-resources.
![Schermafbeelding van de topologiepagina van jumpbox](./media/oracle-design/jumpbox.png)

    De beheerder machine moet bieden IP-beperkte toegang tot de jumpbox alleen. De jumpbox moet toegang hebben tot de applicatie en database.

- *Privénetwerk* (subnetten): We raden u aan de applicatieservice en -database op afzonderlijke subnetten te hebben, zodat een betere controle kan worden ingesteld door het NSG-beleid.


## <a name="additional-reading"></a>Aanvullende lezing

- [Oracle ASM configureren](configure-oracle-asm.md)
- [Oracle Data Guard configureren](configure-oracle-dataguard.md)
- [Oracle Golden Gate configureren](configure-oracle-golden-gate.md)
- [Oracle back-up en herstel](oracle-backup-recovery.md)

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: zeer beschikbare VM's maken](../../linux/create-cli-complete.md)
- [Azure CLI-voorbeelden voor VM-implementatie verkennen](../../linux/cli-samples.md)
