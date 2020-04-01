---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73491606"
---
1. Zoek de virtuele WAN die u hebt gemaakt. Selecteer **hubs**op de pagina Virtueel WAN onder de sectie **Connectiviteit** .
2. Selecteer op de pagina Hubs **+Nieuwe hub** om de **pagina Virtuele hub maken** te openen.
3. Vul op het tabblad **Basisbeginselen** **van de virtuele hub** maken de volgende velden in:

   ![Basisbeginselen](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Basisbeginselen")

    **Projectdetails**

   * Regio (voorheen locatie genoemd)
   * Name
   * Hub privéadresruimte. De minimale adresruimte is /24 om een hub te maken, wat impliceert dat alles varieert van /25 tot /32 een fout zal veroorzaken tijdens het maken.
4. Selecteer het **tabblad ExpressRoute**.

5. Voer op het tabblad **ExpressRoute** de volgende velden in:

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Selecteer **Ja** om een **ExpressRoute-gateway** te maken.
   * Selecteer de waarde van de **gatewayschaal in** de vervolgkeuzelijst.
6. Selecteer **Controleren + Maken** om te valideren.
7. Selecteer **Maken** om de hub te maken. Na 30 minuten **kunt u vernieuwen** om de hub op de **hubspagina** weer te geven. Selecteer **Ga naar resource om** naar de resource te navigeren.