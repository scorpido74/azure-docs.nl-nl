---
title: Uitgebreide metrische gegevens voor Azure virtual machines toevoegen | Microsoft Docs
description: Dit artikel helpt u bij het inschakelen en metrische gegevens over uitgebreide diagnostische gegevens configureren voor uw Azure VM's.
services: cost-management
keywords: ''
author: bandersmsft
manager: vitavor
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
ms.custom: seodec18
ms.openlocfilehash: bd9089b868284902cdc33d87972d573dfbf0097b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989551"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Uitgebreide metrische gegevens voor Azure virtual machines toevoegen

Cloudyn maakt gebruik van Azure metrische gegevens van uw Azure-VM's om u gedetailleerde informatie over hun resources weer te geven. Metrische gegevens, prestatiemeteritems, ook met de naam wordt gebruikt door Cloudyn om rapporten te genereren. Echter Cloudyn wordt niet automatisch verzamelen van alle Azure metrische gegevens van Gast-VM's, moet u metrische gegevens verzameling inschakelen. Dit artikel helpt u bij het inschakelen en configureren van aanvullende metrische gegevens voor uw Azure VM's.

Nadat u het verzamelen van metrische gegevens inschakelt, kunt u het volgende doen:

- Op de hoogte wanneer uw virtuele machines hun geheugen, schijf en CPU-limieten zijn bereikt.
- Detecteer trends in gebruik en afwijkingen.
- Uw kosten te beheren door de grootte op basis van gebruik.
- Get kosten-effectieve formaat aanbevelingen van de optimalisatie van Cloudyn.

Bijvoorbeeld, als u wilt het % CPU en geheugen-% van uw virtuele Azure-machines bewaken. De metrische gegevens van de Azure-VM komen overeen met het _percentage CPU_ en _\% \Memory toegewezen bytes in gebruik_.

> [!NOTE]
> Uitgebreide metrische gegevensverzameling wordt alleen ondersteund met Azure-bewaking op gastniveau. Cloudyn is niet compatibel met de [log Analytics-agent](../../azure-monitor/platform/agents-overview.md). 

## <a name="determine-whether-extended-metrics-are-enabled"></a>Bepalen of uitgebreide metrische gegevens zijn ingeschakeld

1. Meld u aan bij Azure Portal op https://portal.azure.com.
2. Onder **virtuele machines**, selecteert u een virtuele machine en klik vervolgens onder **bewaking**, selecteer **metrische gegevens**. Een lijst met beschikbare metrische gegevens wordt weergegeven.
3. Sommige metrische gegevens selecteren en een grafiek voor deze gegevens worden weergegeven.  
    ![Voorbeeld van de metrische gegevens: host percentage CPU](./media/azure-vm-extended-metrics/metric01.png)

In het voorgaande voorbeeld wordt een beperkte set van standaard metrische gegevens zijn beschikbaar voor uw hosts, maar geheugen metrische gegevens zijn niet. Geheugen metrische gegevens maken deel uit van de uitgebreide metrische gegevens. In dit geval zijn uitgebreide metrische gegevens niet ingeschakeld voor de virtuele machine. U moet enkele extra stappen voor het inschakelen van uitgebreide metrische gegevens uitvoeren. De volgende informatie helpt u om hen in staat.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Uitgebreide metrische gegevens in Azure portal inschakelen

Standaard metrische gegevens zijn computer metrische gegevens voor hosts. Het _percentage CPU_ -metriek is een voor beeld. Er zijn ook eenvoudige metrische gegevens voor de Gast-VM's en uitgebreide metrische gegevens ook genoemd. Voor beelden van uitgebreide metrische gegevens zijn _\Memory\% toegewezen bytes in gebruik_ en _\Memory\Available bytes_.

Het inschakelen van uitgebreide metrische gegevens is eenvoudig. Voor elke virtuele machine, schakel de bewaking op gastniveau. Wanneer u de bewaking op gastniveau inschakelt, wordt de Azure diagnostics-agent is geïnstalleerd op de virtuele machine. Standaard een set met uitgebreide metrische gegevens worden toegevoegd. Het volgende proces is hetzelfde voor klassieke en reguliere virtuele machines en hetzelfde voor Windows en Linux-VM's.

Houd er rekening mee dat Azure- en Linux bewaking op gastniveau zijn vereist voor een opslagaccount. Wanneer u bewaking op gastniveau, inschakelt als u een bestaand opslagaccount niet kiezen, is klikt u vervolgens een gemaakt voor u.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Bewaking op gastniveau op bestaande virtuele machines inschakelen

1. In **virtuele Machines**, uw lijst met uw virtuele machines weergeven en selecteer vervolgens een virtuele machine.
2. Onder **bewaking**, selecteer **diagnostische instellingen**.
3. Klik op de instellingenpagina van diagnostische gegevens **bewaking op gastniveau inschakelen**.  
    ![Niveau bewaking van Gast op de pagina overzicht inschakelen](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Na een paar minuten is de Azure diagnostics-agent geïnstalleerd op de virtuele machine. Een set van metrische gegevens worden toegevoegd. Vernieuw de pagina. De toegevoegde prestatiemeteritems worden weergegeven op het tabblad Overzicht.
5. Selecteer onder controle, **metrische gegevens**.
6. In de grafiek met metrische gegevens onder **metriek Namespace**, selecteer **gast (klassiek)** .
7. In de lijst met metrische gegevens, kunt u alle van de beschikbare prestatiemeteritems weergeven voor de Gast-VM.  
    ![overzicht van voorbeeld van de uitgebreide metrische gegevens](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Bewaking op gastniveau op nieuwe virtuele machines inschakelen

Wanneer u nieuwe virtuele machines, op het tabblad beheer maakt, selecteert u **op** voor **besturingssysteem Gast diagnostics**.

![Diagnostische gegevens van Guest OS ingesteld op aan](./media/azure-vm-extended-metrics/new-enable-diag.png)

Zie voor meer informatie over het inschakelen van uitgebreide metrische gegevens voor Azure virtual machines [begrip en met behulp van de Azure Linux agent](../../virtual-machines/extensions/agent-linux.md) en [overzicht van Azure Virtual Machine Agent](../../virtual-machines/extensions/agent-windows.md).

## <a name="resource-manager-credentials"></a>Resource Manager-referenties

Nadat u uitgebreide metrische gegevens inschakelt, zorg ervoor dat Cloudyn toegang tot heeft uw [Resource Manager-referenties](../../cost-management/activate-subs-accounts.md). Uw referenties zijn vereist voor Cloudyn te verzamelen en weergeven van prestatiegegevens voor uw VM's. Ze worden ook gebruikt om te maken van kosten optimalisatie aanbevelingen. Cloudyn moet ten minste drie dagen van de prestatiegegevens van een exemplaar om te bepalen of een kandidaat voor een aanbeveling downsizing.

## <a name="enable-vm-metrics-with-a-script"></a>De metrische gegevens van een virtuele machine met een script inschakelen

U kunt metrische gegevens van virtuele machine met Azure PowerShell-scripts inschakelen. Wanneer u veel virtuele machines die u wilt inschakelen, metrische gegevens hebt, kunt u een script kunt gebruiken om het proces te automatiseren. Van de voorbeeldscripts zijn op GitHub op [Azure inschakelen Diagnostics](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Metrische gegevens weergeven-prestaties van Azure

Als u wilt weergeven van metrische gegevens voor prestaties op uw Azure-exemplaren in de Cloudyn-portal, gaat u naar **activa** > **Compute** > **exemplaar Explorer**. In de lijst met VM-exemplaren, vouw een exemplaar en vouw vervolgens een resource om details te bekijken.

![van de voorbeeldinformatie die wordt weergegeven in de Verkenner-exemplaar](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Volgende stappen

- Als u dit nog niet hebt Azure Resource Manager API-toegang al ingeschakeld voor uw accounts, gaat u verder met [Activate Azure-abonnementen en accounts](../../cost-management/activate-subs-accounts.md).
