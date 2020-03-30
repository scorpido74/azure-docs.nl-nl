---
title: Aan boord van uw azure stack-virtuele machines naar Azure Sentinel | Microsoft Documenten
description: In dit artikel ziet u hoe u de azure-extensie voor beeldschermen, updates en configuratiebeheer indient op virtuele Azure Stack-machines en deze controleren met Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a8213bd57936f95870324950204dbd6c1473739a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588515"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Azure Stack-virtuele machines verbinden met Azure Sentinel




Met Azure Sentinel u uw VM's die op Azure en Azure Stack worden uitgevoerd, op één plaats controleren. Als u uw Azure Stack-machines wilt aanboord voor Azure Sentinel, moet u eerst de extensie voor virtuele machines toevoegen aan uw bestaande azure stack-virtuele machines. 

Nadat u Azure Stack-machines hebt aangesloten, u kiezen uit een galerij met dashboards die inzichten weergeven op basis van uw gegevens. Deze dashboards kunnen eenvoudig worden aangepast aan uw behoeften.



## <a name="add-the-virtual-machine-extension"></a>De extensie van de virtuele machine toevoegen 

Voeg de azure **monitor-, update- en configuratiebeheer-extensie** toe aan de virtuele machines die op uw Azure Stack worden uitgevoerd. 

1. Log in een nieuw browsertabblad in bij uw [Azure Stack-portal.](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal)
2. Ga naar de pagina **Virtuele machines** en selecteer de virtuele machine die u wilt beveiligen met Azure Sentinel. Zie [Een Windows-serverVM maken met de Azure Stack-portal](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) of Een [VM voor een Linux-server maken met behulp van de Azure Stack-portal](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)voor informatie over het maken van een virtuele machine op Azure Stack.
3. Selecteer **Extensies**. De lijst van virtuele machine-extensies geïnstalleerd op deze virtuele machine wordt weergegeven.
4. Klik op het tabblad **Toevoegen.** Het **menublad New Resource** wordt geopend en toont de lijst met beschikbare virtuele machine-extensies. 
5. Selecteer de **azure-extensie voor monitor-, update- en configuratiebeheer** en klik op **Maken**. Het configuratievenster **voor de installatieextensie** wordt geopend.

   ![Instellingen voor Azure-monitor-, update- en configuratiebeheer](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Als u de azure **monitor-, update- en configuratiebeheerextensie** niet in uw marktplaats ziet, neem dan contact op met uw Azure Stack-operator om deze beschikbaar te maken.

6. Selecteer in het azure sentinel-menu **werkruimte-instellingen** gevolgd door **Geavanceerd**en kopieer de **werkruimte-id** en **werkruimtesleutel (primaire sleutel).** 
1. Plak ze in het **extensievenster** Azure Stack Install in de aangegeven velden en klik op **OK**.
1. Nadat de uitbreidingsinstallatie is voltooid, wordt de status weergegeven als **Provisioning Succeeded**. Het kan tot een uur duren voordat de virtuele machine wordt weergegeven in de Azure Sentinel-portal.

Zie [Windows-computers](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)verbinden voor meer informatie over het installeren en configureren van de agent voor Windows.

Zie Azure Log Analytics [Linux Agent oplossen](../azure-monitor/platform/agent-linux-troubleshoot.md)voor Linux-probleemproblemen.

In de Azure Sentinel-portal op Azure, onder **Virtuele machines,** hebt u een overzicht van alle VM's en computers, samen met hun status. 

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer dit niet meer nodig is, u de extensie uit de virtuele machine verwijderen via de Azure Stack-portal.

Ga als u de extensie verwijderen:

1. Open de **Azure Stack Portal**.
2. Ga naar de pagina **Virtuele machines** en selecteer de virtuele machine waaruit u de extensie wilt verwijderen.
3. Selecteer **Extensies**, selecteer de extensie **Microsoft.EnterpriseCloud.Monitoring**.
4. Klik op **Verwijderen**en bevestig uw selectie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
- Gegevens van [apparaten met algemene foutindeling naar](connect-common-event-format.md) Azure Sentinel streamen.
