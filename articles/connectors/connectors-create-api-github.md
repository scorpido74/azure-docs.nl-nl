---
title: Verbinding maken met GitHub
description: GitHub-gebeurtenissen bewaken met GitHub REST-Api's en Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 7dc865f520b6f4667ace720e656a210e0252d1a1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789748"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Verbinding maken met GitHub vanuit Azure Logic Apps

GitHub is een op het web gebaseerde Git-hosting service voor opslag plaatsen die alle gedistribueerde revisie beheer en SCM-functionaliteit (broncode beheer) biedt in Git en andere functies.

[Maak eerst een logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om aan de slag te gaan met de GitHub-connector.

## <a name="create-a-connection-to-github"></a>Een verbinding met GitHub maken

Als u de GitHub-connector in een logische app wilt gebruiken, moet u eerst een *verbinding* maken en vervolgens Details voor deze eigenschappen opgeven: 

| Eigenschap | Verplicht | Beschrijving | 
| -------- | -------- | ----------- | 
| Token | Ja | Geef uw GitHub-referenties op. |

Nadat u de verbinding hebt gemaakt, kunt u de acties uitvoeren en Luis teren naar de triggers die in dit artikel worden beschreven.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina van de connector](/connectors/github/)voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)