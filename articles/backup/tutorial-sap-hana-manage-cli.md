---
title: 'Zelf studie: een back-up van SAP HANA DB beheren met CLI'
description: In deze zelf studie leert u hoe u een back-up kunt beheren SAP HANA-data bases die worden uitgevoerd op een Azure-VM met behulp van Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 2c088c27a678a4541cbba3c4c43c9cd830c60ff0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79238739"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Zelf studie: SAP HANA-data bases beheren in een Azure-VM met behulp van Azure CLI

Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdracht regel of in scripts. Deze documentatie bevat informatie over het beheren van een back-up van SAP HANA Data Base op Azure VM-allemaal met behulp van Azure CLI. U kunt deze stappen ook uitvoeren met behulp van [de Azure Portal](https://docs.microsoft.com/azure/backup/sap-hana-db-manage).

Gebruik [Azure Cloud shell](tutorial-sap-hana-backup-cli.md) om CLI-opdrachten uit te voeren.

Aan het einde van deze zelf studie kunt u het volgende doen:

> [!div class="checklist"]
>
> * Back-up-en herstel taken bewaken
> * Nieuwe data bases beveiligen die zijn toegevoegd aan een SAP HANA-exemplaar
> * Het beleid wijzigen
> * Beveiliging stoppen
> * Beveiliging hervatten

Als u een back-up hebt gemaakt van [een SAP Hana-data base in azure met behulp van CLI](tutorial-sap-hana-backup-cli.md) om een back-up te maken van uw SAP Hana-data base, gebruikt u de volgende resources:

* een resource groep met de naam *saphanaResourceGroup*
* een kluis met de naam *saphanaVault*
* beveiligde container met de naam *VMAppContainer; Compute; saphanaResourceGroup; saphanaVM*
* back-up maken van data base/item met de naam *saphanadatabase; hxe; hxe*
* resources in de regio *westus2*

Met Azure CLI kunt u eenvoudig een SAP HANA-data base beheren die wordt uitgevoerd op een virtuele Azure-machine waarvan een back-up is gemaakt met behulp van Azure Backup. In deze zelf studie worden alle beheer bewerkingen beschreven.

## <a name="monitor-backup-and-restore-jobs"></a>Back-up-en herstel taken bewaken

Als u voltooide of actieve taken wilt bewaken (back-up of herstel), gebruikt u de cmdlet [AZ Backup Job List](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) . Met CLI kunt u ook [een taak die momenteel wordt uitgevoerd, onderbreken](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) of [wachten totdat een taak is voltooid](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait).

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

De uitvoer ziet er ongeveer als volgt uit:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Beleid wijzigen

Als u het onderliggende beleid van de SAP HANA back-upconfiguratie wilt wijzigen, gebruikt u de cmdlet [AZ backup policy set](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-set) . De para meter name in deze cmdlet verwijst naar het back-upitem waarvan u het beleid wilt wijzigen. Voor deze zelf studie vervangen we het beleid van onze SAP HANA Data Base *saphanadatabase; hxe; hxe* met een nieuw beleid *newsaphanaPolicy*. Er kunnen nieuwe beleids regels worden gemaakt met behulp van de cmdlet [AZ backup policy Create](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) .

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

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Nieuwe data bases beveiligen die zijn toegevoegd aan een SAP HANA-exemplaar

Het [registreren van een SAP Hana-exemplaar met een Recovery Services-kluis](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) detecteert automatisch alle data bases op dit exemplaar.

Als er echter later nieuwe data bases aan het SAP HANA-exemplaar worden toegevoegd, gebruikt u de cmdlet [AZ backup protectable-item Initialize](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-initialize) . Met deze cmdlet worden de toegevoegde nieuwe data bases gedetecteerd.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Gebruik vervolgens de cmdlet [AZ backup protectable-item list](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) om alle data bases weer te geven die zijn gedetecteerd op uw SAP Hana-exemplaar. In deze lijst worden echter de data bases uitgesloten waarop de back-up al is geconfigureerd. Als de data base waarvan een back-up moet worden gemaakt, wordt gedetecteerd, raadpleegt u [back-up inschakelen op SAP Hana data base](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

De nieuwe data base waarvan u een back-up wilt maken, wordt weer gegeven in deze lijst, die er als volgt uitziet:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>De beveiliging van een SAP HANA data base stoppen

U kunt het beveiligen van een SAP HANA Data Base op een aantal manieren stoppen:

* Alle toekomstige back-uptaken stoppen en alle herstelpunten verwijderen.
* Alle toekomstige back-uptaken stoppen en de herstel punten intact laten.

Als u ervoor kiest herstel punten te verlaten, houdt u deze details in acht:

* Alle herstel punten blijven intact, alle weghalen stoppen bij het stoppen van de beveiliging bij het bewaren van gegevens.
* Er worden kosten in rekening gebracht voor het beveiligde exemplaar en de verbruikte opslag.
* Als u een gegevens bron verwijdert zonder back-ups te stoppen, zullen nieuwe back-ups mislukken.

Laten we eens kijken naar de manieren om de beveiliging te stoppen.

### <a name="stop-protection-with-retain-data"></a>Bescherming stoppen met gegevensbehoud

Als u de beveiliging met behoud van gegevens wilt stoppen, gebruikt u de cmdlet [AZ Backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) .

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

Als u de status van deze bewerking wilt controleren, gebruikt u de [opdracht AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retain-data"></a>Beveiliging stoppen zonder gegevens te bewaren

Als u de beveiliging wilt stoppen zonder gegevens te bewaren, gebruikt u de cmdlet [AZ Backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) .

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

Als u de status van deze bewerking wilt controleren, gebruikt u de [opdracht AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="resume-protection"></a>Beveiliging hervatten

Wanneer u de beveiliging voor de SAP HANA-data base stopt met het bewaren van gegevens, kunt u de beveiliging later hervatten. Als u de gegevens van de back-up niet behoudt, kunt u de beveiliging niet hervatten.

Als u de beveiliging wilt hervatten, gebruikt u de cmdlet [AZ Backup Protection resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) .

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

Als u de status van deze bewerking wilt controleren, gebruikt u de [opdracht AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Volgende stappen

* Voor informatie over het maken van een back-up van een SAP HANA-data base die wordt uitgevoerd op een Azure VM met behulp van de Azure Portal raadpleegt u [Backup SAP Hana data bases op virtuele](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

* Raadpleeg voor meer informatie over het beheren van een back-up SAP HANA data base die wordt uitgevoerd op een Azure-VM met behulp van de Azure Portal een back-up [van SAP Hana-data bases beheren op Azure VM](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
