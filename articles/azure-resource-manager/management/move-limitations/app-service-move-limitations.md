---
title: Azure App Service resources verplaatsen
description: Gebruik Azure Resource Manager om App Service resources te verplaatsen naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 338b0559a5de9468ff60024b88d0f676a9fc3e8b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479798"
---
# <a name="move-guidance-for-app-service-resources"></a>Richt lijnen voor App Service resources verplaatsen

In dit artikel worden de stappen beschreven voor het verplaatsen van App Service resources. Er zijn specifieke vereisten voor het verplaatsen van App Service-resources naar een nieuw abonnement.

## <a name="move-across-subscriptions"></a>Scha kelen tussen abonnementen

Bij het verplaatsen van een web-app tussen abonnementen, gelden de volgende richt lijnen:

- De doelresourcegroep mag geen bestaande App Service-resources. App Service-resources zijn onder andere:
    - Web-apps
    - App Service-abonnementen
    - Geüpload of geïmporteerd SSL-certificaten
    - App Service-omgevingen
- Alle App Service-resources in de resourcegroep, moeten tegelijk worden verplaatst. Houd er rekening mee dat App Service omgevingen niet kunnen worden verplaatst naar een nieuwe resource groep of naar een nieuw abonnement.
- U kunt een certificaat dat is gebonden aan een web verplaatsen zonder de SSL-bindingen te verwijderen, zolang het certificaat met alle andere resources in de resource groep is verplaatst.
- App Service-resources kunnen alleen worden verplaatst uit de resourcegroep waarin ze oorspronkelijk zijn gemaakt. Als een App Service resource niet langer in de oorspronkelijke resource groep staat, zet u deze terug naar de oorspronkelijke resource groep. Verplaats vervolgens de resource over abonnementen.

Als u de oorspronkelijke resource groep niet meer weet, kunt u deze vinden via diagnostische gegevens. Selecteer voor uw web-app **diagnose en los problemen**op. Selecteer vervolgens **configuratie en beheer**.

![Diagnostische gegevens selecteren](./media/app-service-move-limitations/select-diagnostics.png)

Selecteer **migratie opties**.

![Migratie opties selecteren](./media/app-service-move-limitations/select-migration.png)

Selecteer de optie voor de aanbevolen stappen voor het verplaatsen van de web-app.

![Aanbevolen stappen selecteren](./media/app-service-move-limitations/recommended-steps.png)

U ziet de aanbevolen acties die u moet uitvoeren voordat u de resources verplaatst. De informatie bevat de oorspronkelijke resource groep voor de web-app.

![Aanbevelingen](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Ondersteuning verplaatsen

Zie de ondersteunings status verplaatsen voor het volgende om te bepalen welke App Service resources kunnen worden verplaatst:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Volgende stappen

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../move-resource-group-and-subscription.md)voor opdrachten voor het verplaatsen van resources.
