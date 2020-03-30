---
title: 'Referentie: Bekende problemen & probleemoplossing'
titleSuffix: Azure Data Science Virtual  Machine
description: Een lijst met de bekende problemen, tijdelijke oplossingen en probleemoplossing voor Azure Data Science Virtual Machine
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206517"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Bekende problemen en het oplossen van problemen met de Azure Data Science Virtual Machine

Met dit artikel u fouten of fouten vinden of corrigeren die u tegenkomen bij het gebruik van de Virtual Machine Azure Data Science.

## <a name="python-package-installation-issues"></a>Problemen met de installatie van Python-pakketten

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Het installeren van pakketten met pip breekt afhankelijkheden op Linux

Gebruik `sudo pip install` in `pip install` plaats van bij het installeren van pakketten.

## <a name="disk-encryption-issues"></a>Schijfversleutelingsproblemen

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Schijfversleuteling mislukt op de Ubuntu DSVM

Azure Disk Encryption (ADE) wordt momenteel niet ondersteund op de Ubuntu DSVM. Als tijdelijke oplossing u overwegen [serverzijdeversleuteling van door Azure beheerde schijven te](../../virtual-machines/windows/disk-encryption.md)configureren.

## <a name="tool-appears-disabled"></a>Gereedschap wordt uitgeschakeld weergegeven

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V werkt niet op de Windows DSVM

Dat Hyper-V in eerste instantie niet werkt op Windows wordt verwacht gedrag. Voor de prestaties van het opstarten hebben we een aantal services uitgeschakeld. Hyper-V inschakelen:

1. De zoekbalk openen op uw Windows DSVM
1. Typ 'Services'.
1. Alle Hyper-V-services instellen op 'Handmatig'
1. Stel "Hyper-V Virtual Machine Management" in op "Automatisch"

Uw uiteindelijke scherm moet er als volgt uitzien:

   ![Hyper-V inschakelen](./media/workaround/hyperv-enable-dsvm.png)

