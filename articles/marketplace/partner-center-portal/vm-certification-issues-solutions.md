---
title: 'Certificering van virtuele machines: problemen en oplossingen'
description: In dit artikel worden veelvoorkomende fout berichten voor VM-installatie kopieën uitgelegd. Daarnaast worden verwante oplossingen besproken
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: v-miegge
ms.author: v-krmall
ms.date: 06/16/2020
ms.openlocfilehash: e73ff612aa07016f69c717a74678d5d1923e87b8
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120650"
---
# <a name="issues-and-solutions-during-virtual-machine-certification"></a>Problemen en oplossingen tijdens de certificering van virtuele machines 

Wanneer u de installatie kopie van de virtuele machine (VM) publiceert naar Azure Marketplace, valideert het Azure-team deze om te zorgen voor de opstart baarheid, beveiliging en Azure-compatibiliteit. Als een van de tests van hoge kwaliteit mislukt, mislukt de publicatie en wordt er een fout bericht weer gegeven waarin het probleem wordt beschreven.

In dit artikel worden veelvoorkomende fout berichten voor het publiceren van VM-installatie kopieën en gerelateerde oplossingen uitgelegd.

> [!NOTE]
> Als u vragen of feedback over verbeteringen hebt, neemt u contact op met het [partner centrum-ondersteuning](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="approved-base-image"></a>Goedgekeurde basis installatie kopie

Wanneer u een aanvraag indient om uw installatie kopie opnieuw te publiceren met updates, kan de test voor de verificatie van het onderdeel nummer mislukken. Als dit mislukt, wordt uw installatie kopie niet goedgekeurd.

Deze fout treedt op wanneer u een basis installatie kopie gebruikt die deel uitmaakt van een andere uitgever en u de installatie kopie hebt bijgewerkt. In deze situatie is het niet toegestaan om uw installatie kopie te publiceren.

U kunt dit probleem oplossen door de installatie kopie op te halen uit Azure Marketplace en er wijzigingen in aan te brengen. Raadpleeg voor meer informatie de volgende artikelen:

- [Linux-installatie kopieën](../../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Windows-installatie kopieën](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)

## <a name="vm-extension-failure"></a>VM-extensie fout

Controleer of de installatie kopie VM-extensies ondersteunt.

Ga als volgt te werk om VM-extensies in te scha kelen:

1. Selecteer uw virtuele Linux-machine.
1. Ga naar **instellingen voor diagnostische gegevens**.
1. Schakel basis matrices in door het **opslag account**bij te werken.
1. Selecteer **Opslaan**.

   ![Bewaking op gastniveau inschakelen](./media/vm-certification-issues-solutions-1.png)

Ga als volgt te werk om te controleren of de VM-extensies correct zijn geactiveerd:

1. Selecteer op de VM het tabblad **VM-extensies** en controleer de status van de **Linux Diagnostics-extensie**.
    * Als de status is *ingericht*, wordt de test case door gegeven.  
    * Als de status van de *inrichting mislukt*is, is het mogelijk dat de uitbrei dingen van de aanvraag zijn mislukt en dat u de gereduceerde vlag moet instellen.

      ![Scherm opname die laat zien dat het inrichten is geslaagd](./media/vm-certification-issues-solutions-2.png)

      Als de extensie van de virtuele machine mislukt, raadpleegt u de [Diagnostische Linux-extensie gebruiken om metrische gegevens en logboeken te controleren](../../virtual-machines/extensions/diagnostics-linux.md) . Als u niet wilt dat de VM-extensie wordt ingeschakeld, neemt u contact op met het ondersteunings team en vraagt u deze uit te scha kelen.

## <a name="vm-provisioning-issue"></a>Probleem met het inrichten van de VM

Controleer of u het VM-inrichtings proces zorgvuldig hebt gevolgd voordat u uw aanbieding verzendt. Zie [certificering van installatie kopieën van virtuele Azure-machines](azure-vm-image-certification.md)om de JSON-indeling voor het inrichten van de virtuele machine weer te geven.

Het inrichtings probleem kan de volgende fout scenario's omvatten:

|Scenario|Fout|Reden|Oplossing|
|---|---|---|---|
|1|Ongeldige virtuele harde schijf (VHD)|Als de opgegeven cookie waarde in de voet tekst van de VHD onjuist is, wordt de VHD als ongeldig beschouwd.|Maak de installatie kopie opnieuw en verzend de aanvraag.|
|2|Ongeldig BLOB-type|Het inrichten van de VM is mislukt omdat het gebruikte blok een BLOB-type in plaats van een pagina Type is.|Maak de installatie kopie opnieuw en verzend de aanvraag.|
|3|Time-out voor inrichten of niet juist gegeneraliseerd|Er is een probleem met VM-generalisatie.|Maak de installatie kopie opnieuw met generalisatie en verzend de aanvraag.|

> [!NOTE]
> Zie voor meer informatie over VM-generalisatie:
> - [Linux-documentatie](create-azure-vm-technical-asset.md#generalize-the-image)
> - [Windows-documentatie](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="software-compliance-for-windows"></a>Software compatibiliteit voor Windows

Als uw Windows-installatie kopie wordt geweigerd vanwege een probleem met de software-naleving, hebt u mogelijk een Windows-installatie kopie gemaakt met het geïnstalleerde SQL Server-exemplaar in plaats van de relevante SQL-versie basis installatie kopie van Azure Marketplace.

Maak geen eigen Windows-installatie kopie waarop SQL Server is geïnstalleerd. Gebruik in plaats daarvan de goedgekeurde SQL-basis installatie kopieën (Enter prise/Standard/web) van Azure Marketplace.

Neem contact op met het ondersteunings team voor een eerdere goed keuring als u probeert Visual Studio of een product met Office-licenties te installeren.

Zie [technische assets van uw virtuele Azure-machines maken](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)voor meer informatie over het selecteren van een goedgekeurde basis.

## <a name="tool-kit-test-case-execution-failed"></a>Uitvoering van test case van Tool Kit is mislukt

Met de micro soft-certificerings Toolkit kunt u test cases uitvoeren en controleren of uw VHD of installatie kopie compatibel is met de Azure-omgeving.

Down load de [micro soft-certificerings Toolkit](azure-vm-image-certification.md).

## <a name="linux-test-cases"></a>Linux-test cases

De volgende tabel geeft een lijst van de Linux-test cases die door de Toolkit worden uitgevoerd. Test validatie wordt vermeld in de beschrijving.

|Scenario|Testcase|Beschrijving|
|---|---|---|
|1|Bash-geschiedenis|Bash-geschiedenis bestanden moeten worden gewist voordat u de VM-installatie kopie maakt.|
|2|Versie van Linux-agent|Azure Linux Agent 2.2.41 of hoger moet worden geïnstalleerd.|
|3|Vereiste kernel-para meters|Verifieert of de volgende kernel-para meters zijn ingesteld: <br>console = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300|
|4|Partitie wisselen op besturingssysteem schijf|Hiermee wordt gecontroleerd of wisselende partities niet zijn gemaakt op de besturingssysteem schijf.|
|5|Basis partitie op besturingssysteem schijf|Maak een enkele hoofd partitie voor de besturingssysteem schijf.|
|6|OpenSSL-versie|De OpenSSL-versie moet v 0.9.8 of hoger zijn.|
|7|Python-versie|Python-versie 2,6 of hoger wordt sterk aanbevolen.|
|8|Interval van client Alive|Stel ' ClientAliveInterval in op 180. Op basis van de toepassing kan deze worden ingesteld op 30 tot 235. Als u de SSH inschakelt voor uw eind gebruikers, moet deze waarde worden ingesteld zoals wordt uitgelegd.|
|9|Architectuur van besturingssysteem|Alleen 64-bits besturingssystemen worden ondersteund.|
|10|Automatisch bijwerken|Hiermee wordt aangegeven of automatisch bijwerken van de Linux-agent is ingeschakeld.|

### <a name="common-errors-found-while-executing-previous-test-cases"></a>Veelvoorkomende fouten tijdens het uitvoeren van de vorige test cases

De volgende tabel bevat algemene fouten die zijn gevonden tijdens het uitvoeren van eerdere test cases:
 
|Scenario|Testcase|Fout|Oplossing|
|---|---|---|---|
|1|Test case voor Linux-agent versie|De minimale versie van de Linux-agent is 2,241 of hoger. Deze vereiste is verplicht sinds 1 mei 2020.|De installatie kopie moet worden bijgewerkt met de vereiste versie om [de aanvraag](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)in te dienen.|
|2|Test case voor bash geschiedenis|U ziet een fout als de grootte van de bash-geschiedenis in uw verzonden afbeelding meer dan 1 kilo byte (KB) is. De grootte is beperkt tot 1 KB om ervoor te zorgen dat mogelijk gevoelige gegevens niet worden vastgelegd in uw bash-geschiedenis bestand.|Om dit probleem op te lossen, koppelt u de VHD aan een andere werkende VM en brengt u de gewenste wijzigingen aan (u kunt bijvoorbeeld de *bash* -geschiedenis bestanden verwijderen) om de grootte te verkleinen tot minder dan of gelijk aan 1 KB.|
|3|Vereiste test case voor de kernel-para meter|U ontvangt deze fout melding wanneer de waarde voor de **console** niet is ingesteld op **ttyS0**. Controleer door de volgende opdracht uit te voeren:<br>`cat /proc/cmdline`|Stel de waarde voor de **console** in op **ttyS0**en verzend de aanvraag opnieuw.|
|4|Test case ClientAlive-interval|Als het resultaat van de Toolkit een mislukt resultaat voor deze test case geeft, is er een onjuiste waarde voor **' ClientAliveInterval**.|Stel de waarde voor **' ClientAliveInterval** in op kleiner dan of gelijk aan 235 en verzend de aanvraag vervolgens opnieuw.|

### <a name="windows-test-cases"></a>Test cases van Windows

De volgende tabel geeft een lijst van de Windows-test cases die de Toolkit moet uitvoeren, samen met een beschrijving van de test validatie:

|Scenario |Testcases|Beschrijving|
|---|---|---|---|
|1|Architectuur van besturingssysteem|Azure ondersteunt alleen 64-bits besturings systemen.|
|2|Afhankelijkheid van gebruikers account|Uitvoering van toepassing mag niet afhankelijk zijn van het Administrator-account.|
|3|Failovercluster|De Windows Server failover clustering-functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|4|IPCONFIGURATION|IPv6 wordt nog niet ondersteund in de Azure-omgeving. De toepassing mag niet afhankelijk zijn van deze functie.|
|5|DHCP|Dynamic Host Configuration Protocol server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|6|Hyper-V|De Hyper-V-Server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|7|Externe toegang|De serverrol voor externe toegang (Direct Access) wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|8|Rights Management Services|Rights Management Services. De server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|9|Windows Deployment Services|Windows Deployment Services. De server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|10|BitLocker-stationsversleuteling|BitLocker-stationsversleuteling wordt niet ondersteund op de vaste schijf van het besturings systeem, maar kan worden gebruikt op gegevens schijven.|
|11|Naam server voor Internet opslag|De functie Internet Storage Name Server wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|12|Multipath I/O|Multipath I/O. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|13|Netwerktaakverdeling|Netwerk taakverdeling. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|14|Peer Name Resolution Protocol|Peer Name Resolution Protocol. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|15|SNMP-services|De functie Services van Simple Network Management Protocol (SNMP) wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|16|Windows Internet Name Service|Windows Internet Name Service. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|17|WLAN-service (Wireless LAN)|Draadloze LAN-service. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|

Als u problemen ondervindt met de voor gaande test cases, raadpleegt u de kolom **Beschrijving** in de tabel voor de oplossing. Als u meer informatie nodig hebt, neemt u contact op met het ondersteunings team. 

## <a name="data-disk-size-verification"></a>Verificatie van de grootte van de gegevens schijf

Als de grootte van een aanvraag die wordt verzonden met de gegevens schijf groter is dan 1023 gigabytes (GB), wordt de aanvraag niet goedgekeurd. Deze regel is van toepassing op Linux en Windows.

Verzend de aanvraag opnieuw met een grootte die kleiner is dan of gelijk is aan 1023 GB.

## <a name="os-disk-size-validation"></a>Validatie van schijf grootte van besturings systeem

Raadpleeg de volgende regels voor beperkingen op de schijf grootte van het besturings systeem. Wanneer u een aanvraag indient, controleert u of de grootte van de besturingssysteem schijf binnen de limiet voor Linux of Windows valt.

|OS|Aanbevolen grootte voor VHD|
|---|---|
|Linux|30 GB tot 1023 GB|
|Windows|30 GB tot 250 GB|

Als Vm's toegang bieden tot het onderliggende besturings systeem, moet u ervoor zorgen dat de VHD groot genoeg is voor de VHD. Omdat schijven niet kunnen worden uitgevouwen zonder uitval tijd, gebruikt u een schijf grootte van 30 GB tot 50 GB.

|VHD-grootte|Werkelijke grootte|Oplossing|
|---|---|---|
|>500 tebibytes (TiB)|N.v.t.|Neem contact op met het ondersteunings team voor een uitzonderings goedkeuring.|
|250-500 TiB|>200 gibibytes (GiB) verschilt van de grootte van de BLOB|Neem contact op met het ondersteunings team voor een uitzonderings goedkeuring.|

> [!NOTE]
> Grotere schijf grootten leiden tot hogere kosten en zullen een vertraging oplopen tijdens de installatie en het replicatie proces. Als gevolg van deze vertraging en kosten kan het ondersteunings team een reden voor de goed keuring van uitzonde ringen zoeken.

## <a name="wannacry-patch-verification-test-for-windows"></a>Verificatie test voor de WannaCry-patch voor Windows

Als u een mogelijke aanval met betrekking tot het WannaCry-virus wilt voor komen, moet u ervoor zorgen dat alle Windows-installatie kopie aanvragen worden bijgewerkt met de nieuwste patch.

Raadpleeg de volgende tabel om de patch versie van Windows Server te controleren voor de details van het besturings systeem en de minimale versie die wordt ondersteund: 

De versie van het installatie kopie bestand kan worden gecontroleerd vanuit `C:\windows\system32\drivers\srv.sys` of `srv2.sys` .

> [!NOTE]
> Windows Server 2019 heeft geen verplichte versie vereisten.

|OS|Versie|
|---|---|
|Windows met 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|NA|

## <a name="sack-vulnerability-patch-verification"></a>Verificatie van beveiligings patch voor de Opzakken

Wanneer u een Linux-installatie kopie verzendt, wordt uw aanvraag mogelijk afgewezen vanwege problemen met de kernel-versie.

Werk de kernel bij met een goedgekeurde versie en verzend de aanvraag opnieuw. U kunt de goedgekeurde versie van de kernel vinden in de volgende tabel. Het versie nummer moet gelijk zijn aan of groter zijn dan het nummer dat hier wordt weer gegeven.

Als uw installatie kopie niet is geïnstalleerd met een van de volgende kernel-versies, werkt u deze bij met de juiste patches. De benodigde goed keuring van het ondersteunings team aanvragen nadat de installatie kopie is bijgewerkt met de volgende vereiste patches:

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|BESTURINGSSYSTEEM familie|Versie|Kernel|
|---|---|---|
|Ubuntu|14,04 LTS|4.4.0-151| 
||14,04 LTS|4.15.0-1049-*-Azure|
||16,04 LTS|4.15.0-1049|
||18,04 LTS|4.18.0-1023|
||18,04 LTS|5.0.0-1025|
||18,10|4.18.0-1023|
||19,04|5.0.0-1010|
||19,04|5.3.0-1004|
|RHEL en Cent OS|6,10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7.4|3.10.0-693.50.3|
||7,5|3.10.0-862.34.2|
||7,6|3.10.0-957.21.3|
||7,7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0-147|
||"7-RAW" (7,6)||
||"7-LVM" (7,6)|3.10.0-957.21.3|
||RHEL-SAP 7,4|NOG TE BEPALEN|
||RHEL-SAP 7,5|NOG TE BEPALEN|
|SLES|SLES11SP4 (inclusief SAP)|3.0.101-108.95.2|
||SLES12SP1 voor SAP|3.12.74-60.64.115.1|
||SLES12SP2 voor SAP|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (kernel-Azure)|
||SLES12SP3 voor SAP|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (kernel-Azure)|
||SLES12SP4 voor SAP|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (kernel-Azure)|
||SLES15 voor SAP|4.12.14-5.30.1 (kernel-Azure)|
||SLES15SP1|4.12.14-5.30.1 (kernel-Azure)|
|Oracle|6,10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0-7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK volgt RHEL hierboven|
||7,6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|CoreOS stabiele 2079.6.0|4.19.43*|
||Bèta 2135.3.1|4.19.50*|
||Alpha-2163.2.1|4.19.50*|
|Debian|Jessie (beveiliging)|3.16.68-2|
||Jessie backports|4.9.168-1 + deb9u3|
||uitrekken (beveiliging)|4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0-18 + deb9u1|
||Buster, sid (stretch backports)|4.19.37-5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>De grootte van de installatie kopie moet in meerdere mega bytes staan

Alle Vhd's op Azure moeten een virtuele grootte hebben die is afgestemd op veelvouden van 1 Mega byte (MB). Als uw VHD niet aan de aanbevolen virtuele grootte voldoet, wordt uw aanvraag mogelijk geweigerd.

Volg de richt lijnen wanneer u van een onbewerkte schijf naar VHD converteert en zorg ervoor dat de onbewerkte schijf grootte een meervoud van 1 MB is. Zie [informatie over niet-goedgekeurde distributies](../../virtual-machines/linux/create-upload-generic.md)voor meer informatie.

## <a name="vm-access-denied"></a>VM-toegang geweigerd

Als u tijdens het uitvoeren van de test cases op de VM problemen ondervindt bij het weigeren van de toegang, kan dit worden veroorzaakt door onvoldoende bevoegdheden om de test cases uit te voeren.

Controleer of de juiste toegang is ingeschakeld voor het account waarop de self-test cases worden uitgevoerd. Als de toegang niet is ingeschakeld, schakelt u deze in om de test cases uit te voeren. Als u geen toegang wilt inschakelen, kunt u de test resultaten met het ondersteunings team delen.

## <a name="download-failure"></a>Fout bij het downloaden
    
Raadpleeg de volgende tabel voor eventuele problemen bij het downloaden van de VM-installatie kopie met behulp van een SAS-URL (Shared Access Signature).

|Scenario|Fout|Reden|Oplossing|
|---|---|---|---|
|1|De blob is niet gevonden|De VHD kan worden verwijderd of verplaatst van de opgegeven locatie.|| 
|2|BLOB in gebruik|De VHD wordt gebruikt door een ander intern proces.|De VHD moet een gebruikte status hebben wanneer u deze downloadt met behulp van een SAS-URL.|
|3|Ongeldige SAS-URL|De bijbehorende SAS-URL voor de VHD is onjuist.|Haal de juiste SAS-URL op.|
|4|Ongeldige hand tekening|De bijbehorende SAS-URL voor de VHD is onjuist.|Haal de juiste SAS-URL op.|
|6|Voorwaardelijke HTTP-header|De SAS-URL is ongeldig.|Haal de juiste SAS-URL op.|
|7|Ongeldige naam voor VHD|Controleer of er speciale tekens, zoals een procent teken (%), worden weer geven of aanhalings tekens ("), bestaan in de naam van de VHD.|Wijzig de naam van het VHD-bestand door de speciale tekens te verwijderen.|

## <a name="first-1-mb-partition"></a>Eerste partitie van 1 MB

Wanneer u de VHD verzendt, moet u ervoor zorgen dat de eerste partitie van 1 MB van de VHD leeg is. Als dat niet het geval is, mislukt de aanvraag.

## <a name="default-credentials"></a>Standaard referenties

Zorg er altijd voor dat er geen standaard referenties worden verzonden met de verzonden VHD. Het toevoegen van standaard referenties maakt de VHD kwetsbaarder voor beveiligings Risico's. Maak in plaats daarvan uw eigen referenties wanneer u de VHD verzendt.
  
## <a name="datadisk-mapped-incorrectly"></a>DataDisk onjuist toegewezen

Wanneer een aanvraag wordt ingediend met meerdere gegevens schijven, maar de volg orde ervan niet bestaat, wordt dit beschouwd als een toewijzings probleem. Als er bijvoorbeeld drie gegevens schijven zijn, moet de Nummerings volgorde *0, 1, 2*zijn. Alle andere orders worden behandeld als een toewijzings probleem.

Verzend de aanvraag opnieuw met de juiste volg orde van de gegevens schijven.

## <a name="incorrect-os-mapping"></a>Onjuiste besturingssysteem toewijzing

Wanneer een installatie kopie wordt gemaakt, kan deze worden toegewezen aan of worden toegewezen aan het verkeerde besturingssysteem label. Wanneer u bijvoorbeeld **Windows** als onderdeel van de naam van het besturings systeem selecteert tijdens het maken van de installatie kopie, moet de besturingssysteem schijf alleen met Windows worden geïnstalleerd. Dezelfde vereiste is van toepassing op Linux.

## <a name="vm-not-generalized"></a>De VM is niet gegeneraliseerd

Als alle installatie kopieën die vanuit Azure Marketplace worden gemaakt, opnieuw moeten worden gebruikt, moet de virtuele harde schijf van het besturings systeem worden gegeneraliseerd.

* Voor **Linux**wordt met het volgende proces een virtuele Linux-machine gegeneraliseerd en opnieuw geïmplementeerd als een afzonderlijke VM.

  Voer in het SSH-venster de volgende opdracht in:`sudo waagent -deprovision+user`

* Voor **Windows**kunt u Windows-installatie kopieën generaliseren met behulp van `sysreptool` .

Zie [overzicht van systeem voorbereiding (Sysprep)]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)voor meer informatie over dit hulp programma.

## <a name="datadisk-errors"></a>DataDisk-fouten

Gebruik de volgende tabel voor oplossingen voor fouten die betrekking hebben op de gegevens schijf:

|Fout|Reden|Oplossing|
|---|---|---|
|`DataDisk- InvalidUrl:`|Deze fout kan optreden vanwege een ongeldig aantal dat is opgegeven voor de Logical Unit Number (LUN) wanneer de aanbieding wordt verzonden.|Controleer of de LUN-nummer reeks voor de gegevens schijf zich in het partner centrum bevindt.|
|`DataDisk- NotFound:`|Deze fout kan optreden als er geen gegevens schijf is gevonden op een opgegeven SAS-URL.|Controleer of de gegevens schijf zich bevindt op de SAS-URL die in de aanvraag is opgegeven.|

## <a name="remote-access-issue"></a>Probleem met externe toegang

Als de optie Remote Desktop Protocol (RDP) niet is ingeschakeld voor de Windows-installatie kopie, wordt deze fout weer gegeven. 

Schakel RDP-toegang voor Windows-installatie kopieën in voordat u deze verzendt.

## <a name="next-steps"></a>Volgende stappen

Als u vragen of feedback over verbeteringen hebt, neemt u contact op met het [partner centrum-ondersteuning](https://partner.microsoft.com/support/v2/?stage=1).
