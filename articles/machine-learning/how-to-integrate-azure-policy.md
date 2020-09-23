---
title: Controleren en beheren van naleving met Azure Policy
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van Azure Policy voor het gebruik van ingebouwde beleids regels voor Azure Machine Learning.
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: e7eebb22efe32b290e078348337049c6c3e762db
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995538"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Azure Machine Learning controleren en beheren met behulp van Azure Policy

[Azure Policy](/azure/governance/policy) is een beheer programma waarmee u ervoor kunt zorgen dat Azure-resources voldoen aan uw beleid. Met Azure Machine Learning kunt u de volgende beleids regels toewijzen:

* Door de **klant beheerde sleutel**: controleren of afdwingen of werk ruimten een door de klant beheerde sleutel moeten gebruiken.
* **Privé-koppeling**: controleren of werk ruimten een persoonlijk eind punt gebruiken om te communiceren met een virtueel netwerk.

Beleids regels kunnen worden ingesteld op verschillende bereiken, zoals op het niveau van het abonnement of de resource groep. Zie de [Azure Policy-documentatie](/azure/governance/policy/overview)voor meer informatie.

## <a name="built-in-policies"></a>Ingebouwd beleid

Azure Machine Learning biedt een set beleids regels die u kunt gebruiken voor algemene scenario's met Azure Machine Learning. U kunt deze beleids definities toewijzen aan uw bestaande abonnement of gebruiken als basis voor het maken van uw eigen aangepaste definities. Zie voor een volledige lijst van het ingebouwde beleid voor Azure Machine Learning [ingebouwde beleids regels voor Azure machine learning](/azure/governance/policy/samples/built-in-policies#machine-learning).

Gebruik de volgende stappen om de ingebouwde beleids definities weer te geven die betrekking hebben op Azure Machine Learning:

1. Ga naar __Azure Policy__ in het [Azure Portal](https://portal.azure.com).
1. __Definities__selecteren.
1. Selecteer bij __type__de optie _ingebouwd_en selecteer bij __categorie__de optie __machine learning__.

Hier kunt u beleids definities selecteren om ze weer te geven. Tijdens het weer geven van een definitie kunt u de koppeling __toewijzen__ gebruiken om het beleid toe te wijzen aan een specifiek bereik en de para meters voor het beleid configureren. Zie [een beleid toewijzen-Portal](/azure/governance/policy/assign-policy-portal)voor meer informatie.

U kunt ook beleids regels toewijzen met behulp van [Azure PowerShell](/azure/governance/policy/assign-policy-powershell), [Azure cli](https://docs.microsoft.com/azure/governance/policy/assign-policy-azurecli)en [sjablonen](/azure/governance/policy/assign-policy-template).

## <a name="workspaces-encryption-with-customer-managed-key"></a>Werk ruimten versleuteling met door de klant beheerde sleutel

Hiermee wordt bepaald of werk ruimten moeten worden versleuteld met een door de klant beheerde sleutel (CMK) of met behulp van een door micro soft beheerde sleutel voor het versleutelen van metrische gegevens en meta gegevens. Voor meer informatie over het gebruik van CMK raadpleegt u de sectie [Azure Cosmos DB](concept-enterprise-security.md#azure-cosmos-db) van het beveiligings artikel van de onderneming.

Als u dit beleid wilt configureren, stelt u de para meter effect in op __controleren__ of __weigeren__. Als deze is ingesteld op __audit__, kunt u werk ruimten maken zonder een CMK en wordt er een waarschuwings gebeurtenis in het activiteiten logboek gemaakt.

Als het beleid is ingesteld op __weigeren__, kunt u geen werk ruimte maken, tenzij er een CMK is opgegeven. Het maken van een werk ruimte zonder CMK resulteert in een fout die vergelijkbaar is met `Resource 'clustername' was disallowed by policy` en er wordt een fout in het activiteiten logboek gemaakt. De beleids-id wordt ook geretourneerd als onderdeel van deze fout.

## <a name="workspaces-should-use-private-link"></a>Werk ruimten moeten een persoonlijke koppeling gebruiken

Hiermee bepaalt u of werk ruimten persoonlijke Azure-koppelingen moeten gebruiken om te communiceren met Azure Virtual Network. Zie [persoonlijke koppeling voor een werk ruimte configureren](how-to-configure-private-link.md)voor meer informatie over het gebruik van een persoonlijke koppeling.

Als u dit beleid wilt configureren, stelt u de para meter effect in op __audit__. Als u een werk ruimte maakt zonder gebruik van een persoonlijke koppeling, wordt er een waarschuwings gebeurtenis in het activiteiten logboek gemaakt.

## <a name="next-steps"></a>Volgende stappen

* [Documentatie voor Azure Policy](/azure/governance/policy/overview)
* [Ingebouwd beleid voor Azure Machine Learning](policy-reference.md)
* [Werken met beveiligings beleid met Azure Security Center](/azure/security-center/tutorial-security-policy)