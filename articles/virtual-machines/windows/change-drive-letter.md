---
title: 'De D: station van een VM een gegevensschijf maken '
description: Beschrijft hoe u schijfletters voor een Windows-vm wijzigt, zodat u het d:station als gegevensstation gebruiken.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 491e7b7be084017cc370fe431c3175ac5b2673f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033657"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>D: station gebruiken als gegevensstation op een Windows-vm
Als uw toepassing het D-station moet gebruiken om gegevens op te slaan, volgt u deze instructies om een andere stationsletter voor de tijdelijke schijf te gebruiken. Gebruik de tijdelijke schijf nooit om gegevens op te slaan die u moet bewaren.

Als u het formaat van een virtuele machine wijzigt of stopt **(Deallocate)** kan dit leiden tot plaatsing van de virtuele machine naar een nieuwe hypervisor. Een geplande of ongeplande onderhoudsgebeurtenis kan ook deze plaatsing activeren. In dit scenario wordt de tijdelijke schijf opnieuw toegewezen aan de eerste beschikbare stationsletter. Als u een toepassing hebt waarvoor specifiek het D-station vereist is, moet u deze stappen volgen om de pagefile.sys tijdelijk te verplaatsen, een nieuwe gegevensschijf toe te voegen en de letter D toe te wijzen en vervolgens de pagefile.sys terug te verplaatsen naar het tijdelijke station. Eenmaal voltooid, zal Azure de D niet terugnemen: als de VM naar een andere hypervisor wordt verplaatst.

Zie Het tijdelijke station op Microsoft [Azure Virtual Machines begrijpen](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) voor meer informatie over hoe Azure de tijdelijke schijf gebruikt

## <a name="attach-the-data-disk"></a>De gegevensschijf koppelen
Eerst moet u de gegevensschijf aan de virtuele machine koppelen. Zie [Een beheerde gegevensschijf koppelen aan de Azure-portal](attach-managed-disk-portal.md)om dit te doen met de portal.

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Pagefile.sys tijdelijk verplaatsen naar C-station
1. Maak verbinding met de virtuele machine. 
2. Klik met de rechtermuisknop op het menu **Start** en selecteer **Systeem**.
3. Selecteer **geavanceerde systeeminstellingen**in het linkermenu .
4. Selecteer **In** de sectie Prestaties de optie **Instellingen**.
5. Klik op het tabblad **Geavanceerd**.
6. Selecteer wijzigen in de sectie **Virtueel geheugen** **.**
7. Selecteer het **C-station** en klik op **Systeembeheergrootte** en klik vervolgens op **Instellen**.
8. Selecteer het **D-station** en klik op **Geen paging-bestand** en klik vervolgens op **Instellen**.
9. Klik op Toepassen. U krijgt een waarschuwing dat de computer opnieuw moet worden opgestart om de wijzigingen te kunnen beïnvloeden.
10. Start de virtuele machine opnieuw op.

## <a name="change-the-drive-letters"></a>De stationsletters wijzigen
1. Zodra de VM opnieuw wordt opgestart, meldt u zich weer aan bij de VM.
2. Klik **op** het menu Start en typ **diskmgmt.msc** en druk op Enter. Schijfbeheer wordt gestart.
3. Klik met de rechtermuisknop op **D,** het station Tijdelijke opslag en selecteer **Stationsletter en -paden wijzigen**.
4. Selecteer onder De letter Van drive een nieuw station zoals **T** en klik op **OK**. 
5. Klik met de rechtermuisknop op de gegevensschijf en selecteer **Stationsletter en -paden wijzigen**.
6. Selecteer onder Station-letter station **D** en klik op **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Pagefile.sys terug naar het tijdelijke opslagstation verplaatsen
1. Klik met de rechtermuisknop op het menu **Start** en selecteer **Systeem**
2. Selecteer **geavanceerde systeeminstellingen**in het linkermenu .
3. Selecteer **In** de sectie Prestaties de optie **Instellingen**.
4. Klik op het tabblad **Geavanceerd**.
5. Selecteer wijzigen in de sectie **Virtueel geheugen** **.**
6. Selecteer het osstation **C** en klik op **Geen paging-bestand** en klik op **Instellen**.
7. Selecteer het tijdelijke opslagstation **T** en klik op **Systeembeheergrootte** en klik vervolgens op **Instellen**.
8. Klik op **Toepassen**. U krijgt een waarschuwing dat de computer opnieuw moet worden opgestart om de wijzigingen te kunnen beïnvloeden.
9. Start de virtuele machine opnieuw op.

## <a name="next-steps"></a>Volgende stappen
* U de beschikbare opslagruimte voor uw virtuele machine vergroten door [een extra gegevensschijf toe](attach-managed-disk-portal.md)te voegen.

