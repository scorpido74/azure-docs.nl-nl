---
title: Azure App Service resources verplaatsen
description: Gebruik Azure Resource Manager om App Service resources te verplaatsen naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: dca9b3a8f328192683cfde586f0ccdb01e84dc16
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150897"
---
# <a name="move-guidance-for-app-service-resources"></a>Richt lijnen voor App Service resources verplaatsen

De stappen voor het verplaatsen van App Service resources zijn afhankelijk van het feit of u de resources in een abonnement verplaatst of naar een nieuw abonnement.

## <a name="move-in-same-subscription"></a>Hetzelfde abonnement verplaatsen

Wanneer u een web-app _binnen hetzelfde abonnement_verplaatst, kunt u geen SSL-certificaten van derden meer verplaatsen. U kunt echter een web-app naar de nieuwe resource groep verplaatsen zonder het certificaat van de derde partij te verplaatsen en de SSL-functionaliteit van uw app blijft werken.

Als u verplaatsen van het SSL-certificaat met de Web-App wilt, volgt u deze stappen:

1. Het certificaat van derden verwijderen uit de web-app, maar een kopie van uw certificaat blijven gebruiken
2. Verplaats de Web-App.
3. Upload het certificaat van derden naar de verplaatste web-app.

## <a name="move-across-subscriptions"></a>Scha kelen tussen abonnementen

Wanneer u een Web-App verplaatst _voor abonnementen_, gelden de volgende beperkingen:

- De doelresourcegroep mag geen bestaande App Service-resources. App Service-resources zijn onder andere:
    - Web Apps
    - App Service-abonnementen
    - Geüpload of geïmporteerd SSL-certificaten
    - App Service-omgevingen
- Alle App Service-resources in de resourcegroep, moeten tegelijk worden verplaatst.
- App Service-resources kunnen alleen worden verplaatst uit de resourcegroep waarin ze oorspronkelijk zijn gemaakt. Als een App Service resource niet langer in de oorspronkelijke resource groep staat, zet u deze terug naar de oorspronkelijke resource groep. Verplaats vervolgens de resource over abonnementen.

Als u de oorspronkelijke resource groep niet meer weet, kunt u deze vinden via diagnostische gegevens. Selecteer voor uw web-app **diagnose en los problemen**op. Selecteer vervolgens **configuratie en beheer**.

![Diagnostische gegevens selecteren](./media/app-service-move-limitations/select-diagnostics.png)

Selecteer **migratie opties**.

![Migratie opties selecteren](./media/app-service-move-limitations/select-migration.png)

Selecteer de optie voor de aanbevolen stappen voor het verplaatsen van de web-app.

![Aanbevolen stappen selecteren](./media/app-service-move-limitations/recommended-steps.png)

U ziet de aanbevolen acties die u moet uitvoeren voordat u de resources verplaatst. De informatie bevat de oorspronkelijke resource groep voor de web-app.

![Aanbevelingen](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>App Service Certificate verplaatsen

U kunt uw App Service Certificate verplaatsen naar een nieuwe resource groep of een nieuw abonnement. Als uw App Service Certificate is gebonden aan een web-app, moet u een aantal stappen uitvoeren voordat u de resources verplaatst naar een nieuw abonnement. Verwijder de SSL-binding en het persoonlijke certificaat van de Web-App voordat u de resources verplaatst. De App Service Certificate hoeft niet te worden verwijderd, alleen het persoonlijke certificaat in de web-app.

## <a name="move-support"></a>Ondersteuning verplaatsen

Zie de ondersteunings status verplaatsen voor het volgende om te bepalen welke App Service resources kunnen worden verplaatst:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Volgende stappen

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../resource-group-move-resources.md)voor opdrachten voor het verplaatsen van resources.
