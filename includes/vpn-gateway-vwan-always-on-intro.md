---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2783c828f96eeb3539e2266eaf1d6d590a6af4c4
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370964"
---
Een nieuwe functie van de Windows 10 VPN-client, altijd aan, is de mogelijkheid om een VPN-verbinding te onderhouden. Met Always on kan het actieve VPN-profiel automatisch verbinding maken en blijven verbonden op basis van triggers, zoals het aanmelden van gebruikers, het wijzigen van de netwerk status of het scherm apparaat actief.

U kunt virtuele Azure-netwerk gateways met Windows 10 altijd on gebruiken om permanente gebruikers tunnels en tunnels voor apparaten te maken met Azure. Dit artikel helpt u bij het configureren van een always on-VPN-gebruikers tunnel.

Altijd op VPN-verbindingen zijn twee typen tunnels:

* **Tunnel van apparaat**: Hiermee maakt u verbinding met opgegeven VPN-servers voordat gebruikers zich aanmelden bij het apparaat. Bij connectiviteits scenario's voor aanmelding en Apparaatbeheer wordt een tunnel gebruikt.

* **Gebruikers tunnel**: maakt alleen verbinding nadat gebruikers zich aanmelden op het apparaat. Door gebruikers tunnels te gebruiken, hebt u toegang tot bedrijfs bronnen via VPN-servers.

Tunnels en gebruikers tunnels van apparaten worden onafhankelijk van hun VPN-profielen gebruikt. Ze kunnen tegelijkertijd worden verbonden en ze kunnen, indien van toepassing, verschillende verificatie methoden en andere VPN-configuratie-instellingen gebruiken.
