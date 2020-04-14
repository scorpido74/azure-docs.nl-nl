---
title: Veelvoorkomende verificatiefouten oplossen | Azure Marketplace
description: Biedt ondersteuning bij veelvoorkomende verificatiefouten bij het gebruik van de API's voor cloudpartners.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: d8fd1eb4bef987b4a8605e4be780512a914ec8b5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255988"
---
# <a name="troubleshooting-common-authentication-errors"></a>Problemen met veelvoorkomende verificatiefouten oplossen

> [!NOTE]
> De API's van cloudpartnerportalen zijn geïntegreerd met het Partner Center en blijven werken nadat uw aanbiedingen zijn gemigreerd naar partnercentrum. De integratie brengt kleine veranderingen met zich mee. Bekijk de wijzigingen in [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) om ervoor te zorgen dat uw code blijft werken na de migratie naar partnercentrum.

In dit artikel wordt u geholpen bij veelvoorkomende verificatiefouten bij het gebruik van de API's van cloudpartnerportalen.

## <a name="unauthorized-error"></a>Ongeautoriseerde fout

Als er consistent `401 unauthorized` fouten worden gemaakt, controleert u of u een geldig toegangstoken hebt.  Als u dit nog niet hebt gedaan, maakt u een basisAzure Active Directory-toepassing (Azure AD) en een serviceprincipal zoals beschreven in [De portal gebruiken om een Azure Active Directory-toepassing en serviceprincipal te maken die toegang hebben tot bronnen.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) Gebruik vervolgens de toepassing of een eenvoudig HTTP POST-verzoek om uw toegang te verifiëren.  U bevat de tenant-id, toepassings-id, object-id en de geheime sleutel om het toegangstoken te verkrijgen zoals weergegeven in de volgende afbeelding:

![Problemen met de fout van 401 oplossen](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Fout: Niet-toegestaan

Als er `403 forbidden` een fout optreedt, moet u ervoor zorgen dat de juiste serviceprincipal is toegevoegd aan uw uitgeversaccount in de Cloud Partner Portal.
Volg de stappen [op de](./cloud-partner-portal-api-prerequisites.md) pagina Voorwaarden om uw serviceprincipal aan de portal toe te voegen.

Als de juiste serviceprincipal is toegevoegd, controleert u alle andere informatie. Let goed op de object-id die op de portal is ingevoerd. Er zijn twee object-id's op de registratiepagina van Azure Active Directory-apps en u moet de lokale object-id gebruiken. U de juiste waarde vinden door naar de pagina **App-registraties** voor uw app te gaan en op de app-naam te klikken onder **Beheerde toepassing in de lokale map.** Dit brengt u naar de lokale eigenschappen voor de app, waar u de juiste object-id vinden op de pagina **Eigenschappen,** zoals in de volgende afbeelding wordt weergegeven. Zorg er ook voor dat u de juiste uitgever-id gebruikt wanneer u de serviceprincipal toevoegt en de API-aanroep maakt.

![Problemen met de fout van 403 oplossen](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
