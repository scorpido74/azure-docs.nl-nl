---
title: 'Verwijzing: afschaffing van DSVM-afbeelding'
description: Details over afschaffing van invloed op de Azure-Data Science Virtual Machine (DSVM)
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 252f9c66034dbadaf7a2e9e6f78665c26d414deb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456292"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Verwijzing: afschaffing van DSVM-installatie kopieën

Hieronder bespreken we suggesties voor het oplossen van toekomstige afschaffings op de Azure-Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: gegevens schijven migreren

De ondersteuning van de Windows 2012 DSVM-installatie kopie op 31 december 2019 wordt gestopt. Voer de volgende stappen uit als u een gegevens schijf van uw bestaande Windows 2012-DSVM naar een Windows 2016 DSVM wilt migreren:

1. Maak een nieuwe Windows 2016-DSVM en volg de instructies die [hier](./provision-vm.md#create-your-dsvm)worden weer gegeven.
1. Koppel bestaande gegevens schijven uit uw Windows 2012-installatie kopie met behulp van [deze instructies](../../virtual-machines/windows/detach-disk.md).
1. Koppel de schijf uit de vorige stap aan uw Windows 2016-installatie kopie met behulp van [deze instructies](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
