---
title: Problemen met Automation-accounts oplossen
description: Meer informatie over het oplossen en oplossen van problemen met een Azure-account.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679384"
---
# <a name="troubleshoot-the-automation-account"></a>Problemen met het automatiseringsaccount oplossen

In dit artikel worden oplossingen besproken voor problemen die u tegenkomen wanneer u een Automatiseringsaccount gebruikt. In de volgende secties worden specifieke foutberichten en mogelijke oplossingen voor elk van deze secties belicht. Zie [Een Azure-account maken](../automation-quickstart-create-account.md)voor algemene informatie over automatiseringsaccounts .

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: Kan automation resourceprovider niet registreren voor abonnementen

### <a name="issue"></a>Probleem

Wanneer u werkt met beheeroplossingen in uw Automatiseringsaccount, komt u de volgende fout tegen:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Oorzaak

De Automation Resource Provider is niet geregistreerd in het abonnement.

### <a name="resolution"></a>Oplossing

Voer de volgende stappen uit in de Azure-portal als u de Automation Resource Provider wilt registreren:

1. Ga vanuit uw browser naar de [Azure-portal.](https://portal.azure.com)

2. Navigeer naar **abonnementen** en selecteer uw abonnement op de pagina Abonnementen.   

3. Selecteer **Resourceproviders**onder **Instellingen**.

4. Controleer in de lijst met resourceproviders of de **microsoft.automation-resourceprovider** is geregistreerd.

5. Als de provider niet wordt vermeld, registreert u deze zoals beschreven in [Fouten oplossen voor de registratie van resourcesprovider.](/azure/azure-resource-manager/resource-manager-register-provider-errors)

## <a name="next-steps"></a>Volgende stappen

Als je het probleem hierboven niet ziet of het probleem niet oplossen, probeer je een van de volgende kanalen voor extra ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport)verbinding met het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.