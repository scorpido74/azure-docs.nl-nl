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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175906"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Stap 1: Navigeren naar de virtuele netwerkgateway

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar **Alle bronnen**. 
2. Als u de pagina met de virtuele netwerkgateway wilt openen, navigeert u naar de virtuele netwerkgateway die u wilt verwijderen en klikt u erop.

### <a name="step-2-delete-connections"></a>Stap 2: Verbindingen verwijderen

1. Klik op de pagina voor uw virtuele netwerkgateway op **Verbindingen** om alle verbindingen met de gateway weer te geven.
2. Klik op de **'...'** in de rij van de naam van de verbinding en selecteer **Verwijderen** in de vervolgkeuzelijst.
3. Klik **op Ja** om te bevestigen dat u de verbinding wilt verwijderen. Als u meerdere verbindingen hebt, verwijdert u elke verbinding.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Stap 3: De virtuele netwerkgateway verwijderen

Houd er rekening mee dat als u naast uw S2S-configuratie een P2S-configuratie op deze VNet hebt, het verwijderen van de virtuele netwerkgateway automatisch alle P2S-clients zonder waarschuwing loskoppelt.

1. Klik op de pagina virtuele netwerkgateway op **Overzicht**.
2. Klik op de pagina **Overzicht** op **Verwijderen** om de gateway te verwijderen.
