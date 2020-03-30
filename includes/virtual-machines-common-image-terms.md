---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 98d765e2f6909f00f8dfe76d06aef017aad67adf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71174945"
---
## <a name="terminology"></a>Terminologie

Een Marketplace-afbeelding in Azure heeft de volgende kenmerken:

* **Uitgever:** de organisatie die de afbeelding heeft gemaakt. Voorbeelden: Canonical, MicrosoftWindowsServer
* **Aanbieding:** de naam van een groep gerelateerde afbeeldingen die door een uitgever zijn gemaakt. Voorbeelden: UbuntuServer, WindowsServer
* **SKU**: Een voorbeeld van een aanbieding, zoals een belangrijke release van een distributie. Voorbeelden: 18.04-LTS, 2019-Datacenter
* **Versie:** het versienummer van een afbeelding SKU. 

Als u een Marketplace-afbeelding wilt identificeren wanneer u een VM programmatisch implementeert, geeft u deze waarden afzonderlijk als parameters. Sommige gereedschappen accepteren een afbeelding *URN*, die deze waarden combineert, gescheiden door de dikke darm (:) karakter: *Uitgever*:*Aanbieding*:*Sku*:*Versie*. In een URN u het versienummer vervangen door laatste, waarmee de nieuwste versie van de afbeelding wordt geselecteerd. 

Als de afbeeldingsuitgever aanvullende licentie- en aankoopvoorwaarden biedt, moet u deze voorwaarden accepteren en programmatische implementatie inschakelen. U moet ook *inkoopplanparameters* leveren wanneer u een VM programmatisch implementeert. Zie [Een afbeelding implementeren met Marketplace-termen](#deploy-an-image-with-marketplace-terms).
