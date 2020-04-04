---
title: Azure App Service-resources verplaatsen
description: Gebruik Azure Resource Manager om App Service-resources te verplaatsen naar een nieuwe brongroep of -abonnement.
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: d0ecd117bdcda9238e310a3020dba19a6871a3fc
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655770"
---
# <a name="move-guidance-for-app-service-resources"></a>Richtlijnen verplaatsen voor App-servicebronnen

In dit artikel worden de stappen beschreven om app-servicebronnen te verplaatsen. Er zijn specifieke vereisten voor het verplaatsen van App Service-resources naar een nieuw abonnement.

## <a name="move-across-subscriptions"></a>Over abonnement heen gaan

Wanneer u een web-app over abonnementen verplaatst, geldt de volgende richtlijnen:

- De resourcegroep bestemming mag geen bestaande App Service-bronnen hebben. Bronnen voor app-service omvatten:
    - Web Apps
    - App Service-abonnementen
    - Geüploade of geïmporteerde TLS/SSL-certificaten
    - App Service-omgevingen
- Alle App-servicebronnen in de resourcegroep moeten samen worden verplaatst. Houd er rekening mee dat App-serviceomgevingen niet kunnen worden verplaatst naar een nieuwe resourcegroep of naar een nieuw abonnement.
- U een certificaat verplaatsen dat naar een web is gebonden zonder de TLS-bindingen te verwijderen, zolang het certificaat wordt verplaatst met alle andere bronnen in de resourcegroep.
- App Service-resources kunnen alleen worden verplaatst van de resourcegroep waarin ze oorspronkelijk zijn gemaakt. Als een App Service-bron zich niet meer in de oorspronkelijke resourcegroep bevindt, verplaatst u deze terug naar de oorspronkelijke brongroep. Verplaats de resource vervolgens over abonnementen.

Als u de oorspronkelijke brongroep niet meer weet, u deze vinden via diagnostische gegevens. Selecteer voor uw web-app **Diagnose en los problemen op.** Selecteer vervolgens **Configuratie en beheer**.

![Diagnostische gegevens selecteren](./media/app-service-move-limitations/select-diagnostics.png)

Selecteer **Migratieopties**.

![Migratieopties selecteren](./media/app-service-move-limitations/select-migration.png)

Selecteer de optie voor aanbevolen stappen om de web-app te verplaatsen.

![Aanbevolen stappen selecteren](./media/app-service-move-limitations/recommended-steps.png)

U ziet de aanbevolen acties die u moet uitvoeren voordat u de resources verplaatst. De informatie bevat de oorspronkelijke brongroep voor de web-app.

![Aanbevelingen](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Ondersteuning verplaatsen

Zie Ondersteuningsstatus verplaatsen voor:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Volgende stappen

Zie Resources verplaatsen naar [een nieuwe resourcegroep of -abonnement](../move-resource-group-and-subscription.md)voor opdrachten om resources te verplaatsen.
