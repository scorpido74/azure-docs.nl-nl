---
title: bestand opnemen
description: bestand opnemen
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: dc4281c17b92e1720625764a52a34a94d6f296ab
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67175966"
---
Voordat u items gaat maken, gaan we het concept bereik bekijken. Azure biedt vier niveaus van beheer: beheergroepen, abonnement, resourcegroep en resources. [Beheergroepen](../articles/billing/billing-enterprise-mgmt-group-overview.md) zijn in een preview-versie. In de volgende afbeelding ziet u een voorbeeld van deze lagen.

![Bereik](./media/resource-manager-governance-scope/scope-levels.png)

U past beheerinstellingen toe op een of meer bereikniveaus. Het niveau dat u kiest, bepaalt de reikwijdte van de instelling. Lagere niveaus nemen instellingen over van hogere niveaus. Wanneer u een instelling toepast op het abonnement, wordt die instelling toegepast op alle resourcegroepen en resources in uw abonnement. Wanneer u een instelling toepast op de resourcegroep, wordt die instelling toegepast op de resourcegroep en alle resources daarvan. Een andere resourcegroep heeft die instelling echter niet.

Meestal is het handig om belangrijke instellingen toe te passen op een hogere niveaus en projectspecifieke vereisten op lagere niveaus. Stel, u wilt ervoor zorgen dat alle resources voor uw organisatie worden geïmplementeerd in bepaalde regio's. U bereikt dit door een beleid toe te passen op het abonnement dat de toegestane locaties bepaalt. Als andere gebruikers in uw organisatie nieuwe resourcegroepen en resources toevoegen, worden toegestane locaties automatisch afgedwongen. 
