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
ms.openlocfilehash: ebbdd89d3ef41d4fb40197cbd83038b5cbc02073
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230149"
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
> Uitgebreide metrische gegevensverzameling wordt alleen ondersteund met Azure-bewaking op gastniveau. Cloudyn is niet compatibel met de [log Analytics-agent](../azure-monitor/platform/agents-overview.md). 

## <a name="determine-whether-extended-metrics-are-enabled"></a>Bepalen of uitgebreide metrische gegevens zijn ingeschakeld

1. Meld u aan bij Azure Portal op https://portal.azure.com.
2. Selecteer onder **virtuele machines**een virtuele machine en selecteer vervolgens onder **bewaking** **metrische gegevens**. Een lijst met beschikbare metrische gegevens wordt weergegeven.
3. Sommige metrische gegevens selecteren en een grafiek voor deze gegevens worden weergegeven.  
    ![Voorbeeld van de metrische gegevens: host percentage CPU](./media/azure-vm-extended-metrics/metric01.png)

In het voorgaande voorbeeld wordt een beperkte set van standaard metrische gegevens zijn beschikbaar voor uw hosts, maar geheugen metrische gegevens zijn niet. Geheugen metrische gegevens maken deel uit van de uitgebreide metrische gegevens. In dit geval zijn uitgebreide metrische gegevens niet ingeschakeld voor de virtuele machine. U moet enkele extra stappen voor het inschakelen van uitgebreide metrische gegevens uitvoeren. De volgende informatie helpt u om hen in staat.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Uitgebreide metrische gegevens in Azure portal inschakelen

Standaard metrische gegevens zijn computer metrische gegevens voor hosts. Het _percentage CPU_ -metriek is een voor beeld. Er zijn ook eenvoudige metrische gegevens voor de Gast-VM's en uitgebreide metrische gegevens ook genoemd. Voor beelden van uitgebreide metrische gegevens zijn _\Memory\% toegewezen bytes in gebruik_ en _\Memory\Available bytes_.

Het inschakelen van uitgebreide metrische gegevens is eenvoudig. Voor elke virtuele machine, schakel de bewaking op gastniveau. Wanneer u de bewaking op gastniveau inschakelt, wordt de Azure diagnostics-agent is geïnstalleerd op de virtuele machine. Standaard een set met uitgebreide metrische gegevens worden toegevoegd. Het volgende proces is hetzelfde voor klassieke en reguliere virtuele machines en hetzelfde voor Windows en Linux-VM's.

Houd er rekening mee dat Azure- en Linux bewaking op gastniveau zijn vereist voor een opslagaccount. Wanneer u bewaking op gastniveau, inschakelt als u een bestaand opslagaccount niet kiezen, is klikt u vervolgens een gemaakt voor u.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Bewaking op gastniveau op bestaande virtuele machines inschakelen

1. In **virtual machines**bekijkt u de lijst met uw vm's en selecteert u vervolgens een virtuele machine.
2. Onder **bewaking**selecteert u **Diagnostische instellingen**.
3. Klik op de pagina Diagnostische instellingen op **bewaking op gast niveau inschakelen**.  
    ![op het tabblad Overzicht de bewaking op gast niveau in te scha kelen](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Na een paar minuten is de Azure diagnostics-agent geïnstalleerd op de virtuele machine. Een set van metrische gegevens worden toegevoegd. Vernieuw de pagina. De toegevoegde prestatiemeteritems worden weergegeven op het tabblad Overzicht.
5. Onder bewaking selecteert u **metrische gegevens**.
6. Selecteer in het grafiek metrische gegevens onder **metrische naam ruimte**de optie **gast (klassiek)** .
7. In de lijst met metrische gegevens, kunt u alle van de beschikbare prestatiemeteritems weergeven voor de Gast-VM.  
    ![overzicht van voorbeeld van de uitgebreide metrische gegevens](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Bewaking op gastniveau op nieuwe virtuele machines inschakelen

Wanneer u nieuwe Vm's maakt, selecteert u op het tabblad beheer de optie **ingeschakeld** voor **Diagnostische gegevens van het besturings systeem**.

![Diagnostische gegevens van Guest OS ingesteld op aan](./media/azure-vm-extended-metrics/new-enable-diag.png)

Zie het overzicht van [Azure Linux agent](../virtual-machines/extensions/agent-linux.md) en [Azure virtual machine agent](../virtual-machines/extensions/agent-windows.md)voor meer informatie over het inschakelen van uitgebreide metrische gegevens voor virtuele Azure-machines.

## <a name="resource-manager-credentials"></a>Resource Manager-referenties

Nadat u uitgebreide metrische gegevens hebt ingeschakeld, moet u ervoor zorgen dat Cloudyn toegang heeft tot uw [Resource Manager-referenties](activate-subs-accounts.md). Uw referenties zijn vereist voor Cloudyn te verzamelen en weergeven van prestatiegegevens voor uw VM's. Ze worden ook gebruikt om te maken van kosten optimalisatie aanbevelingen. Cloudyn moet ten minste drie dagen van de prestatiegegevens van een exemplaar om te bepalen of een kandidaat voor een aanbeveling downsizing.

## <a name="enable-vm-metrics-with-a-script"></a>De metrische gegevens van een virtuele machine met een script inschakelen

U kunt metrische gegevens van virtuele machine met Azure PowerShell-scripts inschakelen. Wanneer u veel virtuele machines die u wilt inschakelen, metrische gegevens hebt, kunt u een script kunt gebruiken om het proces te automatiseren. Voorbeeld scripts bevinden zich op GitHub op [Azure Enable Diagnostics](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Metrische gegevens weergeven-prestaties van Azure

Als u metrische gegevens over prestaties wilt weer geven in uw Azure-instanties in de Cloudyn-Portal, gaat u naar **Assets** > **Compute** > **instance Explorer**. In de lijst met VM-exemplaren, vouw een exemplaar en vouw vervolgens een resource om details te bekijken.

![van de voorbeeldinformatie die wordt weergegeven in de Verkenner-exemplaar](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Volgende stappen

- Als u Azure Resource Manager API-toegang voor uw accounts nog niet hebt ingeschakeld, gaat u verder met het [activeren van Azure-abonnementen en-accounts](activate-subs-accounts.md).
