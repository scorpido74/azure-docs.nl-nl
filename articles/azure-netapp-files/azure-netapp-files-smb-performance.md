---
title: Veelgestelde vragen over de prestaties van het MKB voor Azure NetApp-bestanden| Microsoft Documenten
description: Beantwoordt veelgestelde vragen over de prestaties van het MKB voor Azure NetApp-bestanden.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460446"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Veelgestelde vragen over de prestaties van het MKB voor Azure NetApp-bestanden

In dit artikel worden veelgestelde vragen (veelgestelde vragen) beantwoord over aanbevolen smb-prestaties voor Azure NetApp-bestanden.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Is SMB Multichannel ingeschakeld in Mkb-aandelen? 

Ja, SMB Multichannel is standaard ingeschakeld, een wijziging die begin januari 2020 is ingevoerd. Alle SMB-aandelen die vooraf dateren van bestaande SMB-volumes hebben de functie ingeschakeld en alle nieuw gemaakte volumes hebben de functie ook ingeschakeld op het moment van creatie. 

Elke SMB-verbinding die is gemaakt voordat de functie-enablement is ingeschakeld, moet worden gereset om te profiteren van de SMB Multichannel-functionaliteit. Als u opnieuw wilt resetten, u het SMB-aandeel loskoppelen en opnieuw verbinden.

## <a name="is-rss-supported"></a>Wordt RSS ondersteund?

Ja, Azure NetApp Files ondersteunt receive-side-scaling (RSS).

Als SMB Multichannel is ingeschakeld, maakt een SMB3-client meerdere TCP-verbindingen met de Azure NetApp Files SMB-server via een NIC (Network Interface Card) die geschikt is voor één RSS-server. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Welke Windows-versies ondersteunen SMB Multichannel?

Windows ondersteunt SMB Multichannel sinds Windows 2012 om de beste prestaties mogelijk te maken.  Zie [SMB Multichannel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) implementeren en [de basisprincipes van SMB Multichannel](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) voor meer informatie. 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Ondersteunt mijn Azure virtuele machine RSS?

Als u wilt zien of uw Azure virtual `Get-SmbClientNetworkInterface` machine NIC's `RSS Capable`RSS ondersteunen, voert u de opdracht als volgt uit en controleert u het veld: 

![RSS-ondersteuning voor azure virtuele machine](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Ondersteunt Azure NetApp Files SMB Direct?

Nee, Azure NetApp Files biedt geen ondersteuning voor SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Wat is het voordeel van SMB Multichannel? 

Met de SMB Multichannel-functie kan een SMB3-client een pool van verbindingen tot stand brengen via één netwerkinterfacekaart (NIC) of meerdere NIC's en deze gebruiken om aanvragen voor één SMB-sessie te verzenden. SMB1 en SMB2 vereisen daarentegen dat de client één verbinding tot stand moet brengen en al het MKB-verkeer voor een bepaalde sessie over die verbinding moet verzenden. Deze enkele verbinding beperkt de algehele protocolprestaties die vanuit één client kunnen worden bereikt.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Moet ik meerdere NIC's configureren op mijn client voor SMB?

Nee. De SMB-client komt overeen met het AANTAL NIC's dat door de SMB-server wordt geretourneerd.  Elk opslagvolume is toegankelijk vanaf één en slechts één opslageindpunt.  Dat betekent dat slechts één NIC zal worden gebruikt voor een bepaalde SMB relatie.  

Zoals de `Get-SmbClientNetworkInterace` output van onderstaande shows laat zien, heeft de virtuele machine twee netwerkinterfaces - 15 en 12.  Zoals hieronder onder `Get-SmbMultichannelConnection`de opdracht wordt weergegeven , ook al zijn er twee NICS die geschikt zijn voor RSS, wordt alleen interface 12 gebruikt in verband met het SMB-aandeel; interface 15 is niet in gebruik.

![NICS die geschikt is voor RSS](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Wordt NIC Teaming ondersteund in Azure?

NIC Teaming wordt niet ondersteund in Azure. Hoewel meerdere netwerkinterfaces worden ondersteund op virtuele Azure-machines, vertegenwoordigen ze een logische in plaats van een fysieke constructie. Als zodanig bieden ze geen fouttolerantie.  Ook wordt de bandbreedte die beschikbaar is voor een Virtuele Azure-machine berekend voor de machine zelf en niet voor een afzonderlijke netwerkinterface.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Hoe ziet de prestatie eruit voor SMB Multichannel?

De volgende tests en grafieken tonen de kracht van SMB Multichannel op workloads met één instantie.

### <a name="random-io"></a>Willekeurige I/O  

Met SMB Multichannel uitgeschakeld op de klant, pure 8-KiB lees-en schrijftests werden uitgevoerd met behulp van FIO en een 40-GiB werkset.  Het aandeel smb werd losgekoppeld tussen elke test, waarbij het aantal smb-clientverbindingen`4``8`per`16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`RSS-netwerkinterface-instellingen van `1`, , , werd verbroken. Uit de tests blijkt `4` dat de standaardinstelling van voldoende is voor i/o-intensieve workloads; het verhogen `8` `16` naar en had geen effect. 

De `netstat -na | findstr 445` opdracht bewees dat extra verbindingen `1` met `4` `8` verhogingen `16`van aan en aan werden gevestigd.  Vier CPU-cores werden volledig gebruikt voor SMB tijdens elke test, zoals bevestigd door de perfmon `Per Processor Network Activity Cycles` statistiek (niet opgenomen in dit artikel.)

![Willekeurige I/O-tests](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

De virtuele Azure-machine heeft geen invloed op de I/O-limieten voor SMB-opslag (noch NFS.  Zoals hieronder wordt weergegeven, heeft het D16-instantietype een beperkte snelheid van 32.000 voor IOPS met opslag in cache en 25.600 voor IOPS zonder cache.  Echter, de bovenstaande grafiek toont aanzienlijk meer I / O over SMB.

![Willekeurige I/O-vergelijking](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Sequentiële IO 

Tests vergelijkbaar met de hierboven beschreven willekeurige I/O-tests werden uitgevoerd met 64-KiB sequentiële I/O. Hoewel de toename van het aantal clientverbindingen per RSS-netwerkinterface voorbij 4' geen merkbaar effect had op willekeurige I/O, geldt hetzelfde niet voor sequentiële I/O. Zoals de volgende grafiek laat zien, wordt elke verhoging gekoppeld aan een overeenkomstige toename van de leesdoorvoer. De schrijfdoorvoer bleef vlak vanwege beperkingen op de netwerkbandbreedte die Azure voor elk type type/grootte van het exemplaar heeft geplaatst. 

![Sequentiële I/O-tests](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure stelt netwerktarieflimieten op elk type/formaat van de virtuele machine. De tarieflimiet wordt alleen opgelegd aan uitgaand verkeer. Het aantal NIC's dat aanwezig is op een virtuele machine heeft geen invloed op de totale hoeveelheid bandbreedte die beschikbaar is voor de machine.  Het type D16-instantie heeft bijvoorbeeld een opgelegde netwerklimiet van 8000 Mbps (1.000 MiB/s).  Zoals de sequentiële grafiek hierboven laat zien, is de limiet van invloed op het uitgaande verkeer (schrijft), maar niet op meerkanaals leest.

![Sequentiële I/O-vergelijking](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>Is Accelerated Networking aanbevolen?

Voor maximale prestaties is het raadzaam om [Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) waar mogelijk te configureren. Houd rekening met de volgende overwegingen:  

* De Azure-portal maakt Accelerated Networking standaard mogelijk voor virtuele machines die deze functie ondersteunen.  Andere implementatiemethoden, zoals Ansible en vergelijkbare configuratietools, zijn echter mogelijk niet.  Het niet inschakelen van Accelerated Networking kan de prestaties van een machine in de weg zitten.  
* Als Accelerated Networking niet is ingeschakeld op de netwerkinterface van een virtuele machine vanwege het gebrek aan ondersteuning voor een instantietype of -grootte, blijft deze uitgeschakeld met grotere instantietypen. U moet handmatigingrijpen in die gevallen.

## <a name="are-jumbo-frames-supported"></a>Worden jumboframes ondersteund?

Jumbo frames worden niet ondersteund met Azure virtuele machines.

## <a name="is-smb-signing-supported"></a>Wordt SMB Signing ondersteund? 

Het SMB-protocol vormt de basis voor het delen van bestanden en afdrukken en andere netwerkactiviteiten, zoals extern Windows-beheer. Ter voorkoming van man-in-the-middle-aanvallen waardoor SMB-pakketten in-transit worden gewijzigd, ondersteunt het SMB-protocol de digitale ondertekening van SMB-pakketten. 

SMB Signing wordt ondersteund voor alle SMB-protocolversies die worden ondersteund door Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Wat is de impact van mkb-ondertekening?  

Mkb-ondertekening heeft een schadelijk effect op de prestaties van het MKB. Onder andere mogelijke oorzaken van de prestatiedegradatie, de digitale ondertekening van elk pakket verbruikt extra client-side CPU zoals de perfmon output hieronder laat zien. In dit geval lijkt Core 0 verantwoordelijk voor het MKB, inclusief MKB-ondertekening.  Uit een vergelijking met de niet-multichannel sequentiële leesdoorvoernummers in de vorige sectie blijkt dat SMB Signing de totale doorvoer verlaagt van 875MiB/s naar ongeveer 250 MiB/s. 

![Impact op de prestaties van mkb-ondertekening](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Volgende stappen  

- [Veelgestelde vragen over Azure NetApp-bestanden](azure-netapp-files-faqs.md)
- Zie de [Azure NetApp Files: Managed Enterprise File Shares voor SMB-workloads](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) over het gebruik van SMB-bestandsshares met Azure NetApp-bestanden.
