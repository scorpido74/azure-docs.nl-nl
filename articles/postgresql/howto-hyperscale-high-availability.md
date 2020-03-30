---
title: Hoge beschikbaarheid configureren - Hyperscale (Citus) - Azure Database voor PostgreSQL
description: Hoge beschikbaarheid in- of uitschakelen
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a8d4b5949b34d16191e9ec10a1dd39faff3660dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977648"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Hyperscale (Citus) hoge beschikbaarheid configureren

Azure Database voor PostgreSQL - Hyperscale (Citus) biedt hoge beschikbaarheid (HA) om downtime van de database te voorkomen. Met HA ingeschakeld, krijgt elk knooppunt in een servergroep een stand-by. Als het oorspronkelijke knooppunt ongezond wordt, wordt de stand-by gepromoot om het te vervangen.

> [!IMPORTANT]
> Omdat HA het aantal servers in de groep verdubbelt, zal het ook de kosten verdubbelen.

Het inschakelen van HA is mogelijk tijdens het maken van servergroepen of daarna op het tabblad **Configureren** voor uw servergroep in de Azure-portal. De gebruikersinterface ziet er in beide gevallen hetzelfde uit. Sleep de schuifregelaar voor **hoge beschikbaarheid** naar JA:

![ha schuifregelaar](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Klik **op de** knop Opslaan om uw selectie toe te passen. Het inschakelen van HA kan enige tijd duren als de servergroep stand-bys standbys en stromen gegevens naar hen.

Op het tabblad **Overzicht** voor de servergroep worden alle knooppunten en stand-bys weergegeven, samen met een kolom **met hoge beschikbaarheid** die aangeeft of HA voor elk knooppunt is ingeschakeld.

![de ha-kolom in het overzicht van de servergroep](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Volgende stappen

Meer informatie over [hoge beschikbaarheid](concepts-hyperscale-high-availability.md).
