---
title: 'Zelfstudie: Back-up van SAP HANA-database beheren met CLI'
description: In deze zelfstudie leert u hoe u met behulp van Azure CLI back-ups beheert van SAP HANA-databases die op een Azure-VM worden uitgevoerd.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 22ff95fe5261a839927aa6ad8123ba370710f178
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323087"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Zelfstudie: SAP HANA-databases op een Azure-VM beheren met Azure CLI

Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of door middel van scripts. In deze documentatie vindt u informatie over het beheren van een back-up van een SAP HANA-database op een Azure-VM, uitsluitend met behulp van Azure CLI. U kunt deze stappen overigens ook uitvoeren in de [Azure-portal](./sap-hana-db-manage.md).

Gebruik [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) voor het uitvoeren van CLI-opdrachten.

Aan het einde van deze zelfstudie kunt u:

> [!div class="checklist"]
>
> * Taken voor back-up en herstel controleren
> * Nieuwe databases beveiligen die worden toegevoegd aan een SAP HANA-exemplaar
> * Het beleid wijzigen
> * Beveiliging stoppen
> * Beveiliging hervatten

Als u [Een back-up maken van een SAP HANA-database in Azure met behulp van CLI](tutorial-sap-hana-backup-cli.md) hebt gebruikt om een back-up te maken van uw SAP HANA-database, gebruikt u de volgende resources:

* een resourcegroep met de naam *saphanaResourceGroup*;
* een kluis met de naam *saphanaVault*
* een beveiligde container met de naam *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*
* een database/item waarvan een back-up is gemaakt met de naam *saphanadatabase; hxe; hxe*
* resources in de locatie *westus2*

Met Azure CLI kunt u eenvoudig een SAP HANA-database beheren die wordt uitgevoerd op een Azure-VM waarvan een back-up is gemaakt met Azure Backup. In deze zelfstudie worden de verschillende beheerbewerkingen beschreven.

## <a name="monitor-backup-and-restore-jobs"></a>Taken voor back-up en herstel controleren

Als u voltooide of momenteel actieve taken wilt controleren (back-up of herstel), gebruikt u de cmdlet [az backup job list](/cli/azure/backup/job#az-backup-job-list). Met CLI kunt u ook [een taak onderbreken die momenteel wordt uitgevoerd](/cli/azure/backup/job#az-backup-job-stop) of [wachten tot een taak is voltooid](/cli/azure/backup/job#az-backup-job-wait).

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

De uitvoer ziet er ongeveer uit zoals in dit voorbeeld:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Beleid wijzigen

Als u het onderliggende beleid van een SAP HANA back-upconfiguratie wilt wijzigen, gebruikt u de cmdlet [az backup policy set](/cli/azure/backup/policy#az-backup-policy-set). De parameter name in deze cmdlet verwijst naar het back-upitem waarvan u het beleid wilt wijzigen. Voor deze zelfstudie vervangen we het beleid van onze SAP HANA-database *saphanadatabase;hxe;hxe* door het nieuwe beleid *newsaphanaPolicy*. U kunt een nieuw beleid maken met behulp van de cmdlet [az backup policy create](/cli/azure/backup/policy#az-backup-policy-create).

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

De uitvoer moet er als volgt uitzien:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Nieuwe databases beveiligen die worden toegevoegd aan een SAP HANA-exemplaar

Wanneer u [een SAP HANA-exemplaar registreert bij een Recovery Services-kluis](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance), worden alle databases op dit exemplaar automatisch gedetecteerd.

Als er echter later nieuwe databases worden toegevoegd aan het SAP HANA-exemplaar, gebruikt u de cmdlet [az backup protectable-item initialize](/cli/azure/backup/protectable-item#az-backup-protectable-item-initialize). Met deze cmdlet worden de nieuw toegevoegde databases gedetecteerd.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Gebruik vervolgens de cmdlet [az backup protectable-item list](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) om een lijst weer te geven met alle databases die zijn gedetecteerd op uw SAP HANA-exemplaar. Deze lijst bevat geen databases waarvoor al een back-up is geconfigureerd. Als de database waarvan een back-up moet worden gemaakt, is gedetecteerd, raadpleegt u [Back-up inschakelen voor een SAP HANA-database](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

De nieuwe database waarvan u een back-up wilt maken, staat in deze lijst. Deze ziet er als volgt uit:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Beveiliging van een SAP HANA-database stoppen

U kunt de beveiliging van een SAP HANA-database op een aantal manieren stoppen:

* Alle toekomstige back-uptaken stoppen en alle herstelpunten verwijderen.
* Alle toekomstige back-uptaken stoppen en de herstelpunten intact laten.

Houd rekening met het volgende als u ervoor kiest de herstelpunten intact te laten:

* Alle herstelpunten blijven voor onbepaalde tijd intact en alle verwijderbewerkingen zullen stoppen bij het stoppen van de beveiliging met behoud van gegevens.
* Er worden kosten in rekening gebracht voor het beveiligde exemplaar en de verbruikte opslag.
* Als u een gegevensbron verwijdert zonder back-ups te stoppen, mislukken nieuwe back-ups.

Laten we de verschillende manieren om de beveiliging te stoppen eens wat nader bestuderen.

### <a name="stop-protection-with-retain-data"></a>Bescherming stoppen met gegevensbehoud

Als u de beveiliging met behoud van gegevens wilt stoppen, gebruikt u de cmdlet [az backup protection disable](/cli/azure/backup/protection#az-backup-protection-disable).

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

De uitvoer moet er als volgt uitzien:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Als u de status van deze bewerking wilt controleren, gebruikt u de cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

### <a name="stop-protection-without-retain-data"></a>Beveiliging stoppen zonder gegevensbehoud

Als u de beveiliging wilt stoppen zonder gegevens te behouden, gebruikt u de cmdlet [az backup protection disable](/cli/azure/backup/protection#az-backup-protection-disable).

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

De uitvoer moet er als volgt uitzien:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Als u de status van deze bewerking wilt controleren, gebruikt u de cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

## <a name="resume-protection"></a>Beveiliging hervatten

Wanneer u de beveiliging voor de SAP HANA-database stopt met behoud van gegevens, kunt u de beveiliging later hervatten. Als u de gegevens van de back-up niet bewaart, kunt u de beveiliging niet hervatten.

Gebruik de cmdlet [az backup protection resume](/cli/azure/backup/protection#az-backup-protection-resume) om de beveiliging te hervatten.

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

De uitvoer moet er als volgt uitzien:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Als u de status van deze bewerking wilt controleren, gebruikt u de cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

## <a name="next-steps"></a>Volgende stappen

* Als u wilt weten hoe u met de Azure-portal een back-up maakt van een SAP HANA-database die op een Azure-VM wordt uitgevoerd, raadpleegt u [Een back-up maken van SAP HANA-databases op Azure-VM's](./backup-azure-sap-hana-database.md).

* Als u wilt weten hoe u met de Azure-portal een back-up van een SAP HANA-database beheert die op een Azure-VM wordt uitgevoerd, raadpleegt u [Back-up beheren van SAP HANA-database op Azure-VM's](./sap-hana-db-manage.md).
