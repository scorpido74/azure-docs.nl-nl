---
title: Ondersteuning voor het gebruik van Azure Site Recovery met Azure Backup
description: Hierin wordt een overzicht gegeven van de manier waarop Azure Site Recovery en Azure Backup samen kunnen worden gebruikt.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "72376220"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Ondersteuning voor het gebruik van Site Recovery met Azure Backup

In dit artikel wordt een overzicht gegeven van de ondersteuning voor het gebruik van de [site Recovery-service](site-recovery-overview.md) samen met de [Azure backup-service](https://docs.microsoft.com/azure/backup/backup-overview).

**Actie** | **Ondersteuning voor Site Recovery** | **Nadere**
--- | --- | ---
**Services samen implementeren** | Ondersteund | Services zijn interoperabel en kunnen samen worden geconfigureerd.
**Back-up/herstellen van bestanden** | Ondersteund | Als back-up en replicatie zijn ingeschakeld voor een virtuele machine en er back-ups worden gemaakt, is er geen probleem bij het herstellen van bestanden op de bron-en-groep Vm's. Replicatie wordt voortgezet zoals gebruikelijk zonder wijzigingen in de replicatie status.
**Schijf herstellen** | Geen huidige ondersteuning | Als u een back-up van de schijf herstelt, moet u de replicatie voor de VM opnieuw uitschakelen en opnieuw inschakelen.
**VM herstellen** | Geen huidige ondersteuning | Als u een virtuele machine of een groep virtuele machines herstelt, moet u de replicatie voor de virtuele machine uitschakelen en opnieuw inschakelen.  


