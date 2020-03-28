---
title: Zelfstudie - SAP HANA DB-back-up op Azure met CLI
description: In deze zelfstudie leert u hoe u een back-up maakt van SAP HANA-databases die worden uitgevoerd op een Azure VM naar een Azure Backup Recovery Services-kluis met Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: cdc8a8fb09a086a2b9212c21d071f267991fa275
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78206619"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Zelfstudie: Een back-up maken van SAP HANA-databases in een Azure VM met Azure CLI

Azure CLI wordt gebruikt voor het maken en beheren van Azure-bronnen vanuit de opdrachtregel of via scripts. In deze documentatie wordt beschreven hoe u een back-up maakt van een SAP HANA-database en on-demand back-ups activeert - allemaal met Azure CLI. U deze stappen ook uitvoeren met de [Azure-portal.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

In dit document wordt ervan uitgegaan dat er al een SAP HANA-database is geïnstalleerd op een Azure VM. (U ook [een VM maken met Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)). Tegen het einde van deze tutorial, zult u in staat zijn om:

> [!div class="checklist"]
>
> * Een Recovery Services-kluis maken
> * Registreer SAP HANA-instantie en ontdek er database(s) op
> * Back-up inschakelen in een SAP HANA-database
> * Een on-demand back-up activeren

Bekijk de [scenario's die we momenteel ondersteunen](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support) voor SAP HANA.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u de CLI lokaal wilt installeren en gebruiken, moet u Azure CLI-versie xx.xxx.x of hoger uitvoeren. Voer `az --version` uit om de CLI-versie te bepalen. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een vault van Recovery Services is een logische container die de back-upgegevens voor elke beveiligde bron opslaat, zoals Azure VM's of workloads die worden uitgevoerd op Azure VM's- zoals SQL- of HANA-databases. Wanneer de back-uptaak voor een beveiligde resource wordt uitgevoerd, wordt er binnen de Recovery Services-kluis een herstelpunt gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen.

Maak een Recovery Services-kluis met [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create). Geef de resourcegroep en locatie op van de VM die u wilt beveiligen. Meer informatie over het maken van een VM met Azure CLI met deze [VM-quickstart](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Voor deze zelfstudie gebruiken we het volgende:

* een resourcegroep met de naam *saphanaResourceGroup*
* een VM met de naam *saphanaVM*
* bronnen op de *locatie Westus2.*

We maken een kluis genaamd *saphanaVault.*

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

De Recovery Services-kluis is standaard ingesteld voor geografisch redundante opslag. In geval van geografisch redundante opslag worden uw back-upgegevens gerepliceerd naar een secundaire Azure-regio die honderden kilometers van de primaire regio is verwijderd. Als de opslagredundantie-instelling moet worden gewijzigd, gebruikt u de cmdlet [voor back-up-eigenschappen van AZ Backup](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) Vault.

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Gebruik de cmdlet van de [AZ-back-upkluis](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-list) om te zien of uw kluis is gemaakt. U ziet het volgende antwoord:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Registreer en bescherm het SAP HANA-exemplaar

Voor het SAP HANA-exemplaar (de VM met SAP HANA erop) die door de Azure-services moet worden ontdekt, moet een [preregistratiescript](https://aka.ms/scriptforpermsonhana) worden uitgevoerd op de SAP HANA-machine. Zorg ervoor dat aan alle [voorwaarden](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#prerequisites) wordt voldaan voordat u het script uitvoert. Raadpleeg de sectie [Wat het preregistratiescript doet](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) voor meer informatie over wat het script doet.

Zodra het script is uitgevoerd, kan de SAP HANA-instantie worden geregistreerd bij de kluis van herstelservices die we eerder hebben gemaakt. Om de instantie te registreren, gebruik je de cmdlet van het [AZ-back-upcontainerregister.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-register) *VMResourceId* is de resource-id van de VM die u hebt gemaakt om SAP HANA te installeren.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Als de VM zich niet in dezelfde resourcegroep bevindt als de kluis, verwijst *saphanaResourceGroup* naar de resourcegroep waar de kluis is gemaakt.

Het registreren van het SAP HANA-exemplaar detecteert automatisch al zijn huidige databases. Om echter nieuwe databases te ontdekken die in de toekomst kunnen worden toegevoegd, verwijzen we u naar de [nieuwe databases ontdekken die zijn toegevoegd aan de geregistreerde SAP HANA-instantiesectie.](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance)

Gebruik de cmdlet van de AZ [Backup Container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) om te controleren of het SAP HANA-exemplaar met succes is geregistreerd bij uw kluis. U ziet het volgende antwoord:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> De kolom "naam" in de bovenstaande uitvoer verwijst naar de containernaam. Deze containernaam wordt in de volgende secties gebruikt om back-ups in te schakelen en deze te activeren. Wat in dit geval *VMAppContainer is; Compute;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Back-up inschakelen op SAP HANA-database

In de [cmdlet van az-back-up-items](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) wordt een overzicht weergegeven van alle databases die zijn ontdekt op het SAP HANA-exemplaar dat u in de vorige stap hebt geregistreerd.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

U moet de database vinden waarvan u een back-up wilt maken in deze lijst, die er als volgt uitziet:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Zoals u zien aan de bovenstaande uitgang, de SID van het SAP HANA-systeem is HXE. In deze zelfstudie configureren we back-ups voor de *saphanadatabase;hxe;hxe-database* die zich op de *hxehost-server* bevindt.

Om back-ups in een database te beschermen en te configureren, gebruiken we één voor één de [az-back-upbeveiliging enable-for-azurewl-cmdlet.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurewl) Geef de naam op van het beleid dat u wilt gebruiken. Als u een beleid wilt maken met CLI, maakt u het [az-back-upbeleid om](https://docs.microsoft.com//cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet te maken. Voor deze tutorial gebruiken we het *sapahanaBeleid.*

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

U controleren of de bovenstaande back-upconfiguratie is voltooid met behulp van de az [back-up lijst](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) cmdlet. De uitvoer wordt als volgt weergegeven:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

In de cmdlet van de [AZ-back-uptakenlijst](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) worden alle back-uptaken (gepland of on-demand) weergegeven die zijn uitgevoerd of momenteel worden uitgevoerd op de beveiligde database, naast andere bewerkingen zoals registreren, back-upconfigureren, back-upgegevens verwijderen enz.

## <a name="trigger-an-on-demand-backup"></a>Een on-demand back-up activeren

Terwijl de bovenstaande sectie details hoe u een geplande back-up te configureren, deze sectie spreekt over het activeren van een on-demand back-up. Hiervoor maken we gebruik van de [az back-up beveiliging back-up-nu](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now) cmdlet.

>[!NOTE]
> Het bewaarbeleid van een on-demand back-up wordt bepaald door het onderliggende bewaarbeleid voor de database.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

De uitvoer wordt als volgt weergegeven:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

Het antwoord geeft je de naam van de baan. Deze taaknaam kan worden gebruikt om de taakstatus bij te houden met behulp van de cmdlet van de [AZ-back-upfunctie.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

>[!NOTE]
>Naast het plannen van een volledige of differentiële back-up, kunnen ze momenteel ook handmatig worden geactiveerd. Log back-ups worden automatisch geactiveerd en beheerd door SAP HANA intern.
>
> Incrementele back-ups worden momenteel niet ondersteund door Azure Backup.

## <a name="next-steps"></a>Volgende stappen

* Ga voor meer informatie over het herstellen van een SAP HANA-database in Azure VM met CLI, ga verder met de zelfstudie : [Een SAP HANA-database in Azure VM herstellen met CLI](tutorial-sap-hana-restore-cli.md)

* Voor meer informatie over het maken van een back-up van een SAP HANA-database die wordt uitgevoerd in Azure VM met Azure Portal, raadpleegt u [Back-up maken van een SAP HANA-databases in Azure VM's](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
