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
ms.openlocfilehash: d022b1124146a1e506401e6cee257805e3a38fd3
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526543"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Bekende problemen en het oplossen van problemen met de Azure-Data Science Virtual Machine

Dit artikel helpt u bij het vinden en corrigeren van fouten of fouten die zijn opgetreden bij het gebruik van Azure Data Science Virtual Machine.

## <a name="python-package-installation-issues"></a>Problemen met installatie van python-pakket

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Installeren van pakketten met PIP-onderbrekingen afhankelijkheden op Linux

Gebruik `sudo pip install` in plaats van `pip install` bij het installeren van pakketten.

## <a name="disk-encryption-issues"></a>Problemen met schijf versleuteling

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Schijf versleuteling mislukt op de Ubuntu-DSVM

Azure Disk Encryption (ADE) wordt momenteel niet ondersteund in de Ubuntu-DSVM. Als tijdelijke oplossing kunt u [Azure Storage versleuteling configureren met door de klant beheerde sleutels](../../storage/common/storage-encryption-keys-portal.md).

## <a name="tool-appears-disabled"></a>Hulp programma wordt uitgeschakeld weer gegeven

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V werkt niet op de Windows-DSVM

Dit is normaal gedrag, voor opstart prestaties, hebben sommige services uitgeschakeld. Als u het opnieuw wilt inschakelen, opent u de zoek balk in uw Windows DSVM, typt u ' Services ' en vervolgens stelt u alle Hyper-V-Services in op ' hand matig ' en stelt u ' Hyper-V virtual machine management ' in op ' Automatic '.

Het laatste scherm moet er als volgt uitzien:

   ![Hyper-V inschakelen](./media/workaround/hyperv-enable-dsvm.png)

