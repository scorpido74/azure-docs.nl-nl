---
title: bestand opnemen
description: bestand opnemen
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 04/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 4a6d9ef04a33c84d68dff1429fb39a193a249280
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204523"
---
Deze voorbeelden illustreren verschillende taken die kunnen worden uitgevoerd in scenario's voor het beheer van meerdere tenants.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [create-keyvault-secret](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret) | Maakt een sleutelkluis in de tenant van de klant en maakt toegangsbeleid.
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | Hiermee worden opslagaccounts geïmplementeerd in twee verschillende resourcegroepen.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Maakt Azure-beheerservices, koppelt deze en implementeert aanvullende oplossingen. Gebruik de sjabloon **rgWithAzureMgmt.json** voor een end-to-end-implementatie. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | Configureert en schakelt Azure Security Center in binnen het Azure-doelabonnement. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | Hiermee wordt Azure Sentinel geïmplementeerd en ingeschakeld op een bestaande Log Analytics-werkruimte in een gedelegeerd abonnement. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | Met deze sjablonen kunt u Log Analytics VM-extensies implementeren op uw Windows- en Linux-VM's en deze verbinden met de toegewezen Log Analytics-werkruimte |
