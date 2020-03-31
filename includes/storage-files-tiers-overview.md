---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597790"
---
Azure Files biedt twee verschillende niveaus van opslag, premium en standaard, zodat u uw aandelen afstemmen op de prestatie- en prijsvereisten van uw scenario:

- **Premium bestandsaandelen**: Premium-bestandsaandelen worden ondersteund door solid-state drives (SSD's) en worden geïmplementeerd in het type **FileStorage-opslagaccount.** Premium bestandsshares bieden consistente hoge prestaties en lage latentie, binnen milliseconden met één cijfer voor de meeste IO-bewerkingen, voor IO-intensieve workloads. Dit maakt ze geschikt voor een breed scala aan workloads zoals databases, website hosting en ontwikkelomgevingen. Premium bestandsaandelen zijn alleen beschikbaar in een ingericht factureringsmodel. Zie [Inzicht in provisioning for premium file shares](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)voor meer informatie over het ingerichte factureringsmodel voor premium bestandsaandelen .
- **Standaardbestandsshares**: Standaardbestandsshares worden ondersteund door harde schijven (HDD's) en worden geïmplementeerd in het **type opslagaccounttype voor algemene toepassing versie 2 (GPv2).** Standaardbestandsshares bieden betrouwbare prestaties voor IO-workloads die minder gevoelig zijn voor prestatievariabiliteit, zoals bestandsshares voor algemene doeleinden en dev/testomgevingen. Standaardbestandsshares zijn alleen beschikbaar in een factureringsmodel voor betalen per gebruik.