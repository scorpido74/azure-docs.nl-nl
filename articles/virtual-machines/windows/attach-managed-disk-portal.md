---
title: Een beheerde gegevens schijf koppelen aan een virtuele Windows-machine-Azure
description: Hoe u een beheerde gegevensschijf koppelen aan een virtuele Windows-machine met behulp van de Azure-portal.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0610b235d218c24c108e39aeb9bd3b2cf8c9c663
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463432"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Een beheerde gegevensschijf koppelen aan een virtuele Windows-machine met behulp van Azure portal

In dit artikel wordt beschreven hoe u een nieuwe beheerde gegevensschijf koppelen aan een Windows virtuele machine (VM) met behulp van de Azure-portal. De grootte van de virtuele machine bepaalt hoeveel gegevensschijven die u kunt koppelen. Zie voor meer informatie, [grootten voor virtuele machines](sizes.md).


## <a name="add-a-data-disk"></a>Een gegevensschijf toevoegen

1. Ga naar de [Azure Portal](https://portal.azure.com) om een gegevens schijf toe te voegen. Zoek en selecteer **virtuele machines**.
2. Selecteer een virtuele machine in de lijst.
3. Op de **virtuele machine** weergeeft, schakelt **schijven**.
4. Op de **schijven** weergeeft, schakelt **gegevensschijf toevoegen**.
5. Selecteer in de vervolgkeuzelijst voor de nieuwe schijf, **maken-schijf**.
6. In de **beheerde schijf maken** pagina, typ een naam voor de schijf en de andere instellingen naar behoefte aanpassen. Als u gereed bent, selecteert u **Maken**.
7. In de **schijven** weergeeft, schakelt **opslaan** om op te slaan van de configuratie van de nieuwe schijven voor de virtuele machine.
8. Nadat Azure de schijf wordt gemaakt en gekoppeld aan de virtuele machine, de nieuwe schijf wordt weergegeven in de instellingen van de schijven van de virtuele machine onder **gegevensschijven**.


## <a name="initialize-a-new-data-disk"></a>Een nieuwe gegevensschijf initialiseren

1. Maak verbinding met de VM.
1. Selecteer de Windows **Start** menu in de actieve virtuele machine en voer **diskmgmt.msc** in het zoekvak in. De **Schijfbeheer** -console wordt geopend.
2. Schijfbeheer herkent dat u een nieuwe, niet-geïnitialiseerde schijfruimte hebt en de **schijf initialiseren** venster wordt weergegeven.
3. Controleer of de nieuwe schijf is geselecteerd en selecteer vervolgens **OK** voor het initialiseren.
4. De nieuwe schijf wordt weergegeven als **niet-toegewezen**. Klik met de rechtermuisknop op de schijf en selecteer **Nieuw eenvoudig volume**. De **Wizard Nieuw eenvoudig Volume** venster wordt geopend.
5. Doorloop de wizard, blijven alle van de standaardinstellingen, en wanneer u klaar bent, selecteert **voltooien**.
6. Sluiten **Schijfbeheer**.
7. Een pop-upvenster weergegeven de melding die u nodig hebt om de opmaak van de nieuwe schijf voordat u deze kunt gebruiken. Selecteer **schijf formatteren**.
8. In de **nieuwe schijf formatteren** venster, Controleer de instellingen en selecteer vervolgens **Start**.
9. Een waarschuwing weergegeven de melding dat de schijven formatteert, worden alle gegevens. Selecteer **OK**.
10. Wanneer de opmaak voltooid is, selecteert u **OK**.

## <a name="next-steps"></a>Volgende stappen

- U kunt ook [een gegevensschijf koppelen met behulp van PowerShell](attach-disk-ps.md).
- Als uw toepassing moet u de *D:* station voor het opslaan van gegevens, kunt u [wijzigen van de stationsletter van de tijdelijke schijf Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
