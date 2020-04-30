---
title: Zelf studie-SAP HANA DB herstellen op Azure met behulp van CLI
description: In deze zelf studie leert u hoe u SAP HANA-data bases die worden uitgevoerd op een virtuele machine van Azure, kunt herstellen vanuit een Azure Backup Recovery Services kluis met behulp van Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75472070"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Zelf studie: SAP HANA data bases herstellen in een Azure-VM met behulp van Azure CLI

Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdracht regel of in scripts. Deze documentatie bevat informatie over het herstellen van een back-up van SAP HANA Data Base op een Azure-VM met behulp van Azure CLI. U kunt deze stappen ook uitvoeren met behulp van de [Azure Portal](https://docs.microsoft.com/azure/backup/sap-hana-db-restore).

Gebruik [Azure Cloud shell](tutorial-sap-hana-backup-cli.md) om CLI-opdrachten uit te voeren.

Aan het einde van deze zelf studie kunt u het volgende doen:

> [!div class="checklist"]
>
> * Herstel punten weer geven voor een back-up van de data base
> * Een database herstellen

In deze zelf studie wordt ervan uitgegaan dat u een SAP HANA-Data Base hebt die wordt uitgevoerd op een Azure-VM met een back-up van Azure Backup. Als u een back-up hebt gemaakt van [een SAP Hana-data base in azure met behulp van CLI](tutorial-sap-hana-backup-cli.md) om een back-up te maken van uw SAP Hana-data base, gebruikt u de volgende resources:

* een resource groep met de naam *saphanaResourceGroup*
* een kluis met de naam *saphanaVault*
* beveiligde container met de naam *VMAppContainer; Compute; saphanaResourceGroup; saphanaVM*
* back-up maken van data base/item met de naam *saphanadatabase; hxe; hxe*
* resources in de regio *westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Herstel punten weer geven voor een back-up van de data base

Als u de lijst met alle herstel punten voor een Data Base wilt weer geven, gebruikt u de cmdlet [AZ backup Recovery Point List](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) als volgt:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

De lijst met herstel punten ziet er als volgt uit:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Zoals u kunt zien, bevat de bovenstaande lijst drie herstel punten: één voor elke volledige, differentiële en logboek back-up.

>[!NOTE]
>U kunt ook de begin-en eind punten van elke niet-verbroken logboek back-upketen weer geven met behulp van de cmdlet [AZ backup Recovery Point Show-Log-keten](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) .

## <a name="prerequisites-to-restore-a-database"></a>Vereisten voor het herstellen van een Data Base

Zorg ervoor dat aan de volgende vereisten wordt voldaan voordat u een Data Base herstelt:

* U kunt de data base alleen herstellen naar een SAP HANA-exemplaar dat zich in dezelfde regio bevindt
* Het doel exemplaar moet zijn geregistreerd bij dezelfde kluis als de bron
* Azure Backup kan niet twee verschillende SAP HANA exemplaren op dezelfde VM identificeren. Daarom is het terugzetten van gegevens van het ene naar het andere exemplaar op dezelfde VM niet mogelijk.

## <a name="restore-a-database"></a>Een database herstellen

Azure Backup kunt SAP HANA-data bases die worden uitgevoerd op virtuele Azure-machines als volgt herstellen:

* Herstel naar een specifieke datum of tijd (naar de tweede) met behulp van logboek back-ups. Azure Backup bepaalt automatisch de juiste volledige, differentiële back-ups en de keten van logboek back-ups die nodig zijn om te herstellen op basis van de geselecteerde tijd.
* Herstel naar een specifieke volledige of een differentiële back-up om naar een specifiek herstel punt te herstellen.

Als u een Data Base wilt herstellen, gebruikt u de [AZ herstellen Restore-azurewl-](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet, waarvoor een herstel configuratie object als een van de invoer is vereist. Dit object kan worden gegenereerd met behulp van de recoveryconfig-cmdlet voor het [weer geven van de methode AZ backup](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) . Het herstel configuratie object bevat alle Details voor het uitvoeren van een herstel bewerking. Een van de twee is de herstel modus – **OriginalWorkloadRestore** of **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** : herstel de gegevens naar hetzelfde SAP Hana-exemplaar als de oorspronkelijke bron. Met deze optie wordt de oorspronkelijke data base overschreven. <br>
> **AlternateWorkloadRestore** : herstel de Data Base op een andere locatie en behoud de oorspronkelijke bron database.

## <a name="restore-to-alternate-location"></a>Herstellen naar alternatieve locatie

Als u een Data Base op een andere locatie wilt herstellen, gebruikt u **AlternateWorkloadRestore** als herstel modus. Vervolgens moet u het herstel punt kiezen. Dit kan een eerdere punt in de tijd zijn of een van de vorige herstel punten.

In deze zelf studie gaat u naar een eerder herstel punt herstellen. [Bekijk de lijst met herstel punten](#view-restore-points-for-a-backed-up-database) voor de data base en kies het punt waarnaar u wilt herstellen. In deze zelf studie wordt het herstel punt gebruikt met de naam *7660777527047692711*.

Met behulp van de bovenstaande herstel punt naam en de herstel modus kunt u het herstel configuratie object maken met behulp van de [AZ backup recoveryconfig show-](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) cmdlet. Laten we eens kijken naar de overige para meters in deze cmdlet:

* **--doel item-naam** Dit is de naam die wordt gebruikt door de herstelde data base. In dit geval hebben we de naam *restored_database*gebruikt.
* **--doel-server-naam** Dit is de naam van een SAP HANA server die is geregistreerd bij een Recovery Services-kluis en zich in dezelfde regio bevindt als de data base die moet worden hersteld. Voor deze zelf studie herstelt u de Data Base op dezelfde SAP HANA server die we hebben beveiligd, met de naam *hxehost*.
* **--doel-server-type** Voor het terugzetten van SAP HANA-data bases moet **SapHanaDatabase** worden gebruikt.

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

De reactie op de bovenstaande query is een herstel configuratie object dat er ongeveer als volgt uitziet:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Als u de Data Base wilt herstellen, voert u de cmdlet [AZ Restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) uit. Als u deze opdracht wilt gebruiken, wordt de bovenstaande JSON-uitvoer ingevoerd die wordt opgeslagen in een bestand met de naam *recoveryconfig. json*.

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

Met het antwoord krijgt u de taak naam. Deze taak naam kan worden gebruikt om de taak status bij te houden met de cmdlet [AZ back-uptaak show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="restore-and-overwrite"></a>Herstellen en overschrijven

We gebruiken **OrignialWorkloadRestore** als herstel modus om naar de oorspronkelijke locatie te herstellen. Vervolgens moet u het herstel punt kiezen. Dit kan een eerdere punt in de tijd zijn of een van de vorige herstel punten.

Voor deze zelf studie kiezen we het vorige tijdstip van ' 28-11-2019-09:53:00 ' om terug te zetten naar. U kunt dit herstel punt opgeven in de volgende indelingen: dd-mm-jjjj, dd-mm-jjjj-uu: mm: SS. Als u een geldig punt-in-time wilt kiezen om te herstellen naar, gebruikt u de cmdlet [AZ backup Recovery Point Show-Log-keten](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) , waarin de intervallen van niet-verbroken logboek keten back-ups worden weer gegeven.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

De reactie op de bovenstaande query is een herstel configuratie object dat er als volgt uitziet:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Als u de Data Base wilt herstellen, voert u de cmdlet [AZ Restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) uit. Als u deze opdracht wilt gebruiken, wordt de bovenstaande JSON-uitvoer ingevoerd die wordt opgeslagen in een bestand met de naam *recoveryconfig. json*.

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

Met het antwoord krijgt u de taak naam. Deze taak naam kan worden gebruikt om de taak status bij te houden met de cmdlet [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="next-steps"></a>Volgende stappen

* Voor informatie over het beheren van SAP HANA data bases waarvan een back-up is gemaakt met behulp van Azure CLI, gaat u verder met de zelf studie [een SAP Hana-data base in azure VM beheren met CLI](tutorial-sap-hana-backup-cli.md)

* Raadpleeg voor meer informatie over het herstellen van een SAP HANA-data base die wordt uitgevoerd in de Azure-VM met behulp van de Azure Portal [een SAP Hana-data base herstellen op virtuele machines in azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
