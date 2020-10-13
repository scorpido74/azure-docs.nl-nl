---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 293f0f459e1f1e464fdec16b76eaf08336c92e93
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377400"
---
- Er kan slechts één virtueel netwerk aan een object voor schijftoegang worden gekoppeld.
- Het virtuele netwerk moet zich in hetzelfde abonnement als uw object voor schijftoegang bevinden om ze te koppelen.
- Er kunnen maximaal 10 schijven of momentopnamen worden geïmporteerd of geëxporteerd op hetzelfde moment met hetzelfde object voor schijftoegang.
- U kunt geen handmatige goedkeuring aanvragen om een virtueel netwerk te koppelen aan een object voor schijftoegang.
- Incrementele momentopnamen kunnen niet worden geëxporteerd wanneer ze zijn gekoppeld aan een object voor schijftoegang.
- U kunt AzCopy niet gebruiken voor het downloaden van via Private Links beveiligde VHD van een schijf/momentopname naar een opslagaccount. U kunt AzCopy wel gebruiken om VHD te downloaden naar uw virtuele machines.
