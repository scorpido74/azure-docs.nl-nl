---
title: Ondersteuning voor het gebruik van Azure Site Recovery met Azure Backup
description: Biedt een overzicht van hoe Azure Site Recovery en Azure Backup samen kunnen worden gebruikt.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72376220"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Ondersteuning voor het gebruik van siteherstel met Azure Backup

In dit artikel wordt een overzicht van de ondersteuning voor het gebruik van de [siteherstelservice](site-recovery-overview.md) samen met de [Azure Backup-service.](https://docs.microsoft.com/azure/backup/backup-overview)

**Actie** | **Ondersteuning voor Site Recovery** | **Details**
--- | --- | ---
**Services samen implementeren** | Ondersteund | Diensten zijn interoperabel en kunnen samen worden geconfigureerd.
**Bestandsback-up/herstel** | Ondersteund | Wanneer back-up en replicatie zijn ingeschakeld voor een vm en back-ups worden gemaakt, is er geen probleem bij het herstellen van bestanden op de VM's aan de bronzijde of de groep VM's. Replicatie gaat gewoon door zonder wijziging in de replicatiestatus.
**Schijfherstel** | Geen huidige ondersteuning | Als u een back-upschijf herstelt, moet u de replicatie voor de virtuele machine opnieuw uitschakelen en opnieuw inschakelen.
**VM herstellen** | Geen huidige ondersteuning | Als u een VM of groep VM's herstelt, moet u replicatie voor de vm uitschakelen en opnieuw inschakelen.  


