---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d36d2be59010c47348a8e196b28d87e5b967868e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175906"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Stap 1: Navigeer naar de gateway van het virtuele netwerk

1. Ga in het [Azure Portal](https://portal.azure.com)naar **alle resources**. 
2. Als u de pagina virtuele netwerk gateway wilt openen, gaat u naar de virtuele netwerk gateway die u wilt verwijderen en klikt u erop.

### <a name="step-2-delete-connections"></a>Stap 2: verbindingen verwijderen

1. Klik op de pagina voor uw virtuele netwerk gateway op **verbindingen** om alle verbindingen met de gateway weer te geven.
2. Klik op **de rij** met de naam van de verbinding en selecteer vervolgens **verwijderen** in de vervolg keuzelijst.
3. Klik op **Ja** om te bevestigen dat u de verbinding wilt verwijderen. Als u meerdere verbindingen hebt, verwijdert u elke verbinding.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Stap 3: de gateway van het virtuele netwerk verwijderen

Houd er rekening mee dat als u naast uw S2S-configuratie een P2S-configuratie hebt voor dit VNet en u de gateway van het virtuele netwerk verwijdert, wordt de verbinding van alle P2S-clients automatisch verbroken zonder dat er een waarschuwing wordt weer gegeven.

1. Klik op de pagina virtuele netwerk gateway op **overzicht**.
2. Klik op de pagina **overzicht** op **verwijderen** om de gateway te verwijderen.
