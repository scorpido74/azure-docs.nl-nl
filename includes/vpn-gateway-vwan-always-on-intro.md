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
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117021"
---
Een nieuwe functie van de Windows 10 VPN-client, Always On, is de mogelijkheid om een VPN-verbinding te onderhouden. Met Always On kan het actieve VPN-profiel automatisch verbinding maken en verbonden blijven op basis van triggers, zoals aanmelding door de gebruiker, wijziging van de netwerkstatus of apparaatscherm actief.

U gateways met Windows 10 Always On gebruiken om permanente gebruikerstunnels en apparaattunnels naar Azure vast te stellen. In dit artikel u een Always On VPN-gebruikerstunnel configureren.

Always On VPN-verbindingen omvatten een van de twee soorten tunnels:

* **Apparaattunnel**: maakt verbinding met opgegeven VPN-servers voordat gebruikers zich bij het apparaat aanmelden. Vooraf aanmelden connectiviteitsscenario's en apparaatbeheer maken gebruik van een apparaattunnel.

* **Gebruikerstunnel**: maakt alleen verbinding nadat gebruikers zich bij het apparaat hebben aangemeld. Door gebruik te maken van gebruikerstunnels hebt u toegang tot organisatiebronnen via VPN-servers.

Apparaattunnels en gebruikerstunnels werken onafhankelijk van hun VPN-profielen. Ze kunnen tegelijkertijd worden verbonden en ze kunnen verschillende verificatiemethoden en andere VPN-configuratie-instellingen gebruiken, indien van toepassing.
