---
author: baanders
description: include-bestand voor het controleren van de roltoewijzing in de Azure Digital Apparaatdubbels-installatie
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e651b02bf72ced58b6cba637a68ace3258514176
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405568"
---
Een manier om te controleren of u de roltoewijzing hebt ingesteld, is door de roltoewijzingen voor het Azure Digital Apparaatdubbels-exemplaar in het [Azure Portal](https://portal.azure.com)weer te geven. Ga naar de pagina van de portal van [Azure Digital apparaatdubbels-instanties](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) (u kunt deze koppeling gebruiken of ernaar zoeken in de zoek balk van de portal) en selecteer de naam van het exemplaar dat u wilt controleren. 

Bekijk vervolgens alle toegewezen rollen onder *toegangs beheer (IAM) > roltoewijzingen*. De gebruiker moet in de lijst worden weer gegeven met een rol van de *Azure Digital apparaatdubbels-eigenaar (preview)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Weer gave van de roltoewijzingen voor een Azure Digital Apparaatdubbels-exemplaar in Azure Portal":::