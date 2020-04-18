---
title: Red Hat Enterprise Linux-afbeeldingen in Azure | Microsoft Documenten
description: Meer informatie over Red Hat Enterprise Linux-afbeeldingen in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 028c30fced14a60af9f5683e6c6e087b15591735
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605485"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>RHEL-afbeeldingen (Red Hat Enterprise Linux) beschikbaar in Azure
Azure biedt een verscheidenheid aan RHEL-afbeeldingen voor verschillende use cases.

> [!NOTE]
> Alle RHEL-afbeeldingen zijn beschikbaar in azure public- en Azure Government-clouds. Ze zijn niet beschikbaar in Azure China-clouds.

## <a name="list-of-rhel-images"></a>Lijst met RHEL-afbeeldingen
Dit is een lijst met RHEL-afbeeldingen die beschikbaar zijn in Azure. Tenzij anders vermeld, zijn alle afbeeldingen LVM-partitioned en gekoppeld aan reguliere RHEL-repositories (niet EUS, niet E4S). De volgende afbeeldingen zijn momenteel beschikbaar voor algemeen gebruik:

Aanbieding| SKU | Partitionering | Inrichten | Opmerkingen
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | Raw    | Linux-agent |
|             | 6.8      | Raw    | Linux-agent |
|             | 6.9      | Raw    | Linux-agent |
|             | 6.10     | Raw    | Linux-agent |
|             | 7-RAW    | Raw    | Linux-agent | RHEL 7.x familie van beelden. <br> Standaard gekoppeld aan reguliere repositories (niet EUS).
|             | 7-LVM    | Lvm    | Linux-agent | RHEL 7.x familie van beelden. <br> Standaard gekoppeld aan reguliere repositories (niet EUS). Als u op zoek bent naar een standaard RHEL-afbeelding om te implementeren, gebruikt u deze set afbeeldingen en/of de generatie 2-tegenhanger.
|             | 7lvm-gen2| Lvm    | Linux-agent | Generatie 2, RHEL 7.x familie van beelden. <br> Standaard gekoppeld aan reguliere repositories (niet EUS). Als u op zoek bent naar een standaard RHEL-afbeelding om te implementeren, gebruikt u deze set afbeeldingen en/of de generatie 1-tegenhanger.
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7.x familie van beelden. <br> Standaard gekoppeld aan reguliere repositories (niet EUS).
|             | 7.2      | Raw    | Linux-agent |
|             | 7.3      | Raw    | Linux-agent |
|             | 7.4      | Raw    | Linux-agent | Vanaf april 2019 standaard verbonden aan EUS-repositories.
|             | 74-gen2  | Raw    | Linux-agent | Standaard gekoppeld aan EUS-repositories.
|             | 7,5      | Raw    | Linux-agent | Vanaf juni 2019 standaard verbonden aan EUS-repositories.
|             | 75-gen2  | Raw    | Linux-agent | Standaard gekoppeld aan EUS-repositories.
|             | 7.6      | Raw    | Linux-agent | Vanaf mei 2019 standaard verbonden aan EUS-repositories.
|             | 76-gen2  | Raw    | Linux-agent | Standaard gekoppeld aan EUS-repositories.
|             | 7.7      | Lvm    | Linux-agent | Standaard gekoppeld aan EUS-repositories.
|             | 8-LVM    | Lvm    | Linux-agent | RHEL 8.x familie van beelden. Gekoppeld aan reguliere repositories.
|             | 8-lvm-gen2| Lvm    | Linux-agent | Hyper-V Generation 2 - RHEL 8.x familie van beelden. Gekoppeld aan reguliere repositories.
|             | 8        | Lvm    | Linux-agent | RHEL 8.0 afbeeldingen
|             | 8-gen2   | Lvm    | Linux-agent | Hyper-V Generation 2 - RHEL 8.0 beelden.
|             | 8.1      | Lvm    | Linux-agent | RHEL 8.1 beelden. Momenteel gekoppeld aan reguliere repositories.
|             | 81gen2   | Lvm    | Linux-agent | Hyper-V Generation 2 - RHEL 8.1 beelden. Momenteel gekoppeld aan reguliere repositories.
RHEL-SAP      | 7.4      | Lvm    | Linux-agent | RHEL 7.4 voor SAP HANA en Business Apps. Gekoppeld aan E4S repositories, zal een premie in rekening brengen voor SAP en RHEL, evenals de base compute fee.
|             | 74sap-gen2| Lvm    | Linux-agent | RHEL 7.4 voor SAP HANA en Business Apps. Generatie 2 beeld. Gekoppeld aan E4S repositories, zal een premie in rekening brengen voor SAP en RHEL, evenals de base compute fee.
|             | 7,5       | Lvm    | Linux-agent | RHEL 7.5 voor SAP HANA en Business Apps. Gekoppeld aan E4S repositories, zal een premie in rekening brengen voor SAP en RHEL, evenals de base compute fee.
|             | 75sap-gen2| Lvm    | Linux-agent | RHEL 7.5 voor SAP HANA en Business Apps. Generatie 2 beeld. Gekoppeld aan E4S repositories, zal een premie in rekening brengen voor SAP en RHEL, evenals de base compute fee.
|             | 7.6       | Lvm    | Linux-agent | RHEL 7.6 voor SAP HANA en Business Apps. Gekoppeld aan E4S repositories, zal een premie in rekening brengen voor SAP en RHEL, evenals de base compute fee.
|             | 76sap-gen2| Lvm    | Linux-agent | RHEL 7.6 voor SAP HANA en Business Apps. Generatie 2 beeld. Gekoppeld aan E4S repositories, zal een premie in rekening brengen voor SAP en RHEL, evenals de base compute fee.
|             | 7.7       | Lvm    | Linux-agent | RHEL 7.7 voor SAP HANA en Business Apps. Gekoppeld aan E4S repositories, zal een premie in rekening brengen voor SAP en RHEL, evenals de base compute fee.
RHEL-SAP-HANA | 6.7       | Raw    | Linux-agent | RHEL 6.7 voor SAP HANA. Verouderd ten gunste van de RHEL-SAP beelden.
|             | 7.2       | Lvm    | Linux-agent | RHEL 7.2 voor SAP HANA. Verouderd ten gunste van de RHEL-SAP beelden.
|             | 7.3       | Lvm    | Linux-agent | RHEL 7.3 voor SAP HANA. Verouderd ten gunste van de RHEL-SAP beelden.
RHEL-SAP-APPS | 6.8       | Raw    | Linux-agent | RHEL 6.8 voor SAP Business Applications. Verouderd ten gunste van de RHEL-SAP beelden.
|             | 7.3       | Lvm    | Linux-agent | RHEL 7.3 voor SAP Business Applications. Verouderd ten gunste van de RHEL-SAP beelden.
RHEL-HA       | 7.4       | Lvm    | Linux-agent | RHEL 7.4 met HA Add-On. Brengt een premie in rekening voor HA en RHEL bovenop de basisrekenkosten.
|             | 7,5       | Lvm    | Linux-agent | RHEL 7.5 met HA Add-On. Brengt een premie in rekening voor HA en RHEL bovenop de basisrekenkosten.
|             | 7.6       | Lvm    | Linux-agent | RHEL 7.6 met HA Add-On. Brengt een premie in rekening voor HA en RHEL bovenop de basisrekenkosten.
RHEL-SAP-HA   | 7.4          | Lvm    | Linux-agent | RHEL 7.4 voor SAP met HA en Update Services. Gekoppeld aan E4S-repositories. Brengt een premie in rekening voor SAP- en HA-repositories en RHEL, bovenop de basiscomputekosten.
|             | 74sapha-gen2 | Lvm    | Linux-agent | RHEL 7.4 voor SAP met HA en Update Services. Generatie 2 beeld. Gekoppeld aan E4S-repositories. Brengt een premie in rekening voor SAP- en HA-repositories en RHEL, bovenop de basiscomputekosten.
|             | 7,5          | Lvm    | Linux-agent | RHEL 7.5 voor SAP met HA en Update Services. Gekoppeld aan E4S-repositories. Brengt een premie in rekening voor SAP- en HA-repositories en RHEL, bovenop de basiscomputekosten.
|             | 7.6          | Lvm    | Linux-agent | RHEL 7.6 voor SAP met HA en Update Services. Gekoppeld aan E4S-repositories. Brengt een premie in rekening voor SAP- en HA-repositories en RHEL, bovenop de basiscomputekosten.
|             | 76sapha-gen2 | Lvm    | Linux-agent | RHEL 7.6 voor SAP met HA en Update Services. Generatie 2 beeld. Gekoppeld aan E4S-repositories. Brengt een premie in rekening voor SAP- en HA-repositories en RHEL, bovenop de basiscomputekosten.
|             | 7.7          | Lvm    | Linux-agent | RHEL 7.7 voor SAP met HA en Update Services. Gekoppeld aan E4S-repositories. Brengt een premie in rekening voor SAP- en HA-repositories en RHEL, bovenop de basiscomputekosten.
|             | 77sapha-gen2 | Lvm    | Linux-agent | RHEL 7.7 voor SAP met HA en Update Services. Generatie 2 beeld. Gekoppeld aan E4S-repositories. Brengt een premie in rekening voor SAP- en HA-repositories en RHEL, bovenop de basiscomputekosten.
rhel-byos     |rhel-lvm74| Lvm    | Linux-agent | RHEL 7.4 BYOS-afbeeldingen, die niet zijn gekoppeld aan een bron van updates, brengen geen RHEL-premie in rekening.
|             |rhel-lvm75| Lvm    | Linux-agent | RHEL 7.5 BYOS-afbeeldingen, die niet zijn gekoppeld aan een bron van updates, brengen geen RHEL-premie in rekening.
|             |rhel-lvm76| Lvm    | Linux-agent | RHEL 7.6 BYOS-afbeeldingen, die niet zijn gekoppeld aan een bron van updates, brengen geen RHEL-premie in rekening.
|             |rhel-lvm77| Lvm    | Linux-agent | RHEL 7.7 BYOS-afbeeldingen, die niet zijn gekoppeld aan een bron van updates, brengen geen RHEL-premie in rekening.
|             |rhel-lvm8 | Lvm    | Linux-agent | RHEL 8 BYOS-afbeeldingen (RHEL-secundaire versie wordt weergegeven in de waarde van de afbeeldingsversie), die niet is gekoppeld aan een bron van updates, brengt geen RHEL-premie in rekening.

> [!NOTE]
> Het RHEL-SAP-HANA productaanbod wordt door Red Hat als end of life beschouwd. Bestaande implementaties blijven normaal werken, maar Red Hat raadt klanten aan om van de RHEL-SAP-HANA-afbeeldingen naar de RHEL-SAP-HA-afbeeldingen te migreren, waaronder de SAP HANA-repositories en de HA-add-on. Meer details over het SAP-cloudaanbod van Red Hat zijn [hier](https://access.redhat.com/articles/3751271)beschikbaar.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [Red Hat-afbeeldingen in Azure](./redhat-images.md).
* Meer informatie over de [Red Hat Update Infrastructure](./redhat-rhui.md).
* Meer informatie over de [RHEL BYOS-aanbieding](./byos.md).
* Informatie over het ondersteuningsbeleid van Red Hat voor alle versies van RHEL is te vinden op de Red [Hat Enterprise Linux Life Cycle-pagina.](https://access.redhat.com/support/policy/updates/errata)
