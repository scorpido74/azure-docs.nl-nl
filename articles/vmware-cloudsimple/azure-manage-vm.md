---
title: Azure VMware-oplossing per cloudSimple - Beheer Private Cloud VM's in Azure
description: Beschrijft hoe u CloudSimple Private Cloud VM's beheert in de Azure-portal, inclusief het toevoegen van schijven, het wijzigen van vm-capaciteit en het toevoegen van netwerkinterfaces
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 44a0b8fe56477620c0ac47d5c5de8830dac46214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014994"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Uw CloudSimple Private Cloud-virtuele machines beheren in Azure

Als u de virtuele machines wilt beheren die u [voor uw CloudSimple Private Cloud hebt gemaakt,](azure-create-vm.md)meldt u zich bij de [Azure-portal.](https://portal.azure.com) Zoek naar en selecteer het virtuele (zoek onder **Alle services** of **virtuele machines** in het zijmenu).

## <a name="control-virtual-machine-operation"></a>Bediening van virtuele machines bedienen

De volgende besturingselementen zijn beschikbaar op de pagina **Overzicht** voor uw geselecteerde virtuele machine.

| Beheer | Beschrijving |
| ------------ | ------------- |
| Verbinding maken | Maak verbinding met de opgegeven VM.  |
| Starten | Start de opgegeven VM.  |
| Opnieuw starten | Schakel de opgegeven VM uit en schakel vervolgens de opgegeven VM uit.  |
| Stoppen | Schakel de specifieke VM uit.  |
| Capture | Maak een afbeelding van de opgegeven VM vast, zodat deze als afbeelding kan worden gebruikt om andere VM's te maken. Zie [Een beheerde afbeelding van een gegeneraliseerde vm maken in Azure](../virtual-machines/windows/classic/capture-image.md).   |
| Verplaatsen | Naar de opgegeven VM gaan.  |
| Verwijderen | Verwijder de opgegeven VM.  |
| Vernieuwen | Vernieuw de gegevens in het display.  |

### <a name="view-performance-information"></a>Prestatie-informatie weergeven

De grafieken in het onderste gedeelte van de pagina **Overzicht** presenteren prestatiegegevens voor het geselecteerde interval (laatste uur tot 30 dagen; standaard is het laatste uur). In elke grafiek u de numerieke waarden voor elk moment binnen het interval weergeven door de cursor heen en weer over de grafiek te verplaatsen.

De volgende grafieken worden weergegeven.

| Item | Beschrijving |
| ------------ | ------------- |
| CPU (gemiddeld) | Gemiddeld CPU-gebruik in percentage boven het geselecteerde interval.   |
| Netwerk | Verkeer in en uit het netwerk (MB) via het geselecteerde interval.  |
| Schijfbytes | Totaal aantal gegevens dat van schijf wordt gelezen en naar schijf (MB) is geschreven via het geselecteerde interval.  |
| Schijfbewerkingen | Gemiddelde snelheid van schijfbewerkingen (bewerkingen/seconde) over het geselecteerde interval. |

## <a name="manage-vm-disks"></a>VM-schijven beheren

Als u een VM-schijf wilt toevoegen, opent u de pagina **Schijven** voor de geselecteerde virtuele machine. Als u een schijf wilt toevoegen, klikt u op **Schijf toevoegen**. Configureer elk van de volgende instellingen door een inline-optie in te voeren of te selecteren. Klik op **Opslaan**.

   | Item | Beschrijving |
   | ------------ | ------------- |
   | Name | Voer een naam in om de schijf te identificeren.  |
   | Grootte | Selecteer een van de beschikbare maten.  |
   | SCSI-controller | Selecteer een SCSI-controller. De beschikbare controllers variëren voor de verschillende ondersteunde besturingssystemen.  |
   | Modus | Hiermee bepaalt u hoe de schijf deelneemt aan momentopnamen. Kies een van de volgende opties: <br> - Onafhankelijke persistent: Alle gegevens die naar de schijf worden geschreven, worden permanent geschreven.<br> - Onafhankelijk, niet-persistent: wijzigingen die op de schijf zijn geschreven, worden verwijderd wanneer u de virtuele machine uitschakelt of opnieuw instelt.  Met deze modus u de VM altijd in dezelfde staat opnieuw starten. Zie voor meer informatie de [VMware-documentatie](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Als u een schijf wilt verwijderen, selecteert u deze en klikt u op **Verwijderen**.

## <a name="change-the-capacity-of-the-vm"></a>De capaciteit van de VM wijzigen

Als u de capaciteit van de virtuele machine wilt wijzigen, opent u de pagina **Grootte** voor de geselecteerde virtuele machine. Geef een van de volgende opties op en klik op **Opslaan**.

| Item | Beschrijving |
| ------------ | ------------- |
| Aantal kerngeheugens | Aantal kernen dat aan de VM is toegewezen.  |
| Hardwarevirtualisatie | Schakel het selectievakje in om de hardwarevirtualisatie bloot te stellen aan het besturingssysteem van de gast. Zie het VMware-artikel [Bloot VMware Hardware Assisted Virtualization](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html). |
| Geheugengrootte | Selecteer de hoeveelheid geheugen die u aan de VM wilt toewijzen.  

## <a name="manage-network-interfaces"></a>Netwerkinterfaces beheren

Als u een interface wilt toevoegen, klikt u op **Netwerkinterface toevoegen**. Configureer elk van de volgende instellingen door een inline-optie in te voeren of te selecteren. Klik op **Opslaan**.

   | Beheer | Beschrijving |
   | ------------ | ------------- |
   | Name | Voer een naam in om de interface te identificeren.  |
   | Netwerk | Kies uit de lijst met geconfigureerde netwerken in uw Private Cloud vSphere.  |
   | Adapter | Selecteer een vSphere-adapter in de lijst met beschikbare typen die voor de VM zijn geconfigureerd. Zie voor meer informatie het VMware knowledge base-artikel [Het kiezen van een netwerkadapter voor uw virtuele machine.](https://kb.vmware.com/s/article/1001805) |
   | Inschakelen bij Boot | Kies of u de NIC-hardware wilt inschakelen wanneer de VM wordt opgestart. De standaardinstelling is **Inschakelen**. |

Als u een netwerkinterface wilt verwijderen, selecteert u deze en klikt u op **Verwijderen**.
