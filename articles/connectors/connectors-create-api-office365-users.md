---
title: Verbinding maken met Office 365-gebruikers
description: Taken en werkstromen automatiseren die profielen in Office 365-gebruikersprofielen opdoen en beheren met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666853"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Profielen in Office 365-gebruikers downloaden en beheren met Azure Logic Apps

Maak verbinding met Office 365-gebruikers om profielen, zoekgebruikers en meer te krijgen. Met Office 365-gebruikers u het als nog niet doen:

* Bouw uw bedrijfsstroom op basis van de gegevens die u van Office 365-gebruikers krijgt. 
* Gebruik acties die directe rapporten krijgen, het gebruikersprofiel van een manager krijgen en meer. Deze acties krijgen een antwoord en maken de uitvoer beschikbaar voor andere acties. Download bijvoorbeeld de directe rapporten van een persoon en neem deze informatie en werk een SQL Azure-database bij. 

U nu aan de slag door een logische app te maken, zie [Een logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Een verbinding maken met Office 365-gebruikers

Wanneer u deze connector toevoegt aan uw logische apps, moet u zich aanmelden bij uw Office 365-gebruikersaccount, zodat Azure Logic Apps verbinding kunnen maken met uw account.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Nadat u de verbinding hebt gemaakt, voert u de eigenschappen van Office 365-gebruikers in, zoals de gebruikers-id. De **VERWIJZING REST API** in dit artikel beschrijft deze eigenschappen.

## <a name="connector-specific-details"></a>Connector-specifieke details

Voor technische details over triggers, acties en limieten, die worden beschreven in de Swagger-beschrijving van de connector, raadpleegt u de [referentiepagina van](/connectors/officeusers/)de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](apis-list.md)