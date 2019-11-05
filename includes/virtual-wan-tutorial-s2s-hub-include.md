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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488940"
---
1. Zoek het virtuele WAN dat u hebt gemaakt. Selecteer **hubs**op de virtuele WAN-pagina onder het gedeelte **connectiviteit** .
2. Selecteer op de pagina hubs **+ nieuwe hub** om de pagina **virtuele hub maken** te openen.

    ![Basisinstellingen](./media/virtual-wan-tutorial-hub-include/basics.png "Basisbeginselen")
3. Vul de volgende velden in op het tabblad **basis principes** van **virtuele-hub maken** :

    **Project Details**

   * Regio (voorheen locatie genoemd)
   * Naam
   * Privé-adres ruimte van hub. De minimale adres ruimte is/24 voor het maken van een hub, wat impliceert dat het bereik tussen/25 en/32 een fout produceert tijdens het maken.
4. Selecteer **volgende: site-naar-site**.

    ![Site-naar-site](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Site-naar-site")

5. Op het tabblad **site-naar-site** vult u de volgende velden in:

   * Selecteer **Ja** om een site-naar-site-VPN te maken.
   * Het veld AS-nummer kan op dit moment niet worden bewerkt in de virtuele hub.
   * Selecteer de waarde voor de **Gateway schaal eenheden** in de vervolg keuzelijst. Met de schaal eenheid kunt u de geaggregeerde door Voer van de VPN-gateway die in de virtuele hub wordt gemaakt, selecteren om de sites te verbinden. Als u 1 schaal eenheid kiest = 500 Mbps, betekent dit dat er twee exemplaren voor redundantie worden gemaakt, elk met een maximale door Voer van 500 Mbps. Als u bijvoorbeeld vijf vertakkingen hebt, die elk 10 Mbps bij het filiaal doen, hebt u een aggregatie van 50 Mbps aan het einde van het hoofd. Het plannen van de totale capaciteit van de Azure VPN-gateway moet worden uitgevoerd na het beoordelen van de capaciteit die nodig is om het aantal branches naar de hub te ondersteunen.
6. Selecteer **controleren + maken** om te valideren.
7. Selecteer **maken** om de hub te maken. **Vernieuw** na 30 minuten om de hub weer te geven op de pagina **hubs** . Selecteer **naar resource gaan** om naar de resource te navigeren.