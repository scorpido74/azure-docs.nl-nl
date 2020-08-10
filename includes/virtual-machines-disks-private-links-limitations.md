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
ms.openlocfilehash: 138e7efd33ee36a77fd56deda155a29017432302
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420279"
---
- Er kan slechts één virtueel netwerk aan een object voor schijftoegang worden gekoppeld.
- Het virtuele netwerk moet zich in hetzelfde abonnement als uw object voor schijftoegang bevinden om ze te koppelen.
- Er kunnen maximaal 10 schijven of momentopnamen worden geïmporteerd of geëxporteerd op hetzelfde moment met hetzelfde object voor schijftoegang.
- U kunt geen handmatige goedkeuring aanvragen om een virtueel netwerk te koppelen aan een object voor schijftoegang.
- De differentiële mogelijkheid wordt niet ondersteund voor incrementele momentopnamen die zijn gekoppeld aan een object voor schijftoegang.
- U kunt AzCopy niet gebruiken voor het downloaden van via Private Links beveiligde VHD van een schijf/momentopname naar een opslagaccount. U kunt AzCopy wel gebruiken om VHD te downloaden naar uw virtuele machines.
