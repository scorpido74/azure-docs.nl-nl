---
title: De Azure Backup-Agent bijwerken
description: In deze informatie wordt uitgelegd waarom u de Azure Backup-agent moet upgraden en waar u de upgrade kunt downloaden.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: 227ec87081817b273b66684e31a0ebd0e11134a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197114"
---
## <a name="upgrade-the-mars-agent"></a>De MARS-agent upgraden

De versies van de Microsoft Azure Recovery Services-agent (MARS) onder 2.0.9083.0 hebben een afhankelijkheid van de Azure Access Control-service. De MARS-agent wordt ook wel de Azure Backup-Agent genoemd.

In 2018 heeft micro soft [de Azure Access Control-service afgeschaft](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Vanaf 19 maart 2018 zullen alle versies van de MARS-agent onder 2.0.9083.0 back-upfouten ondervinden. Om back-upfouten te voor komen of op te lossen, [moet u de Mars-agent upgraden naar de nieuwste versie](https://go.microsoft.com/fwlink/?linkid=229525). Volg de stappen in de [back-upblog voor het upgraden van Mars-](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/)agents om servers te identificeren waarvoor een Mars-upgrade is vereist.

De MARS-agent wordt gebruikt voor het maken van een back-up van bestanden en mappen en de systeem status gegevens naar Azure. System Center DPM en Azure Backup Server gebruiken de MARS-agent om een back-up te maken van gegevens in Azure.
