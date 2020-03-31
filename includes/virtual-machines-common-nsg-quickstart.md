---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176005"
---
U opent een poort of maakt een eindpunt voor een virtuele machine (VM) in Azure door een netwerkfilter te maken op een subnet of een VM-netwerkinterface. U plaatst deze filters, die zowel binnenkomend als uitgaand verkeer beheren, op een netwerkbeveiligingsgroep die is gekoppeld aan de bron die het verkeer ontvangt.

In het voorbeeld in dit artikel wordt uitgelegd hoe u een netwerkfilter maakt dat gebruikmaakt van de standaard TCP-poort 80 (er wordt ervan uitgegaan dat u de juiste services al hebt gestart en dat u alle firewallregels van het besturingssysteem op de VM hebt geopend).

Nadat u een VM hebt gemaakt die is geconfigureerd om webaanvragen te serveren op de standaard TCP-poort 80, u het als volgt instellen:

1. Maak een netwerkbeveiligingsgroep.

2. Maak een binnenkomende beveiligingsregel waarmee verkeer wordt toegestaan en wijs waarden toe aan de volgende instellingen:

   - **Bestemmingspoortbereik**: 80

   - **Bronpoortbereiken**: * (maakt elke bronpoort mogelijk)

   - **Prioriteitswaarde:** voer een waarde in die minder dan 65.500 en hoger in prioriteit is dan de standaard catch-all inkomende regel weigeren.

3. Koppel de netwerkbeveiligingsgroep aan de VM-netwerkinterface of subnet.

Hoewel in dit voorbeeld een eenvoudige regel wordt gebruikt om HTTP-verkeer toe te staan, u ook netwerkbeveiligingsgroepen en -regels gebruiken om complexere netwerkconfiguraties te maken. 




