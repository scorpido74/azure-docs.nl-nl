---
title: Azure backup SAP HANA op basis van moment opnamen van de opslag | Microsoft Docs
description: Er zijn twee belang rijke back-upmogelijkheden voor SAP HANA op virtuele machines van Azure. dit artikel behandelt SAP HANA back-up op basis van opslag momentopnamen
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 8bcfdefa2ea9de12ca6029839a41c91111a5c61c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078605"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Back-up van SAP HANA op basis van opslagmomentopnamen

## <a name="introduction"></a>Inleiding

Dit maakt deel uit van een reeks verwante artikelen in drie gedeelten over SAP HANA back-up. De [back-uphandleiding voor SAP Hana op Azure virtual machines](sap-hana-backup-guide.md) bevat een overzicht en informatie over het aan de slag gaan en [SAP Hana Azure Backup op bestands niveau](sap-hana-backup-file-level.md) wordt de back-upoptie op basis van bestanden beschreven.

Wanneer u een VM-back-upfunctie gebruikt voor een alles-in-één-demo systeem met één exemplaar, kunt u overwegen om een VM-back-up te maken in plaats van HANA-back-ups op het niveau van het besturings systeem te beheren. Een alternatief is om Azure Blob-moment opnamen te maken van kopieën van afzonderlijke virtuele schijven, die zijn gekoppeld aan een virtuele machine en de HANA-gegevens bestanden te houden. Maar een kritiek punt is app-consistentie bij het maken van een back-up of moment opname van een virtuele machine terwijl het systeem actief is. Zie _SAP Hana consistentie van gegevens bij het maken van moment opnamen van opslag_ in de bijbehorende [back-upgids voor artikel voor SAP HANA op Azure virtual machines](sap-hana-backup-guide.md). SAP HANA heeft een functie die ondersteuning biedt voor dit soort opslag momentopnamen.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>Moment opnamen SAP HANA als centraal onderdeel van toepassings consistente back-ups

Er is een functionaliteit in SAP HANA die ondersteuning biedt voor het maken van een opslag momentopname. Er geldt een beperking voor systemen met één container. Scenario's SAP HANA MCS met meer dan één Tenant bieden geen ondersteuning voor dit type SAP HANA database momentopname (Zie [een moment opname van een opslag maken (SAP Hana Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Het werkt als volgt:

- Een opslag momentopname voorbereiden door de moment opname van de SAP HANA te initiëren
- De opslag momentopname (bijvoorbeeld Azure Blob snap shot) uitvoeren
- De moment opname van de SAP HANA bevestigen

![Deze scherm afbeelding laat zien dat een SAP HANA gegevens momentopname kan worden gemaakt via een SQL-instructie](media/sap-hana-backup-storage-snapshots/image011.png)

Deze scherm afbeelding laat zien dat een SAP HANA gegevens momentopname kan worden gemaakt via een SQL-instructie.

![De moment opname wordt ook weer gegeven in de back-upcatalogus in SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

De moment opname wordt ook weer gegeven in de back-upcatalogus in SAP HANA Studio.

![De moment opname wordt op schijf weer gegeven in de SAP HANA data directory](media/sap-hana-backup-storage-snapshots/image013.png)

De moment opname wordt op schijf weer gegeven in de SAP HANA data directory.

Een moet ervoor zorgen dat de bestandssysteem consistentie ook wordt gegarandeerd voordat de moment opname van de opslag wordt uitgevoerd terwijl SAP HANA zich in de modus voor het voorbereiden van moment opnamen bevindt. Zie _SAP Hana consistentie van gegevens bij het maken van moment opnamen van opslag_ in de bijbehorende [back-upgids voor artikel voor SAP HANA op Azure virtual machines](sap-hana-backup-guide.md).

Zodra de moment opname van de opslag is voltooid, is het van cruciaal belang om de moment opname van de SAP HANA te bevestigen. Er is een overeenkomende SQL-instructie om uit te voeren: MOMENT opname van back-upgegevens sluiten (Zie back [-up van back-UPGEGEVENS sluiten moment opname maken en herstellen)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm).

> [!IMPORTANT]
> Bevestig de HANA-moment opname. Als &quot;gevolg van het kopiëren van aan-&quot; schrijven kan SAP Hana extra schijf ruimte nodig hebben in de modus voor het voorbereiden van moment opnamen en het is niet mogelijk om nieuwe back-ups te starten totdat de SAP Hana moment opname is bevestigd.

## <a name="hana-vm-backup-via-azure-backup-service"></a>Gegevens van HANA-VM'S via Azure Backup-Service

De back-upagent van de Azure Backup service is niet beschikbaar voor Linux-Vm's. Daarnaast heeft Linux geen vergelijk bare functionaliteit als Windows met VSS.  Als u Azure backup wilt gebruiken op het niveau van het bestand of de map, kopieert u SAP HANA back-upbestanden naar een Windows-VM en gebruikt u vervolgens de back-upagent. 

Anders is een volledige back-up van de virtuele Linux-machine mogelijk via de Azure Backup-service. Zie [overzicht van de functies in azure backup](../../../backup/backup-introduction-to-azure-backup.md) voor meer informatie.

De Azure Backup-service biedt een optie om een back-up te maken van een virtuele machine en deze te herstellen. Meer informatie over deze service en hoe deze werkt, vindt u in het artikel [uw VM-back-upinfrastructuur plannen in azure](../../../backup/backup-azure-vms-introduction.md).

Er zijn twee belang rijke aandachtspunten op basis van dit artikel:

_&quot;Voor virtuele Linux-machines zijn alleen bestands consistente back-ups mogelijk, omdat Linux geen gelijkwaardig platform heeft voor VSS.&quot;_

_&quot;Toepassingen moeten hun eigen &quot;&quot; mechanisme voor het herstellen van de herstelde gegevens implementeren.&quot;_

Daarom moet u er zeker van zijn dat SAP HANA zich in een consistente status op de schijf bevindt wanneer de back-up wordt gestart. Zie _SAP Hana moment opnamen_ die eerder in het document zijn beschreven. Maar er is een mogelijk probleem wanneer SAP HANA in deze modus voor het voorbereiden van moment opnamen blijft. Zie [een moment opname van een opslag maken (SAP Hana Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) voor meer informatie.

Dat artikel staat voor:

_&quot;Het wordt ten zeerste aanbevolen om een opslag momentopname zo snel mogelijk te bevestigen of te annuleren nadat deze is gemaakt. Terwijl de opslag momentopname wordt voor bereid of gemaakt, worden de relevante gegevens van de moment opname geblokkeerd. Terwijl de gegevens van de moment opname relevant blijven, kunnen er nog steeds wijzigingen in de Data Base worden aangebracht. Door dergelijke wijzigingen worden de relevante gegevens van de geblokkeerde moment opname niet gewijzigd. In plaats daarvan worden de wijzigingen geschreven naar posities in het gegevens gebied dat gescheiden is van de moment opname van de opslag. Wijzigingen worden ook naar het logboek geschreven. Hoe langer de gegevens die relevant zijn voor de moment opname, worden geblokkeerd, des te meer het gegevens volume kan groeien.&quot;_

Azure Backup zorgt voor de consistentie van het bestands systeem via Azure VM-extensies. Deze uitbrei dingen zijn niet zelfstandig beschikbaar en werken alleen in combi natie met Azure Backup service. Het is echter nog steeds nodig om scripts te bieden voor het maken en verwijderen van een moment opname van een SAP HANA om de consistentie van apps te garanderen.

Azure Backup heeft vier belang rijke fasen:

- Script voor voorbereiden uitvoeren-script moet een SAP HANA moment opname maken
- Moment opname maken
- Script voor de verwerking van een moment opname uitvoeren-script moet de SAP HANA verwijderen die is gemaakt door het voorbereidings script
- Gegevens overdragen aan de kluis

Raadpleeg de volgende artikelen voor meer informatie over het kopiëren van deze scripts en Details over de manier waarop Azure Backup precies werkt:

- [De infrastructuur voor back-ups van VM's in Azure plannen](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Toepassings consistente back-up van virtuele Azure Linux-machines](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



Op dit moment heeft micro soft geen scripts voor voor bereiding en scripts na moment opnamen gepubliceerd voor SAP HANA. Als klant of systeem integrator moet u deze scripts maken en de procedure configureren op basis van de documentatie waarnaar hierboven wordt verwezen.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Herstellen van toepassings consistente back-up op een VM
Het herstel proces van een toepassings consistente back-up die is gemaakt met Azure Backup wordt beschreven in het artikel [bestanden herstellen van back-ups van virtuele Azure-machines](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm). 

> [!IMPORTANT]
> In het artikel [herstel van de back-up van de virtuele machine van Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) wordt een lijst met uitzonde ringen en stappen beschreven voor het gebruik van schijf stripesets. Gestripde schijven zijn waarschijnlijk de reguliere VM-configuratie voor SAP HANA. Daarom is het essentieel om het artikel te lezen en het herstel proces voor dergelijke gevallen te testen, zoals vermeld in het artikel. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Het terugzetten van de HANA-licentie sleutel en de VM via Azure Backup Service

De Azure Backup-service is ontworpen voor het maken van een nieuwe virtuele machine tijdens het herstellen. Er is momenteel geen plan voor een &quot;in-place&quot; herstel bewerking van een bestaande Azure-VM.

![In deze afbeelding ziet u de optie herstellen van de Azure-service in de Azure Portal](media/sap-hana-backup-storage-snapshots/image019.png)

In deze afbeelding ziet u de optie herstellen van de Azure-service in de Azure Portal. U kunt kiezen tussen het maken van een virtuele machine tijdens het herstellen of het herstellen van de schijven. Na het herstellen van de schijven, is het nog steeds nodig om een nieuwe virtuele machine boven op de schijf te maken. Wanneer een nieuwe virtuele machine wordt gemaakt in azure, worden de unieke VM-ID-wijzigingen (Zie de [unieke id van de Azure VM openen en gebruiken](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![In deze afbeelding ziet u de unieke Azure VM-ID voor en na de terugzet bewerking via Azure Backup Service](media/sap-hana-backup-storage-snapshots/image020.png)

In deze afbeelding ziet u de unieke Azure VM-ID voor en na de terugzet bewerking via Azure Backup service. De SAP-hardwaresleutel, die wordt gebruikt voor SAP-licentie verlening, gebruikt deze unieke VM-ID. Als gevolg hiervan moet een nieuwe SAP-licentie worden geïnstalleerd na een herstel bewerking van de virtuele machine.

Tijdens het maken van deze back-upgids werd een nieuwe Azure Backup-functie weer gegeven in de preview-modus. Hiermee kan een herstel op bestands niveau worden uitgevoerd op basis van de VM-moment opname die is gemaakt voor de back-up van de virtuele machine. Dit voor komt dat u een nieuwe virtuele machine hoeft te implementeren, waardoor de unieke VM-ID niet hetzelfde blijft en er geen nieuwe SAP HANA licentie sleutel is vereist. Meer documentatie over deze functie wordt weer gegeven nadat deze volledig is getest.

Azure Backup kunt uiteindelijk back-ups maken van afzonderlijke virtuele Azure-schijven, plus bestanden en mappen van de virtuele machine. Een belang rijk voor deel van Azure Backup is het beheer van alle back-ups en het opslaan van de klant. Als een herstel bewerking nodig is, selecteert Azure Backup de juiste back-up om te gebruiken.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>Back-up van virtuele machine SAP HANA via hand matige moment opname van schijf

In plaats van de Azure Backup-service te gebruiken, kan één afzonderlijke back-upoplossing worden geconfigureerd door BLOB-moment opnamen van Azure-Vhd's hand matig te maken via Power shell. Zie [BLOB-moment opnamen gebruiken met Power shell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) voor een beschrijving van de stappen.

Het biedt meer flexibiliteit, maar de problemen die eerder in dit document worden beschreven, worden niet opgelost:

- U moet er nog zeker van zijn dat SAP HANA een consistente status heeft door een SAP HANA moment opname te maken
- De besturingssysteem schijf kan niet worden overschreven, zelfs niet als de toewijzing van de virtuele machine ongedaan wordt gemaakt vanwege een fout met de melding dat er een lease bestaat. Het werkt alleen na het verwijderen van de virtuele machine, wat leidt tot een nieuwe unieke VM-ID en de nood zaak om een nieuwe SAP-licentie te installeren.

![Het is mogelijk om alleen de gegevens schijven van een virtuele machine van Azure te herstellen](media/sap-hana-backup-storage-snapshots/image021.png)

Het is mogelijk om alleen de gegevens schijven van een virtuele machine van Azure te herstellen, waardoor het probleem van het ophalen van een nieuwe unieke VM-ID wordt voor komen en daarom de SAP-licentie ongeldig is geworden:

- Voor de test zijn twee Azure-gegevens schijven gekoppeld aan een VM en is software-RAID boven op de machine gedefinieerd 
- Er is bevestigd dat SAP HANA een consistente status had door SAP HANA snap shot-functie
- Bestands systeem blok keren (Zie _SAP Hana gegevens consistentie bij het maken van moment opnamen van opslag_ in de bijbehorende [back-upgids voor artikel voor SAP HANA op Azure virtual machines](sap-hana-backup-guide.md))
- Er zijn BLOB-moment opnamen opgehaald van beide gegevens schijven
- Het bestands systeem wordt geblokkeerd
- Bevestiging van SAP HANA moment opname
- Als u de gegevens schijven wilt herstellen, is de VM afgesloten en zijn beide schijven losgekoppeld
- Nadat de schijven zijn losgekoppeld, zijn ze overschreven met de voormalige BLOB-moment opnamen
- Vervolgens zijn de herstelde virtuele schijven opnieuw gekoppeld aan de VM
- Na het starten van de virtuele machine heeft alles op de software-RAID gewerkt en teruggezet naar de tijd van de BLOB-moment opname
- HANA is weer ingesteld op de HANA-moment opname

Als het mogelijk is om SAP HANA af te sluiten vóór de BLOB-moment opnamen, zou de procedure minder complex zijn. In dat geval kunt u de HANA-moment opname overs Laan en, als er niets meer gebeurt in het systeem, ook de blok kering van het bestands systeem overs Laan. Toegevoegde complexiteit vindt plaats in de afbeelding wanneer het nodig is om moment opnamen uit te voeren terwijl alles online is. Zie _SAP Hana consistentie van gegevens bij het maken van moment opnamen van opslag_ in de bijbehorende [back-upgids voor artikel voor SAP HANA op Azure virtual machines](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Volgende stappen
* In de [back-upgids voor SAP Hana op Azure virtual machines](sap-hana-backup-guide.md) vindt u een overzicht en informatie over aan de slag.
* Met [SAP Hana back-up op bestands niveau](sap-hana-backup-file-level.md) wordt de back-upoptie op basis van bestanden beschreven.
* Zie [SAP Hana (grote instanties) hoge Beschik baarheid en herstel na nood gevallen op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie over het tot stand brengen van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure (grote exemplaren).
