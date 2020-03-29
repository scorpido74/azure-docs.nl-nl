---
title: Een beheerde gegevensschijf koppelen aan een Windows VM - Azure
description: Een beheerde gegevensschijf koppelen aan een Windows-vm met behulp van de Azure-portal.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0fe04941821de2ac6e4e873e8d073c3e9b9d9508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919376"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Een beheerde gegevensschijf aan een Windows-vm koppelen met behulp van de Azure-portal

In dit artikel ziet u hoe u een nieuwe beheerde gegevensschijf aan een virtuele Windows-machine (VM) koppelt met behulp van de Azure-portal. De grootte van de VM bepaalt hoeveel gegevensschijven u koppelen. Zie [Grootte voor virtuele machines voor](sizes.md)meer informatie .


## <a name="add-a-data-disk"></a>Een gegevensschijf toevoegen

1. Ga naar de [Azure-portal](https://portal.azure.com) om een gegevensschijf toe te voegen. Zoeken naar virtuele machines en selecteer **deze**.
2. Selecteer een virtuele machine in de lijst.
3. Selecteer **schijven**op de pagina **Virtuele machine** .
4. Selecteer **op** de pagina Schijven de optie **Gegevensschijf toevoegen**.
5. Selecteer **schijf maken**in de vervolgkeuzelijst voor de nieuwe schijf .
6. Typ op de pagina **Beheerde schijf maken** een naam voor de schijf in en pas de andere instellingen zo nodig aan. Als u gereed bent, selecteert u **Maken**.
7. Selecteer **op** de pagina Schijven de optie **Opslaan** om de nieuwe schijfconfiguratie voor de virtuele machine op te slaan.
8. Nadat Azure de schijf heeft gemaakt en aan de virtuele machine heeft bevestigd, wordt de nieuwe schijf weergegeven in de schijfinstellingen van de virtuele machine onder **Gegevensschijven**.


## <a name="initialize-a-new-data-disk"></a>Een nieuwe gegevensschijf initialiseren

1. Maak verbinding met de VM.
1. Selecteer het menu Windows **Start** in de uitvoerende VM en voer **diskmgmt.msc** in het zoekvak in. De **schijfbeheerconsole** wordt geopend.
2. Schijfbeheer herkent dat u een nieuwe, niet-geïninitialiseerde schijf hebt en dat het venster **Initialiserenschijf** wordt weergegeven.
3. Controleer of de nieuwe schijf is geselecteerd en selecteer **OK** om deze te initialiseren.
4. De nieuwe schijf wordt weergegeven als **niet-toegewezen**. Klik met de rechtermuisknop op de schijf en selecteer **Nieuw eenvoudig volume**. Het venster **Van de wizard Nieuw eenvoudig volume** wordt geopend.
5. Ga door de wizard, houd alle standaardinstellingen bij en selecteer **Voltooien**als u klaar bent.
6. **Schijfbeheer sluiten**.
7. Er verschijnt een pop-upvenster waarin wordt gemeld dat u de nieuwe schijf moet opmaken voordat u deze gebruiken. Selecteer **Schijf opmaken**.
8. Controleer in **het venster Nieuwe schijf opmaken** de instellingen en selecteer **Start**.
9. Er verschijnt een waarschuwing waarin u wordt gewaarschuwd dat het opmaken van de schijven alle gegevens wist. Selecteer **OK**.
10. Wanneer de opmaak is voltooid, selecteert u **OK**.

## <a name="next-steps"></a>Volgende stappen

- U ook [een gegevensschijf koppelen met PowerShell](attach-disk-ps.md).
- Als uw toepassing het *D:-station* moet gebruiken om gegevens op te slaan, u [de stationsletter van de tijdelijke schijf van Windows wijzigen.](change-drive-letter.md)
