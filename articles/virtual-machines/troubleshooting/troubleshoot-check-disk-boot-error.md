---
title: Bestandssysteem controleren bij het opstarten van een Azure VM| Microsoft Documenten
description: Meer informatie over het oplossen van het probleem dat vm-systeem controleren weergeeft bij het opstarten| Microsoft Documenten
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921484"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Windows toont 'bestandssysteem controleren' bij het opstarten van een Azure VM

In dit artikel wordt de fout 'Bestandssysteem controleren' beschreven die u tegenkomen bij het opstarten van een Windows Virtual Machine (VM) in Microsoft Azure.


## <a name="symptom"></a>Symptoom 

Een Windows-vm wordt niet gestart. Wanneer u de opstartschermafbeeldingen in [Opstartdiagnose](boot-diagnostics.md)controleert, ziet u dat het proces Schijf controleren (chkdsk.exe) wordt uitgevoerd met een van de volgende berichten:

- Scan- en reparatiestation (C:)
- Bestandssysteem controleren op C:

## <a name="cause"></a>Oorzaak

Als er een NTFS-fout wordt gevonden in het bestandssysteem, controleert en herstelt Windows de consistentie van de schijf bij de volgende herstart. Meestal gebeurt dit als de VM een onverwachte herstart heeft gehad of als het VM-afsluitproces abrupt werd onderbroken.

## <a name="solution"></a>Oplossing 

Windows wordt normaal opgestart nadat het proces Schijf controleren is voltooid. Als de vm vastzit in het proces Schijf controleren, probeert u de schijf controleren op de VM offline uit te voeren:
1.  Maak een momentopname van de OS-schijf van de getroffen VM als back-up. Zie [Momentopname een schijf voor](../windows/snapshot-copy-managed-disk.md)meer informatie .
2.  [Koppel de OS-schijf aan een herstelvm](troubleshoot-recovery-disks-portal-windows.md).  
3.  Voer op de herstel-vm Schijf controleren uit op de aangesloten osschijf. In het volgende voorbeeld is de stuurprogrammaletter van de aangesloten OS-schijf E: 
        
        chkdsk E: /f
4.  Nadat de schijf controleren is voltooid, moet u de schijf loskoppelen van de herstel-vm en de schijf vervolgens opnieuw koppelen aan de betreffende VM als een OS-schijf. Zie [Problemen met een Windows-vm oplossen door de OS-schijf aan een herstel-vm te koppelen](troubleshoot-recovery-disks-portal-windows.md)voor meer informatie.
