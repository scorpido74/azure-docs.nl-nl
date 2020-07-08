---
title: Veelgestelde vragen over SMB-prestaties voor Azure NetApp Files | Microsoft Docs
description: Antwoorden op veelgestelde vragen over SMB-prestaties voor Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 24b3710861f0ee158619ae9103584dcdb181f3d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79460446"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Veelgestelde vragen over SMB-prestaties voor Azure NetApp Files

In dit artikel vindt u antwoorden op veelgestelde vragen over de aanbevolen procedures voor SMB-prestaties voor Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Is SMB meerdere kanalen ingeschakeld in SMB-shares? 

Ja, SMB meerdere kanalen is standaard ingeschakeld, een wijziging die in eerste januari 2020 is geplaatst. Voor alle SMB-shares die al dating op bestaande SMB-volumes, is de functie ingeschakeld en voor alle nieuw gemaakte volumes is de functie ook ingeschakeld op het moment dat deze wordt gemaakt. 

Elke SMB-verbinding die tot stand is gebracht voordat de functie wordt ingeschakeld, moet opnieuw worden ingesteld om gebruik te kunnen maken van de SMB-functie voor meerdere kanalen. Als u het opnieuw wilt instellen, kunt u de SMB-share loskoppelen en opnieuw verbinding maken.

## <a name="is-rss-supported"></a>Wordt RSS ondersteund?

Ja, Azure NetApp Files ondersteunt het schalen van de ontvangst zijde (RSS).

Als SMB meerdere kanalen is ingeschakeld, brengt een SMB3-client via een netwerk interface kaart (NIC) meer TCP-verbindingen tot stand met de Azure NetApp Files SMB-server. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Welke Windows-versies ondersteunen SMB meerdere kanalen?

SMB meerdere kanalen sinds Windows 2012 wordt ondersteund om de beste prestaties mogelijk te maken.  Zie [SMB meerdere kanalen](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) en [de basis beginselen van SMB meerdere kanalen](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) implementeren voor meer informatie. 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Ondersteunt mijn virtuele Azure-machine RSS?

Als u wilt weten of de Nic's van uw virtuele Azure-machine RSS ondersteunen, voert u de opdracht `Get-SmbClientNetworkInterface` als volgt uit en controleert u het veld `RSS Capable` : 

![RSS-ondersteuning voor virtuele Azure-machines](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Ondersteunt Azure NetApp Files SMB direct?

Nee, Azure NetApp Files biedt geen ondersteuning voor SMB direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Wat is het voor deel van SMB meerdere kanalen? 

Met de functie SMB meerdere kanalen kan een SMB3-client een pool met verbindingen tot stand brengen met een netwerk interface kaart (NIC) of meerdere Nic's en deze gebruiken om aanvragen voor één SMB-sessie te verzenden. In tegens telling tot ontwerp, SMB1 en SMB2 moet de client één verbinding tot stand brengen en alle SMB-verkeer voor een bepaalde sessie over die verbinding verzenden. Deze enkele verbinding beperkt de algehele protocol prestaties die kunnen worden bereikt vanaf één client.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Moet ik meerdere Nic's op mijn client voor SMB configureren?

Nee. De SMB-client komt overeen met het aantal NIC'S dat door de SMB-server wordt geretourneerd.  Elk opslag volume is toegankelijk vanuit één en slechts één opslag eindpunt.  Dit betekent dat er slechts één NIC wordt gebruikt voor een bepaalde SMB-relatie.  

In de uitvoer van `Get-SmbClientNetworkInterace` Hieronder ziet u de virtuele machine met twee netwerk interfaces:--15 en 12.  Zoals hieronder wordt weer gegeven onder de opdracht `Get-SmbMultichannelConnection` , hoewel er twee met RSS geschikte nic's zijn, wordt alleen interface 12 gebruikt in combi natie met de SMB-share; interface 15 wordt niet gebruikt.

![Met RSS compatibele NIC'S](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Worden NIC-teams ondersteund in azure?

NIC-koppeling wordt niet ondersteund in Azure. Hoewel meerdere netwerk interfaces worden ondersteund op virtuele machines van Azure, vertegenwoordigen ze een logische in plaats van een fysieke construct. Daarom bieden ze geen fout tolerantie.  De band breedte die beschikbaar is voor een virtuele machine van Azure wordt ook berekend voor de machine zelf en niet voor afzonderlijke netwerk interfaces.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Wat zijn de prestaties zoals voor SMB meerdere kanalen?

De volgende tests en grafieken tonen de kracht van SMB meerdere kanalen voor workloads met één exemplaar.

### <a name="random-io"></a>Wille keurige I/O  

Als SMB meerdere kanalen is uitgeschakeld op de client, zijn er KiB Lees-en schrijf tests uitgevoerd met behulp van FIO en een 40-GiB werkset.  De SMB-share is losgekoppeld van elke test, met stappen van het aantal SMB-client verbindingen per RSS-netwerk interface-instellingen van,,,, `1` `4` `8` `16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` . De tests laten zien dat de standaard instelling van `4` voldoende is voor I/O-intensieve workloads; er wordt verhoogd naar `8` en `16` heeft geen effect. 

De opdracht heeft `netstat -na | findstr 445` aangetoond dat er extra verbindingen tot stand zijn gebracht met stappen van `1` naar `4` `8` en tot `16` .  Er zijn vier CPU-kernen voor SMB tijdens elke test volledig gebruikt, zoals bevestigd door de prestatie `Per Processor Network Activity Cycles` Statistieken (niet opgenomen in dit artikel).

![Wille keurige I/O-tests](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

De virtuele machine van Azure heeft geen invloed op I/O-opslag limieten voor SMB (of NFS).  Zoals hieronder wordt weer gegeven, heeft het D16-exemplaar type een beperkt aantal van 32.000 voor opslag-IOPS in de cache en 25.600 voor niet-opgeslagen opslag-IOPS.  In het bovenstaande diagram ziet u echter veel meer I/O over SMB.

![Wille keurige I/O-vergelijking](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Sequentiële IO 

Tests die vergelijkbaar zijn met de hierboven beschreven tests voor wille keurige I/O's zijn uitgevoerd met 64-KiB sequentiële I/O. Hoewel de toename van het aantal client verbindingen per RSS-netwerk interface meer dan 4 heeft geen merkbaar effect op wille keurige I/O, is hetzelfde niet van toepassing op opeenvolgende I/O's. Zoals in het volgende diagram wordt weer gegeven, is elke toename gekoppeld aan een overeenkomstige toename van de Lees doorvoer. De schrijf doorvoer bleef plat vanwege de beperkingen van de netwerk bandbreedte die worden geplaatst door Azure voor elk type exemplaar/grootte. 

![Sequentiële I/O-tests](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure plaatst netwerk frequentie limieten voor elk type virtuele machine/grootte. De frequentie limiet wordt alleen opgelegd voor uitgaand verkeer. Het aantal Nic's dat aanwezig is op een virtuele machine heeft geen invloed op de totale hoeveelheid band breedte die beschikbaar is voor de machine.  Het D16-exemplaar type heeft bijvoorbeeld een ingestelde netwerk limiet van 8000 Mbps (1.000 MiB/s).  Zoals in de sequentiële grafiek hierboven wordt weer gegeven, is de limiet van invloed op het uitgaande verkeer (schrijf bewerkingen), maar niet op meerkanaalse Lees bewerkingen.

![Vergelijking van opeenvolgende I/O-bewerkingen](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>Wordt versneld netwerken aanbevolen?

Voor maximale prestaties kunt u het beste zo snel mogelijk [netwerken](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) configureren. Houd rekening met de volgende aandachtspunten:  

* Met de Azure Portal is versneld netwerken standaard ingeschakeld voor virtuele machines die deze functie ondersteunen.  Andere implementatie methoden, zoals Ansible en vergelijk bare configuratie hulpprogramma's, zijn echter niet mogelijk.  Fout bij het inschakelen van versneld netwerken kan de prestaties van een machine hobble.  
* Als versneld netwerken niet zijn ingeschakeld op de netwerk interface van een virtuele machine vanwege een gebrek aan ondersteuning voor een exemplaar type of-grootte, blijft het uitgeschakeld met grotere instantie typen. In die gevallen hebt u hand matige interventie nodig.

## <a name="are-jumbo-frames-supported"></a>Worden Jumbo frames ondersteund?

Jumbo-frames worden niet ondersteund met virtuele machines van Azure.

## <a name="is-smb-signing-supported"></a>Wordt SMB-ondertekening ondersteund? 

Het SMB-protocol biedt de basis voor het delen van bestanden en printers en andere netwerk bewerkingen, zoals extern Windows-beheer. Ter voorkoming van man-in-the-middle-aanvallen waardoor SMB-pakketten in-transit worden gewijzigd, ondersteunt het SMB-protocol de digitale ondertekening van SMB-pakketten. 

SMB-ondertekening wordt ondersteund voor alle SMB-protocol versies die worden ondersteund door Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Wat is de invloed van de prestaties van SMB-ondertekening?  

SMB-ondertekening heeft een deleterious-effect op SMB-prestaties. De digitale ondertekening van elk pakket verbruikt onder andere mogelijke oorzaken van de prestaties van de uitvoering van extra CPU-aan client zijde, zoals hieronder wordt weer gegeven. In dit geval is core 0 verantwoordelijk voor SMB, met inbegrip van SMB-ondertekening.  Een vergelijking met het aantal sequentiële Lees doorvoer van niet-meerkanaals in de vorige sectie laat zien dat SMB-ondertekening de algehele door Voer van 875MiB/s naar ongeveer 250MiB/s vermindert. 

![Invloed op de prestaties van SMB-ondertekening](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Volgende stappen  

- [Veelgestelde vragen over Azure NetApp Files](azure-netapp-files-faqs.md)
- Zie de [Azure NetApp files: beheerde bestands shares voor bedrijven voor SMB-workloads](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) over het gebruik van SMB-bestands shares met Azure NetApp files.
