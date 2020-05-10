---
title: Verbinding maken met Dynamics 365
description: Dynamics 365-records maken en beheren met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/09/2020
tags: connectors
ms.openlocfilehash: 00bf8ea2b783e09711a95f203bdfcce0e6b90b2c
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994306"
---
# <a name="create-and-manage-records-in-dynamics-365-by-using-azure-logic-apps"></a>Records maken en beheren in Dynamics 365 met behulp van Azure Logic Apps

Dynamics 365 maakt gebruik van de [common data service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro). Voor verbindingen met Dynamics 365, gebruikt u de [common data service-connector](https://docs.microsoft.com/connectors/commondataservice/).

> [!IMPORTANT]
> De [Dynamics 365-connector](https://docs.microsoft.com/connectors/dynamicscrmonline/) is afgeschaft, maar blijft werken totdat deze is verwijderd. Gebruik de Dynamics 365-connector niet voor nieuwe logische apps. Er is nog geen tijd lijn aangekondigd voor het verwijderen van de Dynamics 365-connector. U bent niet verplicht om bestaande Logic apps te converteren naar de Common Data Service-connector of andere geplande nieuwe connector, maar u moet uw Logic apps converteren wanneer de connector wordt verwijderd. Zie [Dynamics 365-connector is afgeschaft](https://docs.microsoft.com/power-platform/important-changes-coming)voor meer informatie.
>
> De [common data service-connector](https://docs.microsoft.com/connectors/commondataservice/) biedt dezelfde mogelijkheden als de afgeschafte Dynamics 365-connector, maar bevat verbeteringen die de betrouw baarheid verg Roten. Zie [common data service records maken en beheren met behulp van Azure Logic apps](../connectors/connect-common-data-service.md)voor meer informatie over het gebruik van de common data service-connector in Logic apps.

Zie de volgende onderwerpen voor meer informatie over Common Data Service:

* [Meer informatie: aan de slag met Common Data Service](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
* [Meer informatie: verbinding maken en analyseren van uw Dynamics 365-gegevens met behulp van het Power platform en Common Data Service](https://docs.microsoft.com/learn/wwl/connect-analyze-dynamics-365-data/)