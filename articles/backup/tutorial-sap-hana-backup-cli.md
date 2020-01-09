---
title: Zelf studie-SAP HANA DB-back-up op Azure met CLI
description: In deze zelf studie leert u hoe u een back-up maakt van SAP HANA data bases die worden uitgevoerd op een virtuele Azure-machine naar een Azure Backup Recovery Services kluis met behulp van Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6d35b6ebfc6f47f5775c24407b645b97112387c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472350"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Zelf studie: back-ups maken van SAP HANA data bases in een Azure-VM met behulp van Azure CLI

Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdracht regel of in scripts. Deze documentatie bevat informatie over het maken van een back-up van een SAP HANA-data base en het activeren van back-ups op aanvraag-allemaal met behulp van Azure CLI. U kunt deze stappen ook uitvoeren met behulp van de [Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

In dit document wordt ervan uitgegaan dat u al een SAP HANA-Data Base op een virtuele machine van Azure hebt geïnstalleerd. (U kunt ook [een virtuele machine maken met behulp van Azure cli](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)). Aan het einde van deze zelf studie kunt u het volgende doen:

> [!div class="checklist"]
>
> * Een Recovery Services-kluis maken
> * SAP HANA-exemplaar registreren en data base (s) ontdekken
> * Back-up inschakelen op een SAP HANA data base
> * Een back-up op aanvraag activeren

Bekijk de [scenario's die momenteel worden ondersteund](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support) voor SAP Hana.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u de CLI lokaal wilt installeren en gebruiken, moet u Azure CLI versie xx. xxx. x of hoger uitvoeren. Voer `az --version` uit om de CLI-versie te bepalen. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een Recovery Services kluis is een logische container waarin de back-upgegevens worden opgeslagen voor elke beveiligde resource, zoals virtuele Azure-machines of workloads die worden uitgevoerd op virtuele machines van Azure, zoals SQL-of HANA-data bases. Wanneer de back-uptaak voor een beveiligde resource wordt uitgevoerd, wordt er binnen de Recovery Services-kluis een herstelpunt gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen.

Maak een Recovery Services-kluis met [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create). Geef de resourcegroep en locatie op van de VM die u wilt beveiligen. Meer informatie over het maken van een virtuele machine met behulp van Azure CLI met deze Snelstartgids voor de [virtuele machine](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Voor deze zelf studie gebruiken we het volgende:

* een resource groep met de naam *saphanaResourceGroup*
* een virtuele machine met de naam *saphanaVM*
* resources op de locatie *westus2* .

Er wordt een kluis gemaakt met de naam *saphanaVault*.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

De Recovery Services-kluis is standaard ingesteld voor geografisch redundante opslag. In geval van geografisch redundante opslag worden uw back-upgegevens gerepliceerd naar een secundaire Azure-regio die honderden kilometers van de primaire regio is verwijderd. Als de instelling voor opslag redundantie moet worden gewijzigd, gebruikt u de cmdlet [AZ backup kluis backup-Properties set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) .

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Gebruik de cmdlet [AZ backup kluis List](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-list) om te controleren of uw kluis is gemaakt. U ziet het volgende antwoord:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Het SAP HANA-exemplaar registreren en beveiligen

Voor de SAP HANA-instantie (de virtuele machine waarop SAP HANA is geïnstalleerd) die moet worden gedetecteerd door de Azure-Services, moet een [vooraf registratie script](https://aka.ms/scriptforpermsonhana) worden uitgevoerd op de SAP Hana machine. Zorg ervoor dat aan alle [vereisten](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#prerequisites) wordt voldaan voordat u het script uitvoert. Raadpleeg de sectie [machtigingen instellen](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#setting-up-permissions) voor meer informatie over de werking van het script.

Zodra het script is uitgevoerd, kan het SAP HANA-exemplaar worden geregistreerd bij de Recovery Services-kluis die u eerder hebt gemaakt. Gebruik de cmdlet [AZ backup container REGI ster](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-register) om het exemplaar te registreren. *VMResourceId* is de resource-id van de virtuele machine die u hebt gemaakt om SAP Hana te installeren.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Als de virtuele machine zich niet in dezelfde resource groep bevindt als de kluis, verwijst *saphanaResourceGroup* naar de resource groep waar de kluis is gemaakt.

Het registreren van de SAP HANA-instantie detecteert automatisch alle huidige data bases. Als u echter nieuwe data bases wilt detecteren die in de toekomst kunnen worden toegevoegd, raadpleegt u de detectie van [nieuwe data bases die zijn toegevoegd aan de sectie geregistreerd SAP Hana](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance) -exemplaar.

Gebruik de cmdlet [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) om te controleren of het SAP Hana-exemplaar met de kluis is geregistreerd. U ziet het volgende antwoord:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> De kolom naam in de bovenstaande uitvoer verwijst naar de container naam. Deze container naam wordt in de volgende secties gebruikt om back-ups in te scha kelen en te activeren. In dit geval is *VMAppContainer; Compute; saphanaResourceGroup; saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Back-up inschakelen op SAP HANA data base

De cmdlet [back-up](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) die kan worden beveiligd met de lijst met alle data bases die zijn gedetecteerd op het SAP Hana exemplaar dat u in de vorige stap hebt geregistreerd.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

U vindt de data base waarvan u een back-up wilt maken in deze lijst, die er als volgt uitziet:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Zoals u kunt zien in de bovenstaande uitvoer, is de SID van het SAP HANA systeem HXE. In deze zelf studie configureren we back-ups voor de *saphanadatabase; hxe; hxe* -data base die zich op de *hxehost* -server bevindt.

Als u een back-up wilt beveiligen en configureren op een Data Base, wordt er één tegelijk gebruikt voor de cmdlet [AZ Backup Protection Enable-for-azurewl](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurewl) . Geef de naam op van het beleid dat u wilt gebruiken. Als u een beleid wilt maken met behulp van CLI, gebruikt u de opdracht [AZ backup policy Create](https://docs.microsoft.com//cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) . Voor deze zelf studie gebruiken we het *sapahanaPolicy* -beleid.

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

U kunt controleren of de bovenstaande back-upconfiguratie is voltooid met behulp van de cmdlet [AZ Backup Job List](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) . De uitvoer wordt als volgt weer gegeven:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

In de cmdlet [AZ Backup Job List](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) worden alle back-uptaken (gepland of op aanvraag) weer gegeven die worden uitgevoerd of die momenteel worden uitgevoerd op de beveiligde data base, naast andere bewerkingen zoals registreren, back-up configureren, back-upgegevens verwijderen, enzovoort.

## <a name="trigger-an-on-demand-backup"></a>Een back-up op aanvraag activeren

In de bovenstaande sectie wordt beschreven hoe u een geplande back-up configureert. in dit gedeelte vindt u informatie over het activeren van een back-up op aanvraag. Hiervoor gebruiken we de cmdlet [AZ Backup Protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now) .

>[!NOTE]
> Het Bewaar beleid van een back-up op aanvraag wordt bepaald door het onderliggende Bewaar beleid voor de data base.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

De uitvoer wordt als volgt weer gegeven:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

Met het antwoord krijgt u de taak naam. Deze taak naam kan worden gebruikt om de taak status bij te houden met de cmdlet [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

>[!NOTE]
>Naast het plannen van een volledige of een differentiële back-up, kunnen ze ook hand matig worden geactiveerd. Logboek back-ups worden automatisch door SAP HANA intern geactiveerd en beheerd.
>
> Incrementele back-ups worden momenteel niet ondersteund door Azure Backup.

## <a name="next-steps"></a>Volgende stappen

* Ga verder met de zelf studie – [een SAP Hana-data base herstellen in azure VM met behulp van CLI](tutorial-sap-hana-restore-cli.md) voor meer informatie over het herstellen van een SAP Hana-data base in azure VM met behulp van CLI

* Voor informatie over het maken van een back-up van een SAP HANA-data base die wordt uitgevoerd in een Azure-VM met behulp van Azure Portal, raadpleegt u [een SAP Hana-data bases](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
