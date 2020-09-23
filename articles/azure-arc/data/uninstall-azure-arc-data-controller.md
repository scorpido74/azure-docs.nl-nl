---
title: Azure Arc-gegevens controller verwijderen
description: Azure Arc-gegevens controller verwijderen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2bae661218989d49b74ed8ca3f694ccb912ef912
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936043"
---
# <a name="delete-azure-arc-data-controller"></a>Azure Arc-gegevens controller verwijderen

In het volgende artikel wordt beschreven hoe u een Azure Arc-gegevens controller verwijdert.

Voordat u doorgaat, moet u ervoor zorgen dat alle gegevens services die zijn gemaakt op de gegevens controller, als volgt worden verwijderd:

## <a name="log-in-to-the-data-controller"></a>Meld u aan bij de gegevens controller

Meld u aan bij de gegevens controller die u wilt verwijderen:

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>Bestaande gegevens services weer geven & verwijderen

Voer de volgende opdracht uit om te controleren of er SQL Managed instances zijn gemaakt:

```
azdata arc sql mi list
```

Voer voor elk door SQL beheerd exemplaar uit de bovenstaande lijst de opdracht verwijderen als volgt uit:

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

Als u wilt controleren op PostgreSQL grootschalige-instanties, voert u de volgende handelingen uit:

```
azdata arc postgres server list
```

En voer voor elk exemplaar van PostgreSQL grootschalige de opdracht verwijderen als volgt uit:
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>Controller verwijderen

Nadat alle door SQL beheerde instanties en PostgreSQL grootschalige-instanties zijn verwijderd, kan de gegevens controller als volgt worden verwijderd:

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>SCCs verwijderen (alleen Red Hat open Shift)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>U kunt ook de Azure Arc data controller-naam ruimte verwijderen


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>Volgende stappen

[Wat zijn gegevens services van Azure Arc ingeschakeld?](overview.md)