---
title: Azure Network Watcher-resources verplaatsen | Microsoft Docs
description: Azure Network Watcher-resources verplaatsen naar verschillende regio's
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2020
ms.author: damendo
ms.openlocfilehash: 97349071fee6a95623e5b5efdc0c9818cfe7b811
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388399"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Azure Network Watcher-resources verplaatsen tussen regio's

## <a name="moving-the-network-watcher-resource"></a>De Network Watcher resource verplaatsen
De Network Watcher resource vertegenwoordigt de back-end-service voor Network Watcher en wordt volledig beheerd door Azure. Klanten hoeven deze niet te beheren. De Move-bewerking wordt niet ondersteund voor deze resource.

## <a name="moving-child-resources-of-network-watcher"></a>Onderliggende resources van Network Watcher verplaatsen
Het verplaatsen van resources tussen regio's wordt momenteel niet ondersteund voor een onderliggende resource van het `*networkWatcher*` resource type.

## <a name="next-steps"></a>Volgende stappen
* Lees het [Network Watcher overzicht](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)
* Raadpleeg de [Veelgestelde vragen over Network Watcher](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions)
