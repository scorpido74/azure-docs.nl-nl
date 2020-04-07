---
title: 'Referentie: Data Science Virtual Machine Image Deprecation'
titleSuffix: Azure Data Science Virtual Machine
description: Details over afschrijvingen die van invloed zijn op de Azure Data Science Virtual Machine
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754769"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Referentie: Afschrijving van DSVM-afbeeldingen

Hieronder bespreken we suggesties voor het omgaan met aankomende afschrijvingen op de Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: gegevensschijven migreren

We stoppen met het ondersteunen van de Windows 2012 DSVM-afbeelding op 31 december 2019. Als u een gegevensschijf van uw bestaande Windows 2012 DSVM wilt migreren naar een DSVM van Windows 2016, neemt u de volgende stappen:

1. Maak een nieuwe Windows 2016 DSVM, volgens de [instructies hier](./provision-vm.md#create-your-dsvm)weergegeven .
1. Bestaande gegevensschijven loskoppelen van uw Windows 2012-afbeelding met behulp van [deze instructies](../../virtual-machines/windows/detach-disk.md).
1. Bevestig de schijf van de vorige stap aan uw Windows 2016-afbeelding met behulp van [deze instructies.](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)

## <a name="centos"></a>CentOS

Nieuwe gebruikers moeten de meest recente Ubuntu- of Windows-afbeeldingen gebruiken. CentOS blijft beschikbaar voor gebruik met bestaande oplossingssjablonen.