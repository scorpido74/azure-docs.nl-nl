---
title: Bestands systeem controleren bij het opstarten van een Azure-VM | Microsoft Docs
description: Meer informatie over het oplossen van het probleem dat door VM wordt gecontroleerd bestands systeem controleren bij het opstarten | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 86938c582745cb0759eda9cd0693f407471a0529
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921484"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Windows geeft ' controleren op bestands systeem ' bij het opstarten van een Azure VM

In dit artikel wordt de fout ' controle van het bestands systeem ' beschreven die kan optreden wanneer u een virtuele Windows-machine (VM) opstart in Microsoft Azure.


## <a name="symptom"></a>Symptoom 

Een Windows-VM start niet. Wanneer u de opstart scherm opnamen in [Diagnostische gegevens over opstarten](boot-diagnostics.md)controleert, ziet u dat het proces controle schijf (Chkdsk. exe) wordt uitgevoerd met een van de volgende berichten:

- Station scannen en herstellen (C:)
- Bestands systeem op C controleren:

## <a name="cause"></a>Oorzaak

Als er een NTFS-fout in het bestands systeem wordt gevonden, wordt de consistentie van de schijf bij de volgende keer opnieuw opstarten gecontroleerd en hersteld. Dit gebeurt meestal als de VM onverwacht opnieuw wordt opgestart of als het afsluit proces van de virtuele machine abrupt is onderbroken.

## <a name="solution"></a>Oplossing 

Windows wordt normaal opgestart nadat het proces voor het controleren van de schijf is voltooid. Als de virtuele machine is vastgelopen in het controle schijf proces, probeert u de controle schijf op de virtuele machine offline uit te voeren:
1.  Maak een moment opname van de besturingssysteem schijf van de betrokken VM als back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.
2.  [Koppel de besturingssysteem schijf aan een herstel-VM](troubleshoot-recovery-disks-portal-windows.md).  
3.  Op de virtuele machine voor herstel voert u schijf controleren uit op de gekoppelde besturingssysteem schijf. In het volgende voor beeld is de Stuur letter van de gekoppelde besturingssysteem schijf E: 
        
        chkdsk E: /f
4.  Nadat de controle schijf is voltooid, ontkoppelt u de schijf van de herstel-VM en koppelt u de schijf opnieuw aan de betrokken VM als een besturingssysteem schijf. Zie [problemen met een Windows-VM oplossen door de besturingssysteem schijf aan een herstel-VM te koppelen](troubleshoot-recovery-disks-portal-windows.md)voor meer informatie.
