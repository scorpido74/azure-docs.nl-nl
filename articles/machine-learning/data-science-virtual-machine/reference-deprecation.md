---
title: 'Naslag informatie: Data Science Virtual Machine-afbeeldings afschaffing'
titleSuffix: Azure Data Science Virtual Machine
description: Details over afschaffing van invloed op de Azure-Data Science Virtual Machine
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525784"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Verwijzing: afschaffing van DSVM-installatie kopieën

Hieronder bespreken we suggesties voor het oplossen van toekomstige afschaffings op de Azure-Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: gegevens schijven migreren

De ondersteuning van de Windows 2012 DSVM-installatie kopie op 31 december 2019 wordt gestopt. Voer de volgende stappen uit als u een gegevens schijf van uw bestaande Windows 2012-DSVM naar een Windows 2016 DSVM wilt migreren:

1. Maak een nieuwe Windows 2016-DSVM en volg de instructies die [hier](./provision-vm.md#create-your-dsvm)worden weer gegeven.
1. Koppel bestaande gegevens schijven uit uw Windows 2012-installatie kopie met behulp van [deze instructies](../../virtual-machines/windows/detach-disk.md).
1. Koppel de schijf uit de vorige stap aan uw Windows 2016-installatie kopie met behulp van [deze instructies](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
