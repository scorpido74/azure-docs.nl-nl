---
title: De Azure Backup Agent upgraden
description: In deze informatie wordt uitgelegd waarom u de Azure Backup Agent moet upgraden en waar u de upgrade downloaden.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673190"
---
## <a name="upgrade-the-mars-agent"></a>Upgrade de MARS-agent

Versies van de Microsoft Azure Recovery Services (MARS)-agent onder 2.0.9083.0 zijn afhankelijk van de Azure Access Control-service. De MARS-agent wordt ook wel de Azure Backup Agent genoemd.

In 2018 heeft Microsoft [de Azure Access Control-service afgeschaft.](../articles/active-directory/azuread-dev/active-directory-acs-migration.md) Vanaf 19 maart 2018 zullen alle versies van de MARS-agent onder 2.0.9083.0 back-upfouten ondervinden. Upgrade [uw MARS-agent naar de nieuwste versie om](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent)back-upfouten te voorkomen of op te lossen. Als u servers wilt identificeren waarvoor een UPGRADE van MARS-agent vereist is, voert u de stappen uit [in De MARS-agent (Microsoft Azure Recovery Services) upgraden.](../articles/backup/upgrade-mars-agent.md)

De MARS-agent wordt gebruikt om een back-up te maken van bestanden en mappen en systeemstatusgegevens naar Azure. System Center DPM en Azure Backup Server gebruiken de MARS-agent om back-ups te maken van gegevens naar Azure.
