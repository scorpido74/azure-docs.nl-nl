---
title: 'Zelfstudie: Beheer back-up SAP HANA DB met CLI'
description: In deze zelfstudie leert u hoe u back-up-SAP HANA-databases beheert die op een Azure VM worden uitgevoerd met Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 2c088c27a678a4541cbba3c4c43c9cd830c60ff0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238739"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Zelfstudie: SAP HANA-databases beheren in een Azure VM met Azure CLI

Azure CLI wordt gebruikt voor het maken en beheren van Azure-bronnen vanuit de opdrachtregel of via scripts. In deze documentatie wordt beschreven hoe u een back-up SAP HANA-database op Azure VM beheert - allemaal met Azure CLI. U deze stappen ook uitvoeren met [de Azure-portal.](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)

Gebruik [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) om CLI-opdrachten uit te voeren.

Tegen het einde van deze tutorial, zult u in staat zijn om:

> [!div class="checklist"]
>
> * Back-ups controleren en taken herstellen
> * Nieuwe databases beveiligen die zijn toegevoegd aan een SAP HANA-exemplaar
> * Het beleid wijzigen
> * Beveiliging stoppen
> * Hervatting van de beveiliging

Als u een back-up hebt gemaakt [van een SAP HANA-database in Azure met CLI](tutorial-sap-hana-backup-cli.md) om een back-up te maken van uw SAP HANA-database, gebruikt u de volgende bronnen:

* een resourcegroep met de naam *saphanaResourceGroup*
* een kluis met de naam *saphanaVault*
* beveiligde container met de naam *VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*
* back-up database/item met de naam *saphanadatabase;hxe;hxe*
* middelen in de *regio Westus2*

Azure CLI maakt het eenvoudig om een SAP HANA-database te beheren die wordt uitgevoerd op een Azure VM waarvan een back-up wordt gemaakt met Azure Backup. In deze zelfstudie wordt elk beheerprogramma beschreven.

## <a name="monitor-backup-and-restore-jobs"></a>Back-ups controleren en taken herstellen

Gebruik de cmdlet van de [AZ-back-uplijst](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) om voltooide of momenteel lopende taken te controleren (back-up of herstel). Met CLI u ook [een momenteel lopende taak opschorten](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) of wachten tot een taak is [voltooid.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

De output ziet er ongeveer als volgt uit:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Beleid wijzigen

Als u het beleid wilt wijzigen dat ten grondslag ligt aan de SAP HANA-back-upconfiguratie, gebruikt u de cmdlet van het [AZ-back-upbeleid.](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-set) De parameter naam in deze cmdlet verwijst naar het back-upitem waarvan we het beleid willen wijzigen. Voor deze tutorial zullen we het beleid van onze SAP HANA database *saphanadatabase;hxe;hxe* vervangen door een nieuw beleid *newsaphanaPolicy*. Er kunnen nieuwe beleidsregels worden gemaakt met behulp van het [az-back-upbeleid om](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet te maken.

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

De uitvoer zal er ongeveer als volgt uitzien:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Nieuwe databases beveiligen die zijn toegevoegd aan een SAP HANA-exemplaar

[Als u een SAP HANA-exemplaar registreert bij een kluis van herstelservices,](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) worden automatisch alle databases op dit exemplaar ontdekt.

In gevallen waarin later nieuwe databases aan het SAP HANA-exemplaar worden toegevoegd, gebruikt u echter de [az-back-upbeschermbare-item initialiseren](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-initialize) cmdlet. Deze cmdlet detecteert de nieuwe databases toegevoegd.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Gebruik vervolgens de [cmdlet van az-back-upprotectable-item](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) om alle databases op uw SAP HANA-exemplaar weer te geven. Deze lijst sluit echter de databases uit waarop back-upal is geconfigureerd. Zodra de database die moet worden ontdekt, raadpleegt u [Back-up inschakelen op SAP HANA-database.](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database)

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

De nieuwe database die u wilt een back-up zal worden weergegeven in deze lijst, die eruit ziet als volgt:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Stop de beveiliging van een SAP HANA-database

U op een aantal manieren stoppen met het beschermen van een SAP HANA-database:

* Alle toekomstige back-uptaken stoppen en alle herstelpunten verwijderen.
* Stop alle toekomstige back-uptaken en laat de herstelpunten intact.

Als u ervoor kiest om herstelpunten achter te laten, houdt u rekening met deze gegevens:

* Alle herstelpunten blijven voor altijd intact, alle snoeien stopt bij stopbescherming met bewarende gegevens.
* Er worden kosten in rekening gebracht voor de beveiligde instantie en de verbruikte opslag.
* Als u een gegevensbron verwijdert zonder back-ups te stoppen, mislukken nieuwe back-ups.

Laten we eens kijken naar elk van de manieren om bescherming te stoppen in meer detail.

### <a name="stop-protection-with-retain-data"></a>Bescherming stoppen met gegevensbehoud

Gebruik de [az-back-upbeveiliging](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) om de bescherming met gegevens te bewaren, om de bescherming met gegevens te bewaren.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

De uitvoer zal er ongeveer als volgt uitzien:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Gebruik de [az-back-upfunctie](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet om de status van deze bewerking te controleren.

### <a name="stop-protection-without-retain-data"></a>Stop bescherming zonder gegevens te bewaren

Gebruik de [az-back-upbeveiliging](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) om de beveiliging van az te stoppen zonder gegevens te bewaren.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

De uitvoer zal er ongeveer als volgt uitzien:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Gebruik de [az-back-upfunctie](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet om de status van deze bewerking te controleren.

## <a name="resume-protection"></a>Hervatting van de beveiliging

Wanneer u de beveiliging van de SAP HANA-database met bewaardegegevens stopt, u de beveiliging later hervatten. Als u de back-upgegevens niet bewaart, u de beveiliging niet hervatten.

Gebruik de cmdlet van de [AZ-back-upbeveiliging](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) om de bescherming te hervatten.

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

De uitvoer zal er ongeveer als volgt uitzien:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Gebruik de [az-back-upfunctie](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet om de status van deze bewerking te controleren.

## <a name="next-steps"></a>Volgende stappen

* Voor meer informatie over het maken van een back-up van een SAP HANA-database die wordt uitgevoerd op Azure VM met behulp van de Azure-portal, raadpleegt u [SAP HANA-databases back-ups in Azure VM's](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

* Voor meer informatie over het beheren van een back-up van SAP HANA-database die wordt uitgevoerd op Azure VM met behulp van de Azure-portal, raadpleegt u [Back-up-SAP HANA-databases beheren op Azure VM](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
