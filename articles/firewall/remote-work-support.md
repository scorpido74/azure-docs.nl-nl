---
title: Ondersteuning voor extern werken op Azure Firewall
description: In dit artikel ziet u hoe Azure Firewall uw vereisten voor externe arbeidskrachten kan ondersteunen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289639"
---
# <a name="azure-firewall-remote-work-support"></a>Ondersteuning voor extern werken op Azure Firewall

Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw virtuele Azure-netwerkbronnen beschermt. Het is een volledig stateful firewall als een service met ingebouwde hoge beschikbaarheid en onbeperkte cloudschaalbaarheid. 

## <a name="firewall-rules"></a>Firewall-regels

U Azure Firewall gebruiken om uw inkomende RDP-toegang voor virtuele bureaubladinfrastructuur (VDI) tot uw Azure-virtuele netwerk te beveiligen met behulp van Azure Firewall [DNAT-regels.](rule-processing.md) Windows Virtual Desktop (WVD) vereist geen binnenkomende toegang tot uw virtuele netwerk. U moet echter een reeks uitgaande netwerkverbindingen toestaan voor de virtuele WVD-machines die in uw virtuele netwerk worden uitgevoerd. Zie Wat is Windows Virtual Desktop voor meer [informatie?](../virtual-desktop/overview.md#requirements)

U deze uitgaande toegang configureren met azure firewall-toepassingsregels. Zie [Zelfstudie: Azure Firewall implementeren en configureren met behulp van de Azure-portal](tutorial-firewall-deploy-portal.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/).