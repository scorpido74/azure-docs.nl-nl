---
title: Veelvoorkomende verificatiefouten oplossen | Azure Marketplace
description: Biedt ondersteuning bij veelvoorkomende verificatiefouten bij het gebruik van de API's voor cloudpartners.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 1da9bbd1ed4bc4abea0699e56d8adc397086d6e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280419"
---
# <a name="troubleshooting-common-authentication-errors"></a>Problemen met veelvoorkomende verificatiefouten oplossen

In dit artikel wordt u geholpen bij veelvoorkomende verificatiefouten bij het gebruik van de API's van cloudpartnerportalen.

## <a name="unauthorized-error"></a>Ongeautoriseerde fout

Als er consistent `401 unauthorized` fouten worden gemaakt, controleert u of u een geldig toegangstoken hebt.  Als u dit nog niet hebt gedaan, maakt u een basisAzure Active Directory-toepassing (Azure AD) en een serviceprincipal zoals beschreven in [De portal gebruiken om een Azure Active Directory-toepassing en serviceprincipal te maken die toegang hebben tot bronnen.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) Gebruik vervolgens de toepassing of een eenvoudig HTTP POST-verzoek om uw toegang te verifiëren.  U bevat de tenant-id, toepassings-id, object-id en de geheime sleutel om het toegangstoken te verkrijgen zoals weergegeven in de volgende afbeelding:

![Problemen met de fout van 401 oplossen](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Fout: Niet-toegestaan

Als er `403 forbidden` een fout optreedt, moet u ervoor zorgen dat de juiste serviceprincipal is toegevoegd aan uw uitgeversaccount in de Cloud Partner Portal.
Volg de stappen [op de](./cloud-partner-portal-api-prerequisites.md) pagina Voorwaarden om uw serviceprincipal aan de portal toe te voegen.

Als de juiste serviceprincipal is toegevoegd, controleert u alle andere informatie. Let goed op de object-id die op de portal is ingevoerd. Er zijn twee object-id's op de registratiepagina van Azure Active Directory-apps en u moet de lokale object-id gebruiken. U de juiste waarde vinden door naar de pagina **App-registraties** voor uw app te gaan en op de app-naam te klikken onder **Beheerde toepassing in de lokale map.** Dit brengt u naar de lokale eigenschappen voor de app, waar u de juiste object-id vinden op de pagina **Eigenschappen,** zoals in de volgende afbeelding wordt weergegeven. Zorg er ook voor dat u de juiste uitgever-id gebruikt wanneer u de serviceprincipal toevoegt en de API-aanroep maakt.

![Problemen met de fout van 403 oplossen](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
