---
title: bestand opnemen
description: bestand opnemen
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: a5532b7a4574f3c2cc057255742c72bf032d180b
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "75456797"
---
Deze voorbeelden illustreren verschillende taken die kunnen worden uitgevoerd in scenario's voor het beheer van meerdere tenants.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Hiermee worden opslagaccounts geïmplementeerd in twee verschillende resourcegroepen.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Maakt Azure-beheerservices, koppelt deze en implementeert aanvullende oplossingen. Gebruik de sjabloon **rgWithAzureMgmt.json** voor een end-to-end-implementatie. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Configureert en schakelt Azure Security Center in binnen het Azure-doelabonnement. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Hiermee wordt Azure Sentinel geïmplementeerd en ingeschakeld op een bestaande Log Analytics-werkruimte in een gedelegeerd abonnement. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Met deze sjablonen kunt u Log Analytics VM-extensies implementeren op uw Windows- en Linux-VM's en deze verbinden met de toegewezen Log Analytics-werkruimte |
