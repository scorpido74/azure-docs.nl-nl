---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244640"
---
| Entiteit                                       | Conventies   |
|----------------------------------------------|-----------------------------------------------------|
| Containernamen voor blokblob en paginablob | Moet een geldige DNS-naam zijn die 3 tot 63 tekens lang is. <br>  Moet beginnen met een letter of cijfer. <br> Kan alleen kleine letters, cijfers en het koppelteken (-) bevatten. <br> Elk koppelteken (-) moet direct worden voorafgegaan en gevolgd door een letter of cijfer. <br> Opeenvolgende koppeltekens zijn niet toegestaan in namen. |
| Namen voor Azure-bestanden delen                  | Hetzelfde als hierboven                                          |
| Directory- en bestandsnamen voor Azure-bestanden     |<li> Case-preserving, case-ongevoelig en mag niet meer dan 255 tekens in lengte. </li><li> Kan niet eindigen met de voorwaartse slash (/). </li><li>Indien aanwezig, wordt het automatisch verwijderd. </li><li> Volgende tekens zijn niet toegestaan:<code>" \\ / : \| < > * ?</code></li><li> Gereserveerde tekens voor URL's moeten op de juiste wijze van een escape-teken zijn voorzien. </li><li> Illegale URL-padtekens zijn niet toegestaan. Codepunten \\zoals uE000 zijn geen geldige Unicode-tekens. Sommige ASCII- of Unicode-tekens, zoals controletekens (0x00 tot 0x1F, \\u0081, enz.), zijn ook niet toegestaan. Zie RFC 2616, sectie 2.2: Basisregels en RFC 3987 voor regels voor Unicode-tekenreeksen in HTTP/1.1. </li><li> De volgende bestandsnamen zijn niet toegestaan: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, dot character (.) en twee dot tekens (..).</li>|
| Blobnamen voor blok-blob en pagina-blob      | </li><li>Blobnamen zijn hoofdlettergevoelig en kunnen elke combinatie van tekens bevatten. </li><li>Een blobnaam moet 1 tot 1024 tekens bevatten. </li><li>Gereserveerde tekens voor URL's moeten op de juiste wijze van een escape-teken zijn voorzien. </li><li>Het aantal padsegmenten dat de blobnaam omvat, mag niet meer dan 254 zijn. Een padsegment is de tekenreeks tussen opeenvolgende scheidingstekens (bijvoorbeeld de slash '/') die overeenkomt met de naam van een virtuele map.</li> |
