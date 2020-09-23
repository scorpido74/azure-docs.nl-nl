---
title: Para meters voor de post gres-Engine configureren voor uw PostgreSQL grootschalige-Server groep in azure Arc
titleSuffix: Azure Arc enabled data services
description: Para meters voor de post gres-Engine configureren voor uw PostgreSQL grootschalige-Server groep in azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4e8813647211e0adbfe43a45ae0d19dc12a4a165
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936098"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>De instellingen van de data base-engine instellen voor Azure Arc enabled PostgreSQL grootschalige

In dit document worden de stappen beschreven voor het instellen van de instellingen van de data base-engine van uw PostgreSQL grootschalige-Server groep op aangepaste (niet-standaard) waarden. Raadpleeg de PostgreSQL [-documentatie voor](https://www.postgresql.org/docs/current/runtime-config.html)meer informatie over welke para meters voor de data base-engine kunnen worden ingesteld en wat de standaard waarde is.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> Voor beeld biedt geen ondersteuning voor het instellen van de volgende para meters: 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>Syntax

De algemene indeling van de opdracht voor het configureren van de instellingen van de data base-engine is:

```console
azdata arc postgres server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-engine-settings, --re}] {'<parameter name>=<parameter value>, ...'}
```

## <a name="show-the-current-custom-values-of-the-parameters-settings"></a>De huidige aangepaste waarden van de parameter instellingen weer geven

## <a name="with-azdata-cli-command"></a>Met azdata CLI-opdracht

```console
azdata arc postgres server show -n <server group name>
```

Bijvoorbeeld:

```console
azdata arc postgres server show -n postgres01
```

Met deze opdracht wordt de specificatie van de Server groep geretourneerd waarin u de para meters ziet die u hebt ingesteld. Als er geen engine\settings-sectie is, betekent dit dat alle para meters worden uitgevoerd op de standaard waarde:

```console
 "
...
engine": {
      "settings": {
        "default": {
          "autovacuum_vacuum_threshold": "65"
        }
      }
    },
...
```

## <a name="with-kubectl-command"></a>Met de opdracht kubectl

Volg de onderstaande stappen.

### <a name="1-retrieve-the-kind-of-custom-resource-definition-for-your-server-group"></a>1. het type aangepaste resource definitie voor uw server groep ophalen

Voer het volgende uit:

```console
azdata arc postgres server show -n <server group name>
```

Bijvoorbeeld:

```console
azdata arc postgres server show -n postgres01
```

Met deze opdracht wordt de specificatie van de Server groep geretourneerd waarin u de para meters ziet die u hebt ingesteld. Als er geen engine\settings-sectie is, betekent dit dat alle para meters worden uitgevoerd op de standaard waarde:

```
> {
  >"apiVersion": "arcdata.microsoft.com/v1alpha1",
  >"**kind**": "**postgresql-12**",
  >"metadata": {
    >"creationTimestamp": "2020-08-25T14:32:23Z",
    >"generation": 1,
    >"name": "postgres01",
    >"namespace": "arc",
```

Zoek in dit veld ' kind ' en stel de waarde in, bijvoorbeeld: `postgresql-12` .

### <a name="2-describe-the-kubernetes-custom-resource-corresponding-to-your-server-group"></a>2. Beschrijf de aangepaste Kubernetes-resource die overeenkomt met uw server groep 

De algemene indeling van de opdracht is:

```console
kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
```

Bijvoorbeeld:

```console
kubectl describe postgresql-12 postgres01
```

Als er aangepaste waarden zijn ingesteld voor de engine-instellingen, worden deze geretourneerd. Bijvoorbeeld:

```console
Engine:
...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
```

Als u geen aangepaste waarden voor een van de engine-instellingen hebt ingesteld, is de sectie Engine-instellingen van de resultatenset leeg, zoals:

```console
Engine:
...
    Settings:
      Default:
```

## <a name="set-custom-values-for-the-engine-settings"></a>Aangepaste waarden instellen voor de engine-instellingen

Met de onderstaande opdrachten stelt u de para meters van het coördinator knooppunt en de worker-knoop punten van uw PostgreSQL grootschalige in op dezelfde waarden. Het is nog niet mogelijk om para meters per rol in uw server groep in te stellen. Dat wil zeggen dat het nog niet mogelijk is om een bepaalde para meter te configureren voor een specifiek op het coördinator knooppunt en een andere waarde voor de worker-knoop punten.

## <a name="set-a-single-parameter"></a>Een enkele para meter instellen

```console
azdata arc server edit -n <server group name> -e <parameter name>=<parameter value>
```

Bijvoorbeeld:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=8MB
```

## <a name="set-multiple-parameters-with-a-single-command"></a>Meerdere para meters instellen met één opdracht

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>=<parameter value>, <parameter name>=<parameter value>,...'
```

Bijvoorbeeld:

```console
azdata arc postgres server edit -n postgres01 -e 'shared_buffers=8MB, max_connections=50'
```

## <a name="reset-a-parameter-to-its-default-value"></a>Een para meter opnieuw instellen op de standaard waarde

Als u een para meter wilt instellen op de standaard waarde, stelt u deze in zonder een waarde op te geven. 

Bijvoorbeeld:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=
```

## <a name="reset-all-parameters-to-their-default-values"></a>Alle para meters opnieuw instellen op de standaard waarden

```console
azdata arc postgres server edit -n <server group name> -e '' -re
```

Bijvoorbeeld:

```console
azdata arc postgres server edit -n postgres01 -e '' -re
```

## <a name="special-considerations"></a>Speciale overwegingen

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>Een para meter instellen die waarde een komma, spatie of speciaal teken bevat

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>="<parameter value>"'
```

Bijvoorbeeld:

```console
azdata arc postgres server edit -n postgres01 -e 'custom_variable_classes = "plpgsql,plperl"'
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>Een omgevings variabele in een parameter waarde door geven

De omgevings variabele moet worden verpakt in ' ' ' zodat deze niet kan worden opgelost voordat deze is ingesteld.

Bijvoorbeeld: 

```console
azdata arc postgres server edit -n postgres01 -e 'search_path = "$user"'
```



## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [uitschalen (werk knooppunten toevoegen)](scale-out-postgresql-hyperscale-server-group.md) voor uw server groep
- Meer informatie over het [schalen van omhoog of omlaag (verg Roten/verkleinen van geheugen/vcores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) uw server groep
