---
title: Script om kdump in te scha kelen in SAP HANA (grote exemplaren) | Microsoft Docs
description: Script om kdump in te scha kelen in SAP HANA (grote instanties) HLI type I, HLI type II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16dc15b4369904643d0138a4b8e5b94c47868d31
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204934"
---
# <a name="enable-kdump-service"></a>Kdump-service inschakelen

Dit document bevat informatie over het inschakelen van de kdump-service op Azure HANA grote instantie (**type I en type II**)

## <a name="supported-skus"></a>Ondersteunde Sku's

|  Type van de grote hoeveelheid Hana-exemplaren   |  BESTURINGSSYSTEEM leverancier   |  Versie van besturingssysteem pakket   |  SKU        |
|-----------------------------|--------------|-----------------------|-------------|
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |

## <a name="prerequisites"></a>Vereisten

- De kdump- `/var/crash` service maakt gebruik van Directory voor het schrijven van dumps, zorg ervoor dat de partitie overeenkomt met deze map voldoende ruimte heeft voor dumps.

## <a name="setup-details"></a>Details van de installatie

- Script voor het inschakelen van kdump vindt u [hier](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh)

- Dit script uitvoeren op een HANA grote instantie met behulp van de onderstaande opdracht

    > [!NOTE]
    > de sudo-bevoegdheid is vereist om deze opdracht uit te voeren.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Als de opdracht uitvoer kdump is ingeschakeld, start u het systeem opnieuw op om de wijziging toe te passen. de kdump is nu ingeschakeld. Start het systeem opnieuw op om de wijzigingen toe te passen.

- Als de uitvoer van de opdracht bepaalde bewerking niet kan uitvoeren, sluit u!!!!, en wordt de kdump-service niet ingeschakeld. Raadpleeg het [ondersteunings probleem](#support-issue)van de sectie.

## <a name="test-kdump"></a>Kdump testen

> [!NOTE]
>  Onder bewerking wordt een kernel-crash geactiveerd en wordt het systeem opnieuw opgestart.

- Kernel-crash activeren

    ```bash
    echo 1 > /proc/sys/kernel/sysrq
    echo c > /proc/sysrq-trigger
    ```

- Nadat het systeem opnieuw is opgestart, controleert u de `/var/crash` Directory op kernel crash logs.

- Als de `/var/crash` map bevat met de huidige datum, wordt de kdump ingeschakeld.

## <a name="support-issue"></a>Ondersteunings probleem

Als het script mislukt met een fout of kdump niet is ingeschakeld, verhoogt u de service aanvraag met het micro soft-ondersteunings team met de volgende details.

* HLI-abonnements-ID

* Servernaam

* BESTURINGSSYSTEEM leverancier

* Versie van het besturingssysteem

* Kernelversie
