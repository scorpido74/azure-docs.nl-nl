---
title: Installatie kopieën Red Hat Enterprise Linux in azure | Microsoft Docs
description: Meer informatie over Red Hat Enterprise Linux installatie kopieën in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 32df17ffed400af80eadadbdeaafbaa1e3e1dbce
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941707"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Red Hat Enterprise Linux-installatie kopieën (RHEL) die beschikbaar zijn in azure
Azure biedt een aantal RHEL-installatie kopieën voor verschillende use cases.

## <a name="list-of-rhel-images"></a>Lijst met RHEL-installatie kopieën
Dit is een lijst met RHEL-installatie kopieën die beschikbaar zijn in Azure. Tenzij anders vermeld, worden alle installatie kopieën gepartitioneerd en gekoppeld aan gewone RHEL-opslag plaatsen (niet EUS, niet E4S). De volgende installatie kopieën zijn momenteel beschikbaar voor algemeen gebruik:

Aanbieding| SKU | Partitionering | Inrichting | Opmerkingen
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux-agent |
|             | 6.8      | RAW    | Linux-agent |
|             | 6.9      | RAW    | Linux-agent |
|             | 6.10     | RAW    | Linux-agent |
|             | 7-RAW    | RAW    | Linux-agent | RHEL 7. x-familie van installatie kopieën. <br> Standaard gekoppeld aan reguliere opslag plaatsen (niet EUS).
|             | 7-LVM    | LVM    | Linux-agent | RHEL 7. x-familie van installatie kopieën. <br> Standaard gekoppeld aan reguliere opslag plaatsen (niet EUS).
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7. x-familie van installatie kopieën. <br> Standaard gekoppeld aan reguliere opslag plaatsen (niet EUS).
|             | 7.2      | RAW    | Linux-agent |
|             | 7.3      | RAW    | Linux-agent |
|             | 7.4      | RAW    | Linux-agent | Is vanaf april 2019 standaard gekoppeld aan EUS-opslag plaatsen.
|             | 7.5      | RAW    | Linux-agent | Is vanaf juni 2019 standaard gekoppeld aan EUS-opslag plaatsen.
|             | 7.6      | RAW    | Linux-agent | Gekoppeld aan EUS-opslag plaatsen standaard vanaf mei 2019.
|             | 7.7      | LVM    | Linux-agent | Standaard gekoppeld aan EUS-opslag plaatsen.
|             | 8        | LVM    | Linux-agent | RHEL 8. x-familie van installatie kopieën
|             | 8-Gen2   | LVM    | Linux-agent | Hyper-V-generatie 2-RHEL 8. x-familie van installatie kopieën.
RHEL-SAP      | 7.4      | LVM    | Linux-agent | RHEL 7,4 voor SAP HANA en zakelijke apps. Aan de E4S-opslag plaatsen wordt een Premium voor SAP en RHEL, en de basis reken kosten in rekening gebracht.
|             | 7.5      | LVM    | Linux-agent | RHEL 7,5 voor SAP HANA en zakelijke apps. Aan de E4S-opslag plaatsen wordt een Premium voor SAP en RHEL, en de basis reken kosten in rekening gebracht.
|             | 7.6      | LVM    | Linux-agent | RHEL 7,5 voor SAP HANA en zakelijke apps. Aan de E4S-opslag plaatsen wordt een Premium voor SAP en RHEL, en de basis reken kosten in rekening gebracht.
|             | 7.7      | LVM    | Linux-agent | RHEL 7,5 voor SAP HANA en zakelijke apps. Aan de E4S-opslag plaatsen wordt een Premium voor SAP en RHEL, en de basis reken kosten in rekening gebracht.
RHEL-SAP-HANA | 6.7      | RAW    | Linux-agent | RHEL 6,7 voor SAP HANA. Verouderd in het voor deel van de RHEL-SAP-installatie kopieën.
|             | 7.2      | LVM    | Linux-agent | RHEL 7,2 voor SAP HANA. Verouderd in het voor deel van de RHEL-SAP-installatie kopieën.
|             | 7.3      | LVM    | Linux-agent | RHEL 7,3 voor SAP HANA. Verouderd in het voor deel van de RHEL-SAP-installatie kopieën.
RHEL-SAP-APPS | 6.8      | RAW    | Linux-agent | RHEL 6,8 voor SAP Business Applications. Verouderd in het voor deel van de RHEL-SAP-installatie kopieën.
|             | 7.3      | LVM    | Linux-agent | RHEL 7,3 voor SAP Business Applications. Verouderd in het voor deel van de RHEL-SAP-installatie kopieën.
RHEL-HA       | 7.4      | LVM    | Linux-agent | RHEL 7,4 met HA-invoeg toepassing. In wordt een Premium voor HA en RHEL boven op de basis reken kosten in rekening gebracht.
|             | 7.5      | LVM    | Linux-agent | RHEL 7,5 met HA-invoeg toepassing. In wordt een Premium voor HA en RHEL boven op de basis reken kosten in rekening gebracht.
|             | 7.6      | LVM    | Linux-agent | RHEL 7,6 met HA-invoeg toepassing. In wordt een Premium voor HA en RHEL boven op de basis reken kosten in rekening gebracht.
RHEL-SAP-HA   | 7.4      | LVM    | Linux-agent | RHEL 7,4 voor SAP met HA-invoeg toepassing. Gekoppeld aan E4S-opslag plaatsen. Berekent een Premium voor SAP-en HA-opslag plaatsen, evenals RHEL, bovenop de kosten voor basis berekeningen.
|             | 7.5      | LVM    | Linux-agent | RHEL 7,5 voor SAP met HA-invoeg toepassing. Gekoppeld aan E4S-opslag plaatsen. Berekent een Premium voor SAP-en HA-opslag plaatsen, evenals RHEL, bovenop de kosten voor basis berekeningen.
|             | 7.6      | LVM    | Linux-agent | RHEL 7,6 voor SAP met HA-invoeg toepassing. Gekoppeld aan E4S-opslag plaatsen. Berekent een Premium voor SAP-en HA-opslag plaatsen, evenals RHEL, bovenop de kosten voor basis berekeningen.
RHEL-BYOS     |RHEL-lvm74| LVM    | Linux-agent | RHEL 7,4 BYOS-installatie kopieën die niet zijn gekoppeld aan een bron van updates, brengen geen kosten in rekening voor een RHEL Premium.
|             |RHEL-lvm75| LVM    | Linux-agent | RHEL 7,5 BYOS-installatie kopieën die niet zijn gekoppeld aan een bron van updates, brengen geen kosten in rekening voor een RHEL Premium.
|             |RHEL-lvm76| LVM    | Linux-agent | RHEL 7,6 BYOS-installatie kopieën die niet zijn gekoppeld aan een bron van updates, brengen geen kosten in rekening voor een RHEL Premium.
|             |RHEL-lvm77| LVM    | Linux-agent | RHEL 7,7 BYOS-installatie kopieën die niet zijn gekoppeld aan een bron van updates, brengen geen kosten in rekening voor een RHEL Premium.
|             |RHEL-lvm8 | LVM    | Linux-agent | RHEL 8 BYOS-installatie kopieën (RHEL secundaire versie wordt weer gegeven in de waarde van de installatie kopie versie), niet gekoppeld aan een bron van updates, brengt geen RHEL Premium in rekening.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [Red Hat-afbeeldingen in azure](./redhat-images.md).
* Meer informatie over de [infra structuur voor Red Hat-updates](./redhat-rhui.md).
* Meer informatie over de [RHEL BYOS-aanbieding](./byos.md).
* Informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL vindt u op de pagina [levens cyclus van Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
