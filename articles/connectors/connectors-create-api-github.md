---
title: Verbinding maken met GitHub-Azure Logic Apps
description: GitHub-gebeurtenissen bewaken met GitHub REST-Api's en Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 6a6e2a803ee2a272189abf0f21796b2305eea40b
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050901"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Verbinding maken met GitHub vanuit Azure Logic Apps

GitHub is een op het web gebaseerde Git-hosting service voor opslag plaatsen die alle gedistribueerde revisie beheer en SCM-functionaliteit (broncode beheer) biedt in Git en andere functies.

[Maak eerst een logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om aan de slag te gaan met de GitHub-connector.

## <a name="create-a-connection-to-github"></a>Een verbinding met GitHub maken

Als u de GitHub-connector in een logische app wilt gebruiken, moet u eerst een *verbinding* maken en vervolgens Details voor deze eigenschappen opgeven: 

| Eigenschap | Vereist | Description | 
| -------- | -------- | ----------- | 
| Token | Ja | Geef uw GitHub-referenties op. |

Nadat u de verbinding hebt gemaakt, kunt u de acties uitvoeren en Luis teren naar de triggers die in dit artikel worden beschreven.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina van de connector](/connectors/github/)voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps](../connectors/apis-list.md) -connectors