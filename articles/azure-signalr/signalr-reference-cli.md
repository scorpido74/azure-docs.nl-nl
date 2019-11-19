---
title: Azure CLI-voorbeelden - Azure SignalR-service
description: Volg echte voor beelden voor meer informatie over Azure CLI voor Azure signalerings service. U leert hoe u de signalerings service kunt maken met meer Azure-Services.
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 3c28781362e60d22b0d9a4e3754daaa24e5b61ee
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158124"
---
# <a name="azure-cli-reference"></a>Naslaginformatie over Azure CLI

De volgende tabel bevat links naar bash-scripts voor het maken van een Azure SignalR-service met behulp van Azure CLI.

| | |
|-|-|
|**Maken**||
| [Een nieuwe SignalR-service en resourcegroep maken](scripts/signalr-cli-create-service.md) | Hiermee maakt u een nieuwe resource voor een Azure SignalR-service in een nieuwe resourcegroep met een willekeurige naam.  |
|**Integreren**||
| [Een nieuwe SignalR-service maken en een web-app die is geconfigureerd voor het gebruik van SignalR](scripts/signalr-cli-create-with-app-service.md) | Hiermee maakt u een nieuwe resource voor een Azure SignalR-service in een nieuwe resourcegroep met een willekeurige naam. Voegt ook een nieuwe web-app toe en App Service plan om een ASP.NET Core web-app te hosten die gebruikmaakt van de signalerings service. De web-app wordt geconfigureerd met een app-instelling om verbinding te maken met de nieuwe resource voor de SignalR-service. |
| [Een nieuwe SignalR-service maken en een web-app die is geconfigureerd voor het gebruik van SignalR en GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Hiermee maakt u een nieuwe resource voor een Azure SignalR-service in een nieuwe resourcegroep met een willekeurige naam. Voegt ook een nieuwe Azure-web-app en hosting plan toe om een ASP.NET Core web-app te hosten die gebruikmaakt van de signalerings service. De web-app wordt geconfigureerd met app-instellingen voor de verbindingsreeks voor de nieuwe SignalR-serviceresource, en met clientgeheimen ter ondersteuning van [GitHub-verificatie](https://developer.github.com/v3/guides/basics-of-authentication/), zoals wordt beschreven in de [zelfstudie over verificatie](signalr-concept-authenticate-oauth.md). De web-app wordt ook geconfigureerd voor het gebruik van een implementatiebron van een lokale Git-opslagplaats. |
| | |
