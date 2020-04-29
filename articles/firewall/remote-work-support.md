---
title: Azure Firewall ondersteuning voor externe werk
description: In dit artikel wordt beschreven hoe Azure Firewall uw vereisten voor externe werk kracht kan ondersteunen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289639"
---
# <a name="azure-firewall-remote-work-support"></a>Azure Firewall ondersteuning voor externe werk

Azure Firewall is een beheerde, Cloud service voor netwerk beveiliging die de resources van uw Azure-netwerk beveiligt. Het is een volledig stateful firewall als een service met ingebouwde hoge beschikbaarheid en onbeperkte cloudschaalbaarheid. 

## <a name="firewall-rules"></a>Firewall-regels

U kunt Azure Firewall gebruiken om de inkomende RDP-toegang van uw virtuele desktop infrastructuur (VDI) met uw virtuele Azure-netwerk te beveiligen met behulp van Azure Firewall [DNAT-regels](rule-processing.md). Voor het virtuele bureau blad van Windows (WVD) hoeft u geen inkomende toegang te openen voor uw virtuele netwerk. U moet echter een reeks uitgaande netwerk verbindingen toestaan voor de virtuele WVD-machines die worden uitgevoerd in uw virtuele netwerk. Zie [Wat is Windows virtueel bureau blad?](../virtual-desktop/overview.md#requirements) voor meer informatie.

U kunt deze uitgaande toegang configureren met behulp van Azure Firewall toepassings regels. Zie voor meer informatie [zelf studie: Azure firewall implementeren en configureren met behulp van de Azure Portal](tutorial-firewall-deploy-portal.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [virtueel bureau blad van Windows](https://docs.microsoft.com/azure/virtual-desktop/).