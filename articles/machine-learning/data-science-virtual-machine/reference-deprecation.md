---
title: 'Naslag informatie: Data Science Virtual Machine-afbeeldings afschaffing'
titleSuffix: Azure Data Science Virtual Machine
description: Details over afschaffing van invloed op de Azure-Data Science Virtual Machine
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80754769"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Verwijzing: afschaffing van DSVM-installatie kopieën

Hieronder bespreken we suggesties voor het oplossen van toekomstige afschaffings op de Azure-Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: gegevens schijven migreren

De ondersteuning van de Windows 2012 DSVM-installatie kopie op 31 december 2019 wordt gestopt. Voer de volgende stappen uit als u een gegevens schijf van uw bestaande Windows 2012-DSVM naar een Windows 2016 DSVM wilt migreren:

1. Maak een nieuwe Windows 2016-DSVM en volg de instructies die [hier](./provision-vm.md#create-your-dsvm)worden weer gegeven.
1. Koppel bestaande gegevens schijven uit uw Windows 2012-installatie kopie met behulp van [deze instructies](../../virtual-machines/windows/detach-disk.md).
1. Koppel de schijf uit de vorige stap aan uw Windows 2016-installatie kopie met behulp van [deze instructies](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

## <a name="centos"></a>CentOS

Nieuwe gebruikers moeten de meest recente Ubuntu-of Windows-installatie kopieën gebruiken. CentOS blijft beschikbaar voor gebruik met bestaande oplossings sjablonen.