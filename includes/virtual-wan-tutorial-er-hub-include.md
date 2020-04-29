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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73491606"
---
1. Zoek het virtuele WAN dat u hebt gemaakt. Selecteer **hubs**op de virtuele WAN-pagina onder het gedeelte **connectiviteit** .
2. Selecteer op de pagina hubs **+ nieuwe hub** om de pagina **virtuele hub maken** te openen.
3. Vul de volgende velden in op het tabblad **basis principes** van **virtuele-hub maken** :

   ![Basisbeginselen](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Basisbeginselen")

    **Project Details**

   * Regio (voorheen locatie genoemd)
   * Naam
   * Privé-adres ruimte van hub. De minimale adres ruimte is/24 voor het maken van een hub, wat impliceert dat het bereik tussen/25 en/32 een fout produceert tijdens het maken.
4. Selecteer het **tabblad ExpressRoute**.

5. Vul op het tabblad **ExpressRoute** de volgende velden in:

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Selecteer **Ja** om een **ExpressRoute** -gateway te maken.
   * Selecteer de waarde voor de **Gateway schaal eenheden** in de vervolg keuzelijst.
6. Selecteer **controleren + maken** om te valideren.
7. Selecteer **maken** om de hub te maken. **Vernieuw** na 30 minuten om de hub weer te geven op de pagina **hubs** . Selecteer **naar resource gaan** om naar de resource te navigeren.