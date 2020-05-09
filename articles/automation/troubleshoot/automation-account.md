---
title: Problemen met Azure Automation-account oplossen
description: Meer informatie over het oplossen van problemen met een Azure-account.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 6a7defdaa9b4b0f0b3580a3ac6b1a0487434a97c
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864127"
---
# <a name="troubleshoot-an-azure-automation-account"></a>Problemen met een Azure Automation-account oplossen

In dit artikel worden oplossingen beschreven voor problemen die kunnen optreden wanneer u een Azure Automation-account gebruikt. Zie [een Azure-account maken](../automation-quickstart-create-account.md)voor algemene informatie over Automation-accounts.

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: kan Automation-resource provider niet registreren voor abonnementen

### <a name="issue"></a>Probleem

Wanneer u met beheer oplossingen in uw Automation-account werkt, treedt de volgende fout op:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Oorzaak

De resource provider voor Automation is niet geregistreerd in het abonnement.

### <a name="resolution"></a>Oplossing

Voer de volgende stappen uit in de Azure Portal om de resource provider Automation te registreren:

1. Ga in uw browser naar de [Azure Portal](https://portal.azure.com).

2. Ga naar **abonnementen**en selecteer uw abonnement.   

3. Onder **instellingen**selecteert u **resource providers**.

4. Controleer in de lijst met resource providers of de resource provider **micro soft. Automation** is geregistreerd.

5. Als de provider niet wordt weer gegeven, registreert u deze zoals beschreven in [fouten voor de registratie van de resource provider oplossen](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet kunt oplossen met dit artikel, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Verbinding maken [@AzureSupport](https://twitter.com/azuresupport)met. Dit is het officiële Microsoft Azure account voor het verbinden van de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**.