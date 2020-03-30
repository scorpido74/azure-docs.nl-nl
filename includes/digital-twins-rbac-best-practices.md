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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044954"
---
Op rollen gebaseerd toegangsbeheer is een op overervingen gebaseerde beveiligingsstrategie voor het beheren van toegang, machtigingen en rollen. Afstammelingenrollen erven machtigingen van bovenliggende rollen. Machtigingen kunnen ook worden toegewezen zonder te worden overgenomen van een bovenliggende rol. Ze kunnen ook worden toegewezen om een rol aan te passen als dat nodig is.

Een ruimtebeheerder heeft bijvoorbeeld algemene toegang nodig om alle bewerkingen voor een bepaalde ruimte uit te voeren. Toegang omvat alle knooppunten onder of binnen de ruimte. Een apparaatinstallatieprogramma hoeft mogelijk alleen *lees-* en *updatemachtigingen* voor apparaten en sensoren.

In elk geval worden rollen precies verleend *en niet meer dan de toegang die nodig is* om hun taken te vervullen volgens het beginsel van het minste voorrecht. Volgens dit beginsel wordt een identiteit *slechts*toegekend :

* De hoeveelheid toegang die nodig is om zijn taak te voltooien.
* Een passende rol en beperkt tot de uitvoering van haar taak.

>[!IMPORTANT]
> Volg altijd het principe van het minste voorrecht.

Twee andere belangrijke op rollen gebaseerde toegangscontrolepraktijken die moeten worden gevolgd:

> [!div class="checklist"]
> * Controleer periodiek roltoewijzingen om te controleren of elke rol over de juiste machtigingen beschikt.
> * Taken en opdrachten opschonen wanneer personen van rol of toewijzing veranderen.