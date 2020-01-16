---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 893beb0800af0eece4d69e727e427c3e92b79121
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044954"
---
Op rollen gebaseerd toegangs beheer is een overname strategie voor het beheren van de toegang, machtigingen en rollen. Onderliggende rollen nemen machtigingen over van bovenliggende rollen. Machtigingen kunnen ook worden toegewezen zonder dat ze worden overgenomen van een bovenliggende rol. Ze kunnen ook worden toegewezen om een rol zo nodig aan te passen.

Een ruimte beheerder kan bijvoorbeeld globale toegang nodig hebben om alle bewerkingen voor een opgegeven ruimte uit te voeren. De toegang omvat alle knoop punten onder of in de ruimte. Het installatie programma van een apparaat heeft mogelijk alleen *Lees* -en *Update* -machtigingen nodig voor apparaten en Sens oren.

In elk geval worden rollen precies toegekend *en niet meer dan de toegang* die is vereist om aan hun taken te voldoen volgens het principe van minimale bevoegdheden. Volgens dit principe wordt een identiteit *alleen*verleend:

* De hoeveelheid toegang die nodig is om de taak te volt ooien.
* Een rol die geschikt en beperkt is om de taak uit te voeren.

>[!IMPORTANT]
> Volg altijd het principe van minimale bevoegdheden.

Twee andere belang rijke op rollen gebaseerde toegangs beheer procedures:

> [!div class="checklist"]
> * Controleer regel matig de roltoewijzingen om te controleren of elke rol over de juiste machtigingen beschikt.
> * Rollen en toewijzingen opschonen wanneer individuen rollen of toewijzingen wijzigen.