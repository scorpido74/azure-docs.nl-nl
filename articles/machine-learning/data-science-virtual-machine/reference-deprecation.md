---
title: 'Referentie: Data Science Virtual Machine Image Deprecation'
titleSuffix: Azure Data Science Virtual Machine
description: Details over afschrijvingen die van invloed zijn op de Azure Data Science Virtual Machine
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525784"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Referentie: Afschrijving van DSVM-afbeeldingen

Hieronder bespreken we suggesties voor het omgaan met aankomende afschrijvingen op de Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: gegevensschijven migreren

We stoppen met het ondersteunen van de Windows 2012 DSVM-afbeelding op 31 december 2019. Als u een gegevensschijf van uw bestaande Windows 2012 DSVM wilt migreren naar een DSVM van Windows 2016, neemt u de volgende stappen:

1. Maak een nieuwe Windows 2016 DSVM, volgens de [instructies hier](./provision-vm.md#create-your-dsvm)weergegeven .
1. Bestaande gegevensschijven loskoppelen van uw Windows 2012-afbeelding met behulp van [deze instructies](../../virtual-machines/windows/detach-disk.md).
1. Bevestig de schijf van de vorige stap aan uw Windows 2016-afbeelding met behulp van [deze instructies.](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)
