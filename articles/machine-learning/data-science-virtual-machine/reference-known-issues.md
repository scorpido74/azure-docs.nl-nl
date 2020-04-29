---
title: 'Naslag informatie: bekende problemen bij het oplossen van problemen &'
titleSuffix: Azure Data Science Virtual  Machine
description: Een lijst met bekende problemen, tijdelijke oplossingen en probleem oplossing voor Azure Data Science Virtual Machine ophalen
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78206517"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Bekende problemen en het oplossen van problemen met de Azure-Data Science Virtual Machine

Dit artikel helpt u bij het vinden en corrigeren van fouten of fouten die u kunt tegen komen wanneer u de Azure-Data Science Virtual Machine gebruikt.

## <a name="python-package-installation-issues"></a>Problemen met installatie van python-pakket

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Installeren van pakketten met PIP-onderbrekingen afhankelijkheden op Linux

Gebruik `sudo pip install` in plaats `pip install` van wanneer u pakketten installeert.

## <a name="disk-encryption-issues"></a>Schijfversleutelingsproblemen

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Schijf versleuteling mislukt op de Ubuntu-DSVM

Azure Disk Encryption (ADE) wordt momenteel niet ondersteund in de Ubuntu-DSVM. Als tijdelijke oplossing kunt u overwegen om [versleuteling aan de server zijde van Azure Managed disks](../../virtual-machines/windows/disk-encryption.md)te configureren.

## <a name="tool-appears-disabled"></a>Hulp programma wordt uitgeschakeld weer gegeven

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V werkt niet op de Windows-DSVM

De werking van Hyper-V in eerste instantie werkt niet in Windows. Sommige services zijn uitgeschakeld voor de prestaties van het systeem. Hyper-V inschakelen:

1. De zoek balk openen in uw Windows-DSVM
1. Typ ' Services ',
1. Alle Hyper-V-Services instellen op hand matig
1. "Hyper-V virtual machine management" instellen op automatisch

Het laatste scherm moet er als volgt uitzien:

   ![Hyper-V inschakelen](./media/workaround/hyperv-enable-dsvm.png)

