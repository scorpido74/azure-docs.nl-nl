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
ms.openlocfilehash: 3bd9489adaf46e604393fc7059d37443bdd5ec3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73488940"
---
1. Zoek de virtuele WAN die u hebt gemaakt. Selecteer **hubs**op de pagina Virtueel WAN onder de sectie **Connectiviteit** .
2. Selecteer op de pagina Hubs **+Nieuwe hub** om de **pagina Virtuele hub maken** te openen.

    ![Basics](./media/virtual-wan-tutorial-hub-include/basics.png "Basisbeginselen")
3. Vul op het tabblad **Basisbeginselen** **van de virtuele hub** maken de volgende velden in:

    **Projectdetails**

   * Regio (voorheen locatie genoemd)
   * Name
   * Hub privéadresruimte. De minimale adresruimte is /24 om een hub te maken, wat impliceert dat alles varieert van /25 tot /32 een fout zal veroorzaken tijdens het maken.
4. Selecteer **Volgende: site-to-site**.

    ![Site-to-site](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Site-naar-site")

5. Voer op het tabblad **Site-to-site** de volgende velden in:

   * Selecteer **Ja** om een VPN van site-naar-site te maken.
   * Het veld AS-getal is op dit moment niet bewerkbaar in de virtuele hub.
   * Selecteer de waarde van de **gatewayschaal in** de vervolgkeuzelijst. Met de schaaleenheid u de totale doorvoer kiezen van de VPN-gateway die wordt gemaakt in de virtuele hub om sites aan te sluiten. Als u 1 schaaleenheid = 500 Mbps kiest, betekent dit dat er twee exemplaren voor redundantie worden gemaakt, elk met een maximale doorvoer snelheid van 500 Mbps. Als u bijvoorbeeld vijf branches had, die elk 10 Mbps bij de branch doen, hebt u een totaal van 50 Mbps aan het hoofdeinde nodig. Planning voor de totale capaciteit van de Azure VPN-gateway moet worden uitgevoerd na het beoordelen van de capaciteit die nodig is om het aantal branches naar de hub te ondersteunen.
6. Selecteer **Controleren + Maken** om te valideren.
7. Selecteer **Maken** om de hub te maken. Na 30 minuten **kunt u vernieuwen** om de hub op de **hubspagina** weer te geven. Selecteer **Ga naar resource om** naar de resource te navigeren.