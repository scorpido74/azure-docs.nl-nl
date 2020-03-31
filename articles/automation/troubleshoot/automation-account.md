---
title: Problemen met het automatiseringsaccount oplossen
description: Meer informatie over het oplossen en oplossen van problemen met een Azure-account.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c66b1728144b8517f6ac444059b3a8def956c6e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301003"
---
# <a name="automation-account-troubleshooting"></a>Problemen met het automatiseringsaccount oplossen

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

5. Als deze niet wordt vermeld, registreert u de **Microsoft.Automation-provider** door de stappen op te volgen bij [Fouten oplossen voor registratie van resourcesprovider.](/azure/azure-resource-manager/resource-manager-register-provider-errors)

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of het probleem niet oplossen, kun je een van de volgende kanalen proberen voor extra ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport)verbinding met het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.