---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5149973fe63f867b49e55c970779c005e12536b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "68780206"
---
1. Open de pagina voor de gateway van uw virtuele netwerk. Er zijn meerdere manieren om ernaar te navigeren. U kunt naar de gateway gaan door te gaan naar de **naam van uw VNet-> overzicht: > verbonden apparaten: > naam van uw gateway**.
2. Op de pagina voor de gateway klikt u op **verbindingen**. Klik bovenaan de pagina Verbindingen op **+ Toevoegen** om de pagina **Verbinding toevoegen** te openen.

   ![Site-naar-Site-verbinding maken](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. Op de pagina **Verbinding toevoegen** configureert u de waarden die nodig zijn om verbinding te maken.

   - **Naam:** de naam van de verbinding.
   - **Verbindingstype:** selecteer **Site-naar-site (IPsec)**.
   - **Virtuele netwerkgateway:** hiervoor geldt een vaste waarde, omdat u verbinding maakt vanaf deze gateway.
   - **Lokale netwerkgateway:** klik op **Een lokale netwerkgateway kiezen** en selecteer de lokale netwerkgateway die u wilt gebruiken.
   - **Gedeelde sleutel:** de waarde hier moet overeenkomen met de waarde die u voor uw lokale on-premises VPN-apparaat gebruikt. In het voorbeeld wordt 'abc123' gebruikt, maar u kunt een ingewikkeldere waarde gebruiken (aanbevolen). Het is van belang dat de waarde die u hier opgeeft, dezelfde waarde is die u hebt opgegeven bij het configureren van het VPN-apparaat.
   - De resterende waarden voor **Abonnement**, **Resourcegroep**, en **Locatie** zijn vast.

4. Klik op **OK** om uw verbinding te maken. U ziet *Verbinding maken* op het scherm knipperen.
5. U kunt de verbinding bekijken op de pagina **Verbindingen** van de virtuele netwerkgateway. De status verandert van *Onbekend* in *Verbinding maken* en vervolgens in *Voltooid*.
