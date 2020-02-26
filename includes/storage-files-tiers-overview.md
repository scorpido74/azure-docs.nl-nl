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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597790"
---
Azure Files biedt twee verschillende opslag lagen, Premium en Standard, zodat u uw aandelen kunt aanpassen aan de prestatie-en prijs vereisten van uw scenario:

- **Premium-bestands shares**: Premium-bestands shares worden ondersteund door ssd's (Solid-state drives) en worden geïmplementeerd in het type **opslag account van FileStorage** . Premium-bestands shares bieden consistente hoge prestaties en lage latentie in milliseconden voor de meeste i/o-bewerkingen, voor i/o-intensieve workloads. Dit maakt ze geschikt voor een groot aantal werk belastingen, zoals data bases, hosting van websites en ontwikkel omgevingen. Premium-bestands shares zijn alleen beschikbaar in een ingericht facturerings model. Zie [Wat is inrichten voor Premium-bestands shares](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)? voor meer informatie over het ingerichte facturerings model voor Premium-bestands shares.
- **Standaard bestands shares**: standaard bestands shares worden ondersteund door harde schijven (hdd's) en worden geïmplementeerd in het type **opslag account voor algemeen gebruik (versie 2) (GPv2)** . Standaard bestands shares bieden betrouw bare prestaties voor i/o-workloads die minder gevoelig zijn voor prestatie verschillen, zoals bestands shares voor algemene doel einden en ontwikkel-en test omgevingen. Standaard bestands shares zijn alleen beschikbaar in een facturerings model met betalen per gebruik.