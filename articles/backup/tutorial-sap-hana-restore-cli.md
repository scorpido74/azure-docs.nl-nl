---
title: Zelfstudie - SAP HANA DB-herstel op Azure met CLI
description: In deze zelfstudie leert u hoe SAP HANA-databases die op een Azure VM worden uitgevoerd, worden hersteld vanuit een Azure Backup Recovery Services-kluis met Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75472070"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Zelfstudie: SAP HANA-databases herstellen in een Azure VM met Azure CLI

Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of via scripts. In deze documentatie wordt beschreven hoe u een back-up SAP HANA-database op een Azure VM herstellen - met Azure CLI. U deze stappen ook uitvoeren met de [Azure-portal.](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)

Gebruik [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) om CLI-opdrachten uit te voeren.

Tegen het einde van deze tutorial zul je in staat zijn om:

> [!div class="checklist"]
>
> * Herstelpunten weergeven voor een back-updatabase
> * Een database herstellen

In deze zelfstudie wordt ervan uitgegaan dat er een SAP HANA-database is uitgevoerd op Azure VM waarvan een back-up wordt gemaakt met Azure Backup. Als u een back-up hebt gemaakt [van een SAP HANA-database in Azure met CLI](tutorial-sap-hana-backup-cli.md) om een back-up te maken van uw SAP HANA-database, gebruikt u de volgende bronnen:

* een resourcegroep met de naam *saphanaResourceGroup*
* een kluis met de naam *saphanaVault*
* beveiligde container met de naam *VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*
* back-up database/item met de naam *saphanadatabase;hxe;hxe*
* middelen in de *regio Westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Herstelpunten weergeven voor een back-updatabase

Als u de lijst met alle herstelpunten voor een database wilt weergeven, gebruikt u de cmdlet van het [AZ-back-upherstelpunt](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) als volgt:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

De lijst met herstelpunten ziet er als volgt uit:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Zoals u zien, bevat de bovenstaande lijst drie herstelpunten: één voor volledige, differentiële en logboekback-up.

>[!NOTE]
>U ook de begin- en eindpunten van elke ongebroken logback-upketen bekijken met behulp van de cmdlet van de [AZ-back-up-back-up-tape.](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain)

## <a name="prerequisites-to-restore-a-database"></a>Vereisten voor het herstellen van een database

Controleer of aan de volgende voorwaarden is voldaan voordat u een database herstelt:

* U de database alleen herstellen naar een SAP HANA-exemplaar dat zich in dezelfde regio bevindt
* De doelinstantie moet worden geregistreerd bij dezelfde kluis als de bron
* Azure Backup kan niet twee verschillende SAP HANA-exemplaren op dezelfde VM identificeren. Daarom is het niet mogelijk om gegevens van de ene instantie naar de andere op dezelfde vm te herstellen.

## <a name="restore-a-database"></a>Een database herstellen

Azure Backup kan SAP HANA-databases die op Azure VM's worden uitgevoerd als volgt herstellen:

* Herstellen naar een specifieke datum of tijd (naar de tweede) met behulp van logboekback-ups. Azure Backup bepaalt automatisch de juiste volledige, differentiële back-ups en de keten van logboekback-ups die moeten worden hersteld op basis van de geselecteerde tijd.
* Herstellen naar een specifieke volledige of differentiële back-up om te herstellen naar een specifiek herstelpunt.

Als u een database wilt herstellen, gebruikt u de cmdlet [az restore restore-azurewl,](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) waarvoor een herstelobject als een van de ingangen vereist is. Dit object kan worden gegenereerd met behulp van de [az back-up recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) cmdlet. Het herstelconfig-object bevat alle details om een herstel uit te voeren. Een van hen is de herstelmodus - **OriginalWorkloadRestore** of **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** - De gegevens herstellen naar dezelfde SAP HANA-instantie als de oorspronkelijke bron. Met deze optie wordt de oorspronkelijke database overschrijft. <br>
> **AlternateWorkloadRestore** - De database herstellen naar een alternatieve locatie en de oorspronkelijke brondatabase behouden.

## <a name="restore-to-alternate-location"></a>Herstellen naar alternatieve locatie

Als u een database wilt herstellen naar een alternatieve locatie, gebruikt u **AlternateWorkloadRestore** als herstelmodus. U moet vervolgens het herstelpunt kiezen, dat een eerdere point-in-time of een van de vorige herstelpunten kan zijn.

In deze zelfstudie herstelt u naar een eerder herstelpunt. [Bekijk de lijst met herstelpunten](#view-restore-points-for-a-backed-up-database) voor de database en kies het punt waarnaar u wilt herstellen. Deze zelfstudie gebruikt het herstelpunt met de naam *7660777527047692711*.

Met behulp van de bovenstaande naam van het herstelpunt en de herstelmodus maken we het herstelobject met behulp van de cmdlet van de [AZ-back-uprecoveryconfig.](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) Laten we eens kijken naar wat elk van de resterende parameters in deze cmdlet betekenen:

* **--doel-item-naam** Dit is de naam die de herstelde database zal gebruiken. In dit geval gebruikten we de naam *restored_database.*
* **--doel-server-naam** Dit is de naam van een SAP HANA-server die met succes is geregistreerd bij een kluis van herstelservices en zich in dezelfde regio bevindt als de database die moet worden hersteld. Voor deze zelfstudie herstellen we de database naar dezelfde SAP HANA-server die we hebben beschermd, *hxehost*genaamd.
* **--doel-servertype** Voor het herstellen van SAP HANA-databases moet **SapHanaDatabase** worden gebruikt.

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

Het antwoord op de bovenstaande query is een herstelconfig-object dat er ongeveer als volgt uitziet:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Nu, om de database te herstellen draaien de [AZ restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet. Als u deze opdracht wilt gebruiken, voeren we de bovenstaande json-uitvoer in die is opgeslagen in een bestand met de naam *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

De uitvoer ziet er als volgt uit:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Het antwoord geeft je de naam van de baan. Deze taaknaam kan worden gebruikt om de taakstatus bij te houden met behulp van de CMDLet [van de AZ-back-upjobshow.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="restore-and-overwrite"></a>Herstellen en overschrijven

Om te herstellen naar de oorspronkelijke locatie, gebruiken we **OrignialWorkloadRestore** als herstelmodus. U moet vervolgens het herstelpunt kiezen, dat een eerdere point-in-time of een van de vorige herstelpunten kan zijn.

Voor deze zelfstudie kiezen we de vorige point-in-time "28-11-2019-09:53:00" om naar te herstellen. U dit herstelpunt in de volgende formaten: dd-mm-yyyy, dd-mm-yyyy-hh:mm:ss. Als u een geldige point-in-time wilt kiezen om naar te herstellen, gebruikt u de cmdlet van het [AZ-back-upherstelpunt show-log-chain,](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) waarin de intervallen van ongebroken logboekketenback-ups worden weergegeven.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

Het antwoord op de bovenstaande query is een herstelobject dat er als volgt uitziet:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Nu, om de database te herstellen draaien de [AZ restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet. Als u deze opdracht wilt gebruiken, voeren we de bovenstaande json-uitvoer in die is opgeslagen in een bestand met de naam *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

De uitvoer ziet er als volgt uit:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Het antwoord geeft je de naam van de baan. Deze taaknaam kan worden gebruikt om de taakstatus bij te houden met behulp van de cmdlet van de [AZ-back-upfunctie.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="next-steps"></a>Volgende stappen

* Ga voor meer informatie over het beheren van SAP HANA-databases waarvan een back-up wordt gemaakt met Azure CLI, door naar de zelfstudie [Een SAP HANA-database beheren in Azure VM met CLI](tutorial-sap-hana-backup-cli.md)

* Voor meer informatie over het herstellen van een SAP HANA-database die wordt uitgevoerd in Azure VM met behulp van de Azure-portal, raadpleegt u [Een SAP HANA-databases herstellen in Azure VM's](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
