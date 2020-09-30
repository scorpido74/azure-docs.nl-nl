---
title: Back-up en herstel voor Azure Database for PostgreSQL Hyperscale-servergroepen
description: Back-up en herstel voor Azure Database for PostgreSQL Hyperscale-servergroepen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: dde4db7f3eb476b7645e910504e48fea8bb6df0c
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569713"
---
# <a name="backup-and-restore-for-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Back-ups maken en herstellen voor Azure Arc ingeschakelde PostgreSQL grootschalige-Server groepen

U kunt een volledige back-up maken/herstellen van uw PostgreSQL grootschalige-Server groep voor Azure Arc. Wanneer u dit doet, wordt er een back-up gemaakt van de hele set data bases op alle knoop punten van de PostgreSQL grootschalige-Server groep van Azure.
Als u een back-up wilt maken en herstellen, moet u ervoor zorgen dat er een back-upopslag klasse is geconfigureerd voor uw server groep. Voor nu moet u een back-upopslag klasse opgeven op het moment dat u de Server groep maakt. Het is nog niet mogelijk om uw server groep te configureren voor het gebruik van een back-upopslag klasse nadat deze is gemaakt.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!CAUTION]
> Preview biedt geen ondersteuning voor back-up/herstel voor versie 11 van de post gres-engine. Het ondersteunt alleen Backup/Restore voor post gres versie 12.

## <a name="verify-configuration"></a>Configuratie controleren

Controleer eerst of de bestaande server groep is geconfigureerd voor het gebruik van de back-upopslag klasse.

Voer de volgende opdracht uit nadat u de naam van de Server groep hebt ingesteld:
```console
 azdata arc postgres server show -n postgres01
```
Bekijk de sectie opslag van de uitvoer:
```console
...
"storage": {
      "backups": {
        "className": "local-storage"
      },
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
...
```
Als de naam van een opslag klasse wordt weer gegeven in de sectie ' back-ups ' van de uitvoer van die opdracht, betekent dit dat uw server groep is geconfigureerd voor het gebruik van een back-upopslag klasse en dat u klaar bent om back-ups te maken en op te slaan. Als u geen sectie back-ups ziet, moet u de Server groep verwijderen en opnieuw maken om de back-upopslag klasse te configureren. Op dit moment is het nog niet mogelijk om een back-upopslag klasse te configureren nadat de Server groep is gemaakt.

>[!IMPORTANT]
>Als uw server groep al is geconfigureerd voor het gebruik van een back-upopslag klasse, slaat u de volgende stap over en gaat u rechtstreeks naar stap ' hand matige volledige back-up maken '.

## <a name="create-a-server-group"></a>Een server groep maken 

Maak vervolgens een server groep die is geconfigureerd voor back-up/herstel.

Als u back-ups wilt maken en deze wilt herstellen, moet u een server die is geconfigureerd met een opslag klasse.

Voer de volgende opdracht uit om een lijst op te halen van de opslag klassen die beschikbaar zijn op uw Kubernetes-cluster:

```console
kubectl get sc
```

<!--The general format of create server group command is documented [here](create-postgresql-instances.md)-->

```console
azdata arc postgres server create -n <name> --workers 2 --storage-class-backups <storage class name> [--storage-class-data <storage class name>] [--storage-class-logs <storage class name>]
```

Als u bijvoorbeeld een eenvoudige omgeving hebt gemaakt op basis van kubeadm:
```console
azdata arc postgres server create -n postgres01 --workers 2 --storage-class-backups local-storage
```

## <a name="take-manual-full-backup"></a>Hand matige volledige back-up maken

Maak vervolgens een hand matige volledige back-up.

Voer de volgende opdracht uit om een volledige back-up te maken van de volledige gegevens en logboek mappen van uw server groep:

```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Waar:
- __naam__ geeft de naam van een back-up aan
- __Server naam__ geeft een server groep aan
- __no-wait__ geeft aan dat de opdracht regel niet wacht totdat de back-up is voltooid, zodat u dit opdracht regel venster kunt blijven gebruiken

>**Opmerking**: de opdracht waarmee u de back-ups kunt weer geven die beschikbaar zijn om te worden hersteld, is nog niet zichtbaar, de datum en tijd waarop de back-up is gemaakt. Daarom kunt u het beste een naam aan de back-up geven (met de para meter-name) die de datum/tijd-informatie bevat.

Met deze opdracht wordt een gedistribueerde volledige back-up gecoördineerd op alle knoop punten die de PostgreSQL grootschalige-Server groep voor Azure Arc ondersteunen. Met andere woorden, Hiermee maakt u een back-up van alle gegevens in uw coördinator en worker-knoop punten.

Bijvoorbeeld:
```console
azdata arc postgres backup create --name MyBackup_Aug31_0730amPST --server-name postgres01
```

Wanneer de back-up is voltooid, worden de ID, de naam en de status van de back-up geretourneerd. Bijvoorbeeld:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "name": "MyBackup_Aug31_0730amPS",
  "state": "Done"
}
```

> [!NOTE]
> Het is nog niet mogelijk om:
> - Automatische back-ups plannen
> - De voortgang van een back-up weer geven terwijl deze wordt uitgevoerd

## <a name="list-backups"></a>Back-ups weergeven

De back-ups weer geven die beschikbaar zijn voor herstel.

Voer de volgende opdracht uit om de back-ups weer te geven die beschikbaar zijn voor herstel:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Bijvoorbeeld:
```console
azdata arc postgres backup list --server-name postgres01
```

Er wordt een uitvoer geretourneerd zoals:
```console
ID                                Name                      State    Timestamp
--------------------------------  ------------------------  -------  ------------------------------
d134f51aa87f4044b5fb07cf95cf797f  MyBackup_Aug31_0730amPST  Done     2020-08-31 14:30:00:00+00:00
```

Tijds tempel geeft het punt aan van de tijd waarop de back-up is gemaakt.

## <a name="restore-a-backup"></a>Een back-up terugzetten

Voer de volgende opdracht uit om de back-up van een hele server groep te herstellen:

```console
azdata arc postgres backup restore --server-name <server group name> --backup-id <backup id>
```

Waar:
- __Backup-id__ is de id van de back-up die wordt weer gegeven in de back-upopdracht van de lijst (zie stap 3).
Hiermee coördineert u een gedistribueerd volledig herstel op alle knoop punten die de PostgreSQL grootschalige-Server groep van Azure Arc ondersteunen. Met andere woorden, Hiermee worden alle gegevens in uw coördinator en worker-knoop punten hersteld.

Bijvoorbeeld:
```console
azdata arc postgres backup restore --server-name postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Wanneer de herstel bewerking is voltooid, wordt de uitvoer als volgt geretourneerd naar de opdracht regel:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```
> [!NOTE]
> Het is nog niet mogelijk om:
> - Een back-up herstellen door de naam ervan aan te geven
> - Een server groep herstellen onder een andere naam of op een andere server groep
> - De voortgang van een herstel bewerking weer geven

## <a name="delete-backups"></a>Back-ups verwijderen
Het bewaren van back-ups kan niet worden ingesteld in de preview-versie. U kunt back-ups die u niet nodig hebt, echter hand matig verwijderen.
De algemene opdracht voor het verwijderen van back-ups is:
```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```
Hierbij
- `--server-name` is de naam van de Server groep waaruit de gebruiker een back-up wil verwijderen
- `--name` is de naam van de back-up die u wilt verwijderen
- `-id`is de ID van de back-up die u wilt verwijderen

> [!NOTE]
> `--name` en `-id` sluiten elkaar wederzijds uit.

U kunt de naam en de ID van uw back-ups ophalen door de opdracht lijst back-up uit te voeren, zoals wordt beschreven in de vorige alinea.

U kunt bijvoorbeeld de volgende back-ups weer gegeven:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
5b0481dfc1c94b4cac79dd56a1bb21f4  MyBackup091720200110am  Done
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```
en u het eerste wilt verwijderen, voert u de volgende opdracht uit:
```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```
Als u de back-ups op dat moment zou vermelden, krijgt u de volgende uitvoer:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```

Voor meer informatie over de opdracht verwijderen voert u het volgende uit:
```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [uitschalen (werk knooppunten toevoegen)](scale-out-postgresql-hyperscale-server-group.md) voor uw server groep
- Meer informatie over het [schalen van omhoog of omlaag (verg Roten/verkleinen van geheugen/vcores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) uw server groep
