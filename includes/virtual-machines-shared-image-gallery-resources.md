---
title: Include-bestand
description: Include-bestand
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 433e909563602a2ef32b7986959b428c9afaf9f4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788984"
---
| Resource | Beschrijving|
|----------|------------|
| **Bron van installatie kopie** | Dit is een resource die kan worden gebruikt voor het maken van een **installatie kopie versie** in een galerie met installatie kopieën. Een installatie kopie bron kan bestaan uit een bestaande virtuele machine van Azure die is [gegeneraliseerde of gespecialiseerde](../articles/virtual-machines/windows/shared-image-galleries.md#generalized-and-specialized-images), een beheerde installatie kopie, een moment opname of een installatie kopie-versie in een andere galerie met installatie kopieën. |
| **Galerie met installatie kopieën** | Net als de Azure Marketplace is een **afbeeldings galerie** een opslag plaats voor het beheren en delen van installatie kopieën, maar u bepaalt wie toegang heeft. |
| **Definitie van installatie kopie** | Afbeeldings definities worden in een galerie gemaakt en bevatten informatie over de installatie kopie en vereisten voor het intern gebruik. Dit betekent of de installatie kopie Windows of Linux, release opmerkingen en minimale en maximale geheugen vereisten is. Het is een definitie van een type installatie kopie. |
| **Versie van installatiekopie** | Een **installatie kopie versie** is wat u gebruikt om een virtuele machine te maken wanneer u een galerie gebruikt. U kunt meerdere versies van een installatie kopie naar behoefte hebben voor uw omgeving. Net als bij een beheerde installatie kopie wordt de versie van de installatie kopie gebruikt voor het maken van nieuwe schijven voor de virtuele machine wanneer u een **installatie kopie** gebruikt voor het maken van een virtuele machine. Installatie kopie versies kunnen meerdere keren worden gebruikt. |