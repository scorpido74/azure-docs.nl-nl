---
title: Gebruik Azure Policy om cluster configuraties op schaal toe te passen (preview-versie)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Policy gebruiken om cluster configuraties op schaal toe te passen
keywords: Kubernetes, Arc, azure, K8s, containers
ms.openlocfilehash: e4279f3d89376320116067bf191e3196271918ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050047"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Gebruik Azure Policy om cluster configuraties op schaal toe te passen (preview-versie)

## <a name="overview"></a>Overzicht

Gebruik Azure Policy om af te dwingen dat elke `Microsoft.Kubernetes/connectedclusters` resource of Git-Ops ingeschakelde `Microsoft.ContainerService/managedClusters` resource hiervoor specifieke is `Microsoft.KubernetesConfiguration/sourceControlConfigurations` toegepast. Als u Azure Policy wilt gebruiken, selecteert u een bestaande beleids definitie en maakt u een beleids toewijzing. Wanneer u de beleids toewijzing maakt, stelt u het bereik voor de toewijzing in: dit is een Azure-resource groep of-abonnement. U stelt ook de para meters in voor de `sourceControlConfiguration` die worden gemaakt. Zodra de toewijzing is gemaakt, worden in de beleids engine alle `connectedCluster` of `managedCluster` bronnen die zich binnen het bereik bevinden, geïdentificeerd en toegepast `sourceControlConfiguration` .

Als u meerdere Git-opslag plaatsen gebruikt als de bronnen van de waarheid voor elk cluster (bijvoorbeeld één opslag plaats voor centrale IT/cluster operator en andere opslag plaatsen voor toepassings teams), kunt u dit inschakelen met behulp van meerdere beleids toewijzingen, elke beleids toewijzing die is geconfigureerd om een andere Git-opslag plaats te gebruiken.

## <a name="prerequisite"></a>Vereiste

Controleer of u `Microsoft.Authorization/policyAssignments/write` machtigingen hebt voor het bereik (abonnement of resource groep) waar u deze beleids toewijzing wilt maken.

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

1. Ga in het Azure Portal naar beleid en selecteer in het gedeelte **ontwerpen** van de zijbalk de optie **definities**.
2. Kies het ingebouwde beleid ' Deploy GitOps to Kubernetes cluster ' in de categorie ' Kubernetes ' en klik op **Assign**.
3. Stel het **bereik** in op de beheer groep, het abonnement of de resource groep waarin de beleids toewijzing van toepassing is.
4. Als u resources uit het beleids bereik wilt uitsluiten, stelt u **uitsluitingen**in.
5. Geef de beleids toewijzing een **naam** en **Beschrijving** die u kunt gebruiken om deze gemakkelijk te identificeren.
6. Zorg ervoor dat het **afdwingen van beleid** is ingesteld op *ingeschakeld*.
7. Selecteer **Next**.
8. Stel parameter waarden in die worden gebruikt tijdens het maken van de `sourceControlConfiguration` .
9. Selecteer **Next**.
10. Schakel **een herstel taak maken**in.
11. Verzeker u ervan dat het **maken van een beheerde identiteit** is ingeschakeld en dat de identiteit **Inzender** machtigingen heeft. Zie [dit document](../../governance/policy/assign-policy-portal.md) en [de opmerking in dit document](../../governance/policy/how-to/remediate-resources.md) voor meer informatie over de machtigingen die u nodig hebt.
12. Selecteer **Controleren + maken**.

Nadat de beleids toewijzing is gemaakt, wordt voor elke nieuwe `connectedCluster` resource (of `managedCluster` resource waarbij de GitOps-agents zijn geïnstalleerd) die zich binnen het bereik van de toewijzing bevindt, de `sourceControlConfiguration` toegepast. Voor bestaande clusters moet u een herstel taak hand matig uitvoeren. Het duurt doorgaans van 10-20 minuten voordat de beleids toewijzing van kracht wordt.

## <a name="verify-a-policy-assignment"></a>Een beleids toewijzing controleren

1. In de Azure Portal gaat u naar een van uw `connectedCluster` resources en selecteert u in de sectie **instellingen** van de zijbalk **beleids regels**. (De UX voor het AKS-cluster is nog niet geïmplementeerd, maar het is wel beschikbaar.)
2. In de lijst ziet u de beleids toewijzing die u hierboven hebt gemaakt, en de **nalevings status** moet *voldoen*aan het beleid.
3. Selecteer in de sectie **instellingen** van de zijbalk **configuraties**.
4. In de lijst ziet u `sourceControlConfiguration` dat de beleids toewijzing is gemaakt.
5. **Kubectl** gebruiken voor het opvragen van het cluster: u ziet de naam ruimte en artefacten die zijn gemaakt door `sourceControlConfiguration` .
6. Binnen vijf minuten ziet u in de cluster de artefacten die worden beschreven in de manifesten in het geconfigureerde Git-opslag plaats.

## <a name="next-steps"></a>Volgende stappen

* [Azure Monitor instellen voor containers met Kubernetes-clusters die zijn ingeschakeld voor Arc](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
