---
title: 'Het D: station van een virtuele machine maken een gegevens schijf '
description: 'Hierin wordt beschreven hoe u stationsletters wijzigt voor een Windows-VM, zodat u de D: station kunt gebruiken als gegevens station.'
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 15df3178f2860fa066a82cb1429e0c1a6e5c2b08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82083419"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Het D: station gebruiken als gegevens station op een Windows-VM
Als uw toepassing het D-station moet gebruiken om gegevens op te slaan, volgt u deze instructies om een andere stationsletter voor de tijdelijke schijf te gebruiken. Gebruik nooit de tijdelijke schijf om gegevens op te slaan die u wilt bewaren.

Als u het formaat van een virtuele machine wijzigt of **stopt (toewijzing opheffen)** , wordt de plaatsing van de virtuele machine naar een nieuwe Hyper Visor geactiveerd. Deze plaatsing kan ook worden geactiveerd door een geplande of niet-geplande onderhouds gebeurtenis. In dit scenario wordt de tijdelijke schijf opnieuw toegewezen aan de eerste beschik bare stationsletter. Als u een toepassing hebt die specifiek het D:-station vereist, moet u deze stappen volgen om het bestand Pagefile. sys tijdelijk te verplaatsen, een nieuwe gegevens schijf toe te voegen en de letter D toe te wijzen en vervolgens het bestand Pagefile. sys terug te zetten naar het tijdelijke station. Zodra het proces is voltooid, wordt de D niet meer teruggebracht door Azure als de VM naar een andere Hyper Visor wordt verplaatst.

Zie [inzicht in het tijdelijke station op Microsoft Azure virtual machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) voor meer informatie over hoe Azure de tijdelijke schijf gebruikt.

## <a name="attach-the-data-disk"></a>De gegevens schijf koppelen
Eerst moet u de gegevens schijf aan de virtuele machine koppelen. Zie [een beheerde gegevens schijf koppelen in de Azure Portal](attach-managed-disk-portal.md)om dit te doen met behulp van de portal.

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Pagefile. sys tijdelijk verplaatsen naar station C
1. Maak verbinding met de virtuele machine. 
2. Klik met de rechter muisknop op het menu **Start** en selecteer **systeem**.
3. Selecteer in het menu aan de linkerkant **geavanceerde systeem instellingen**.
4. Selecteer in de sectie **prestaties** de optie **instellingen**.
5. Klik op het tabblad **Geavanceerd**.
6. Selecteer **wijzigen**in het gedeelte **virtueel geheugen** .
7. Selecteer station **C** en klik vervolgens op door **systeem beheerde grootte** en klik vervolgens op **instellen**.
8. Selecteer het station **D** en klik vervolgens op **geen wissel bestand** en klik vervolgens op **instellen**.
9. Klik op Toep assen. Er wordt een waarschuwing weer gegeven dat de computer opnieuw moet worden opgestart om de wijzigingen van kracht te laten worden.
10. Start de virtuele machine opnieuw op.

## <a name="change-the-drive-letters"></a>De stationsletters wijzigen
1. Nadat de VM opnieuw is opgestart, meldt u zich weer aan bij de VM.
2. Klik op het menu **Start** , typ **diskmgmt. msc** en druk op ENTER. Schijf beheer wordt gestart.
3. Klik met de rechter muisknop op **D**, het tijdelijke opslag station en selecteer **stationsletter en paden wijzigen**.
4. Selecteer onder stationsletter een nieuw station, bijvoorbeeld **T** , en klik vervolgens op **OK**. 
5. Klik met de rechter muisknop op de gegevens schijf en selecteer **stationsletter en paden wijzigen**.
6. Onder stationsletter, selecteer station **D** en klik vervolgens op **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Pagefile. sys terug naar het tijdelijke opslag station verplaatsen
1. Klik met de rechter muisknop op het menu **Start** en selecteer **systeem**
2. Selecteer in het menu aan de linkerkant **geavanceerde systeem instellingen**.
3. Selecteer in de sectie **prestaties** de optie **instellingen**.
4. Klik op het tabblad **Geavanceerd**.
5. Selecteer **wijzigen**in het gedeelte **virtueel geheugen** .
6. Selecteer het OS-station **C** en klik op **geen wissel bestand** en klik vervolgens op **instellen**.
7. Selecteer het tijdelijke opslag station **T** en klik vervolgens op door **systeem beheerde grootte** en klik vervolgens op **instellen**.
8. Klik op **Toepassen**. Er wordt een waarschuwing weer gegeven dat de computer opnieuw moet worden opgestart om de wijzigingen van kracht te laten worden.
9. Start de virtuele machine opnieuw op.

## <a name="next-steps"></a>Volgende stappen
* U kunt de opslag ruimte die beschikbaar is voor uw virtuele machine verhogen door [een extra gegevens schijf te koppelen](attach-managed-disk-portal.md).

