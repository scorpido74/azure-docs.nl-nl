---
title: Problemen met Automation-accounts oplossen
description: Meer informatie over het oplossen van problemen met een Azure-account.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679384"
---
# <a name="troubleshoot-the-automation-account"></a>Problemen met het Automation-account oplossen

In dit artikel worden oplossingen beschreven voor problemen die kunnen optreden wanneer u een Automation-account gebruikt. In de volgende secties worden specifieke fout berichten en mogelijke oplossingen voor elk weer gemarkeerd. Zie [een Azure-account maken](../automation-quickstart-create-account.md)voor algemene informatie over Automation-accounts.

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

2. Navigeer naar **abonnementen** en selecteer uw abonnement op de pagina Abonnementen.   

3. Onder **instellingen**selecteert u **resource providers**.

4. Controleer in de lijst met resource providers of de resource provider **micro soft. Automation** is geregistreerd.

5. Als de provider niet wordt weer gegeven, registreert u deze zoals beschreven in [fouten voor de registratie van de resource provider oplossen](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Volgende stappen

Als uw probleem niet hierboven wordt weer geven of als u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport)met, het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.