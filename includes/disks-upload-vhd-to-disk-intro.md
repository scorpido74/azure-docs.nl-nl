---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420952"
---
In dit artikel wordt uitgelegd hoe u een VHD van uw lokale machine uploadt naar een door Azure beheerde schijf of een beheerde schijf naar een andere regio kopieert met AzCopy. Dit proces, directe upload, stelt u ook in staat om een VHD tot 32 TiB in grootte direct te uploaden naar een beheerde schijf. Momenteel wordt direct uploadondersteund voor standaard HDD, standaard SSD en premium SSD managed disks. Het wordt nog niet ondersteund voor ultra schijven.

Als u een back-upoplossing voor IaaS-vm's in Azure aanbiedt, raden we u aan directe uploadte te gebruiken om back-ups van klanten te herstellen naar beheerde schijven. Bij het uploaden van een VHD van een bron buiten Azure, zouden de snelheden afhankelijk zijn van uw lokale bandbreedte. Bij het uploaden of kopiëren van een Azure VM, dan is uw bandbreedte hetzelfde als standaard HDD's.