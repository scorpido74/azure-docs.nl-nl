---
title: Ondersteuning voor het gebruik van Azure Site Recovery met Azure Backup
description: Hierin wordt een overzicht gegeven van de manier waarop Azure Site Recovery en Azure Backup samen kunnen worden gebruikt.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: 2078aa33ca735b91b5923d94028a286427f107a6
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146861"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Ondersteuning voor het gebruik van Site Recovery met Azure Backup

In dit artikel wordt een overzicht gegeven van de ondersteuning voor het gebruik van de [site Recovery-service](site-recovery-overview.md) samen met de [Azure backup-service](https://docs.microsoft.com/azure/backup/backup-overview).

**Actie** | **Ondersteuning voor Site Recovery** | **Details**
--- | --- | ---
**Services samen implementeren** | Ondersteund | Services zijn interoperabel en kunnen samen worden geconfigureerd.
**Back-up/herstellen van bestanden** | Ondersteund | Als back-up en replicatie zijn ingeschakeld voor een virtuele machine en er back-ups worden gemaakt, is er geen probleem bij het herstellen van bestanden op de bron-en-groep Vm's. Replicatie wordt voortgezet zoals gebruikelijk zonder wijzigingen in de replicatie status.
**Schijf back-ups maken/herstellen** | Geen huidige ondersteuning | Als u een back-up van de schijf herstelt, moet u de replicatie voor de virtuele machine opnieuw uitschakelen en opnieuw inschakelen.
**Back-up/herstel van VM** | Geen huidige ondersteuning | Als u een back-up van een virtuele machine of een groep virtuele machines maakt of herstelt, moet u de replicatie voor de virtuele machine uitschakelen en opnieuw inschakelen.  


