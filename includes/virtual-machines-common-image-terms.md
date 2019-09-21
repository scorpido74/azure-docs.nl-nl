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
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174945"
---
## <a name="terminology"></a>Terminologie

Een Marketplace-installatie kopie in azure heeft de volgende kenmerken:

* **Uitgever**: De organisatie waarvoor de installatiekopie is gemaakt. Voorbeelden: Canonical, MicrosoftWindowsServer
* **Aanbieding**: De naam van een groep gerelateerde installatie kopieën die door een uitgever zijn gemaakt. Voorbeelden: UbuntuServer, WindowsServer
* **SKU**: Een exemplaar van een aanbieding, zoals een grote release van een distributie. Voorbeelden: 18,04-LTS, 2019-Data Center
* **Version**: Het versie nummer van een afbeeldings-SKU. 

Als u een Marketplace-installatie kopie wilt identificeren wanneer u een virtuele machine programmatisch implementeert, dient u deze waarden afzonderlijk op te geven als para meters. Sommige hulpprogram ma's accepteren een afbeelding *urn*, dat deze waarden combineert, gescheiden door de dubbele punt (:) ring *Uitgever*:*aanbieding*:*SKU*:*versie*. In een URN kunt u het versie nummer vervangen door ' laatste ', waarmee de meest recente versie van de installatie kopie wordt geselecteerd. 

Als de uitgever van de installatie kopie aanvullende licentie-en aankoop voorwaarden biedt, moet u deze voor waarden accepteren en programmatische implementatie inschakelen. U moet ook de para meters van het *aankoop plan* opgeven wanneer u een virtuele machine programmatisch implementeert. Zie [een installatie kopie implementeren met Marketplace-voor waarden](#deploy-an-image-with-marketplace-terms).
