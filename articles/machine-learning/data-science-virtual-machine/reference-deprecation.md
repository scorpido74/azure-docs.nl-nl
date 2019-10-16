---
title: 'Verwijzing: afschaffing van DSVM-afbeelding'
description: Details over afschaffing van invloed op de Azure-Data Science Virtual Machine (DSVM)
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 4968ac08b75141ed84994ca287215a34728232c4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333337"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Verwijzing: afschaffing van DSVM-installatie kopieën

Hieronder bespreken we suggesties voor het oplossen van toekomstige afschaffings op de Azure-Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: gegevens schijven migreren

De ondersteuning van de Windows 2012 DSVM-installatie kopie op 5 november 2019 wordt gestopt. Voer de volgende stappen uit als u een gegevens schijf van uw bestaande Windows 2012-DSVM naar een Windows 2016 DSVM wilt migreren:

1. Maak een nieuwe Windows 2016-DSVM en volg de instructies die [hier](./provision-vm.md#create-your-dsvm)worden weer gegeven.
1. Koppel bestaande gegevens schijven uit uw Windows 2012-installatie kopie met behulp van [deze instructies](../../virtual-machines/windows/detach-disk.md).
1. Koppel de schijf uit de vorige stap aan uw Windows 2016-installatie kopie met behulp van [deze instructies](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
