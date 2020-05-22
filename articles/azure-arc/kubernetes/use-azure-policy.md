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
ms.openlocfilehash: c017e9422733069ffd93f6dff72ecb884da057c4
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779950"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Gebruik Azure Policy om cluster configuraties op schaal toe te passen (preview-versie)

## <a name="overview"></a>Overzicht

Gebruik Azure Policy voor het afdwingen van elke `Microsoft.Kubernetes/connectedclusters` resource of een Git-OPS- `Microsoft.ContainerService/managedClusters` bron die specifiek is `Microsoft.KubernetesConfiguration/sourceControlConfigurations` toegepast.  Als u Azure Policy wilt gebruiken, selecteert u een bestaande beleids definitie en maakt u een beleids toewijzing.  Wanneer u de beleids toewijzing maakt, stelt u het bereik voor de toewijzing in: dit is een Azure-resource groep of-abonnement.  U stelt ook de para meters in voor de `sourceControlConfiguration` die worden gemaakt.  Zodra de toewijzing is gemaakt, worden in de beleids engine alle `connectedCluster` of `managedCluster` bronnen die zich binnen het bereik bevinden, geïdentificeerd en toegepast `sourceControlConfiguration` .

Als u meerdere Git-opslag plaatsen gebruikt als de bronnen van de waarheid voor elk cluster (bijvoorbeeld één opslag plaats voor centrale IT/cluster operator en andere opslag plaatsen voor toepassings teams), kunt u dit inschakelen met behulp van meerdere beleids toewijzingen, elke beleids toewijzing die is geconfigureerd om een andere Git-opslag plaats te gebruiken.

## <a name="create-a-custom-policy-definition"></a>Een aangepaste beleidsdefinitie maken

1. Ga in het Azure Portal naar beleid en selecteer in het gedeelte **ontwerpen** van de zijbalk de optie **definities**.
2. Selecteer **+ beleids definitie**.
3. Stel de **definitie locatie** in voor uw abonnement of beheer groep.  Hiermee bepaalt u het breedste bereik waarin de beleids definitie kan worden gebruikt.
4. Geef het beleid een **naam** en **Beschrijving**.
5. Kies onder categorie de optie **nieuwe maken**en *Kubernetes-cluster schrijven-Azure-boog*
6. Kopieer/plak in het invoervak **beleids regel** de inhoud van dit [voor beeld van een beleids definitie](https://raw.githubusercontent.com/Azure/arc-k8s-demo/master/policy/Ensure-GitOps-configuration-for-Kubernetes-cluster.json).
7. **Opslaan**.

Deze stap voor het maken van een aangepaste beleids definitie is niet nodig wanneer het werk is voltooid om dit ingebouwde beleid te maken.

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

1. Ga in het Azure Portal naar beleid en selecteer in het gedeelte **ontwerpen** van de zijbalk de optie **definities**.
2. Zoek de definitie die u zojuist hebt gemaakt en selecteer deze.
3. Selecteer in de pagina acties **toewijzen**.
4. Stel het **bereik** in op de beheer groep, het abonnement of de resource groep waarin de beleids toewijzing van toepassing is.
5. Als u resources uit het beleids bereik wilt uitsluiten, stelt u **uitsluitingen**in.
6. Geef de beleids toewijzing een **naam** en **Beschrijving** die u kunt gebruiken om deze gemakkelijk te identificeren.
7. Zorg ervoor dat het **afdwingen van beleid** is ingesteld op *ingeschakeld*.
8. Selecteer **Volgende**.
9. Stel parameter waarden in die worden gebruikt tijdens het maken van de `sourceControlConfiguration` .
10. Selecteer **Volgende**.
11. Schakel **een herstel taak maken**in.
12. Verzeker u ervan dat het **maken van een beheerde identiteit** is ingeschakeld en dat de identiteit **Inzender** machtigingen heeft.  Zie [dit document](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal) en [de opmerking in dit document](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) voor meer informatie over de machtigingen die u nodig hebt.
13. Selecteer **controleren + maken**.

Nadat de beleids toewijzing is gemaakt, wordt voor elke nieuwe `connectedCluster` resource (of `managedCluster` resource waarbij de GitOps-agents zijn geïnstalleerd) die zich binnen het bereik van de toewijzing bevindt, de `sourceControlConfiguration` toegepast.  Voor bestaande clusters moet u een herstel taak hand matig uitvoeren.  Het duurt doorgaans van 10-20 minuten voordat de beleids toewijzing van kracht wordt.

## <a name="verify-a-policy-assignment"></a>Een beleids toewijzing controleren

1. In de Azure Portal gaat u naar een van uw `connectedCluster` resources en selecteert u in de sectie **instellingen** van de zijbalk **beleids regels**. (De UX voor AKS Managed cluster is nog niet geïmplementeerd, maar is wel beschikbaar.)
2. In de lijst ziet u de beleids toewijzing die u hierboven hebt gemaakt, en de **nalevings status** moet *voldoen*aan het beleid.
3. Selecteer in de sectie **instellingen** van de zijbalk **configuraties**.
4. In de lijst ziet u `sourceControlConfiguration` dat de beleids toewijzing is gemaakt.
5. **Kubectl** gebruiken voor het opvragen van het cluster: u ziet de naam ruimte en artefacten die zijn gemaakt door `sourceControlConfiguration` .
6. Binnen vijf minuten ziet u in de cluster de artefacten die worden beschreven in de manifesten in het geconfigureerde Git-opslag plaats.

## <a name="next-steps"></a>Volgende stappen

* [Azure Monitor instellen voor containers met Kubernetes-clusters die zijn ingeschakeld voor Arc](./deploy-azure-monitor-for-containers.md)
