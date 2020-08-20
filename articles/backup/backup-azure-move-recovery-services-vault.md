---
title: Azure Backup Recovery Services kluizen verplaatsen
description: Instructies voor het verplaatsen van de Recovery Services-kluis in azure-abonnementen en resource groepen.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: fbbe914b3e567a1a136d735fc52965524bc17b67
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612568"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Een Recovery Services kluis verplaatsen over Azure-abonnementen en-resource groepen

In dit artikel wordt uitgelegd hoe u een Recovery Services kluis kunt verplaatsen die is geconfigureerd voor Azure Backup over Azure-abonnementen of naar een andere resource groep in hetzelfde abonnement. U kunt de Azure Portal of Power shell gebruiken om een Recovery Services kluis te verplaatsen.

## <a name="supported-regions"></a>Ondersteunde regio’s

Resource verplaatsing voor Recovery Services kluis wordt ondersteund in Australië-oost, Australië-Zuid-Oost, Canada-centraal, Canada-oost, Zuid-Azië-oost, Azië-oost, centraal VS, Noord-Centraal VS, VS-Oost, VS-Oost 2, Zuid-Centraal VS, West-Centraal VS, West-Centraal VS 2, VS-West, VS-Oost 2, Centraal-India, India-zuid, Japan, Japan, West , Europa-west, Zuid-Afrika-noord, Zuid-Afrika-west, UK-zuid en UK-west.

## <a name="unsupported-regions"></a>Niet-ondersteunde regio's

Frankrijk-centraal, Frankrijk-zuid, Duitsland-noordoost, Duitsland-centraal, US Gov-Iowa, China-noord, China North2, China-oost, China Oost2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Vereisten voor het verplaatsen van Recovery Services kluis

- Tijdens het verplaatsen van de kluis over resource groepen worden de bron-en doel resource groepen vergrendeld, waardoor er geen schrijf-en verwijder bewerkingen kunnen worden uitgevoerd. Zie dit [artikel](../azure-resource-manager/management/move-resource-group-and-subscription.md) voor meer informatie.
- Alleen beheerders abonnement heeft de machtigingen om een kluis te verplaatsen.
- Voor het verplaatsen van kluizen tussen abonnementen moet het doel abonnement zich in dezelfde Tenant bevinden als het bron abonnement en moet de status zijn ingeschakeld.
- U moet gemachtigd zijn om schrijf bewerkingen uit te voeren op de doel resource groep.
- Door de kluis te verplaatsen, wijzigt u alleen de resource groep. De Recovery Services kluis bevindt zich op dezelfde locatie en kan niet worden gewijzigd.
- U kunt slechts één Recovery Services kluis per regio tegelijk verplaatsen.
- Als een virtuele machine niet wordt verplaatst met de Recovery Services kluis in abonnementen of naar een nieuwe resource groep, blijven de huidige VM-herstel punten intact in de kluis totdat ze verlopen.
- Of de virtuele machine wordt verplaatst met de kluis of niet, u kunt de virtuele machine altijd herstellen met behulp van de behouden Back-upgeschiedenis in de kluis.
- De Azure Disk Encryption vereist dat de sleutel kluis en de virtuele machines zich in dezelfde Azure-regio en hetzelfde abonnement bevinden.
- Als u een virtuele machine wilt verplaatsen met beheerde schijven, raadpleegt u dit [artikel](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- De opties voor het verplaatsen van resources die via het klassieke model zijn geïmplementeerd, zijn afhankelijk van het feit of u de resources in een abonnement verplaatst of naar een nieuw abonnement. Zie dit [artikel](../azure-resource-manager/management/move-resource-group-and-subscription.md) voor meer informatie.
- Het back-upbeleid dat voor de kluis is gedefinieerd, blijft behouden nadat de kluis over alle abonnementen of naar een nieuwe resource groep is verplaatst.
- U kunt alleen een kluis verplaatsen die een van de volgende typen back-upitems bevat. Alle back-upitems van typen die hieronder niet worden vermeld, moeten worden gestopt en de gegevens worden permanent verwijderd voordat de kluis wordt verplaatst.
  - Azure Virtual Machines
  - Microsoft Azure Recovery Services-agent (MARS)
  - Microsoft Azure Backup-Server (MABS)
  - Data Protection Manager (DPM)
- Als u een kluis met back-upgegevens voor de VM verplaatst, moet u de virtuele machines verplaatsen naar hetzelfde abonnement en dezelfde doel-VM-resource groepsnaam gebruiken (zoals in het oude abonnement) om door te gaan met back-ups.

> [!NOTE]
> Het is niet mogelijk om Recovery Services kluizen voor Azure Backup over Azure-regio's te verplaatsen.<br><br>
> Als u virtuele machines (Azure IaaS, Hyper-V, VMware) of fysieke computers hebt geconfigureerd voor herstel na nood gevallen met **Azure site Recovery**, wordt de verplaatsings bewerking geblokkeerd. Als u kluizen voor Azure Site Recovery wilt verplaatsen, raadpleegt u [dit artikel](../site-recovery/move-vaults-across-regions.md) voor meer informatie over het hand matig verplaatsen van kluizen.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Azure Portal gebruiken om Recovery Services kluis te verplaatsen naar een andere resource groep

Een Recovery Services-kluis en de bijbehorende resources verplaatsen naar een andere resource groep

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Open de lijst met **Recovery Services kluizen** en selecteer de kluis die u wilt verplaatsen. Wanneer het kluis dashboard wordt geopend, wordt het weer gegeven zoals in de volgende afbeelding.

   ![Service kluis herstellen openen](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Als u de **essentiële** informatie voor uw kluis niet ziet, selecteert u het pictogram vervolg keuzelijst. U ziet nu de essentiële informatie voor uw kluis.

   ![Het tabblad informatie over Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Selecteer in het menu kluis overzicht de optie **wijzigen** naast de **resource groep**om het deel venster **resources verplaatsen** te openen.

   ![Resource groep wijzigen](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. In het deel venster **resources verplaatsen** kunt u voor de geselecteerde kluis het beste de optionele gerelateerde resources verplaatsen door het selectie vakje te selecteren, zoals in de volgende afbeelding wordt weer gegeven.

   ![Abonnement verplaatsen](./media/backup-azure-move-recovery-services/move-resource.png)

5. Als u de doel resource groep wilt toevoegen, selecteert u in de vervolg keuzelijst **resource groep** een bestaande resource groep of selecteert u **een nieuwe groep maken** .

   ![Resource maken](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Nadat u de resource groep hebt toegevoegd, **moet u weten dat de hulpprogram ma's en scripts die zijn gekoppeld aan verplaatste resources, niet werken totdat ze zijn bijgewerkt** met de optie nieuwe resource-id's en vervolgens **OK** selecteren om de kluis te volt ooien.

   ![Bevestigings bericht](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Azure Portal gebruiken om Recovery Services kluis te verplaatsen naar een ander abonnement

U kunt een Recovery Services kluis en de bijbehorende resources naar een ander abonnement verplaatsen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Open de lijst met Recovery Services kluizen en selecteer de kluis die u wilt verplaatsen. Wanneer het kluis dashboard wordt geopend, wordt het weer gegeven zoals in de volgende afbeelding.

    ![Service kluis herstellen openen](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Als u de **essentiële** informatie voor uw kluis niet ziet, selecteert u het pictogram vervolg keuzelijst. U ziet nu de essentiële informatie voor uw kluis.

    ![Het tabblad informatie over Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Selecteer in het menu kluis overzicht de optie **wijzigen** naast **abonnement**om het deel venster **resources verplaatsen** te openen.

   ![Abonnement wijzigen](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selecteer de resources die u wilt verplaatsen. u wordt aangeraden de optie **Alles selecteren** te gebruiken om alle vermelde optionele resources te selecteren.

   ![resource verplaatsen](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Selecteer het doel abonnement in de vervolg keuzelijst **abonnement** , waar u de kluis wilt verplaatsen.
6. Als u de doel resource groep wilt toevoegen, selecteert u in de vervolg keuzelijst **resource groep** een bestaande resource groep of selecteert u **een nieuwe groep maken** .

   ![Abonnement toevoegen](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Selecteer **Ik begrijp dat hulp middelen en scripts die zijn gekoppeld aan verplaatste resources, niet werken totdat ze de optie nieuwe resource-id's gebruiken** om te bevestigen, en selecteer vervolgens **OK**.

> [!NOTE]
> Back-up tussen meerdere abonnementen (RS-kluis en beveiligde Vm's bevinden zich in verschillende abonnementen) is geen ondersteund scenario. Daarnaast kan de optie voor opslag redundantie van lokale redundante opslag (LRS) naar globale redundante opslag (GRS) en vice versa niet worden gewijzigd tijdens de bewerking voor het verplaatsen van de kluis.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Power shell gebruiken om Recovery Services kluis te verplaatsen

Als u een Recovery Services kluis wilt verplaatsen naar een andere resource groep, gebruikt u de `Move-AzureRMResource` cmdlet. `Move-AzureRMResource` vereist de resource naam en het resource type. U kunt beide van de `Get-AzureRmRecoveryServicesVault` cmdlet ophalen.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Als u de resources naar een ander abonnement wilt verplaatsen, neemt u de `-DestinationSubscriptionId` para meter op.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Nadat u de bovenstaande cmdlets hebt uitgevoerd, wordt u gevraagd om te bevestigen dat u de opgegeven resources wilt verplaatsen. Typ **Y** om te bevestigen. Na een geslaagde validatie wordt de resource verplaatst.

## <a name="use-cli-to-move-recovery-services-vault"></a>CLI gebruiken om Recovery Services kluis te verplaatsen

Als u een Recovery Services kluis wilt verplaatsen naar een andere resource groep, gebruikt u de volgende cmdlet:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Als u wilt overstappen op een nieuw abonnement, geeft u de `--destination-subscription-id` para meter op.

## <a name="post-migration"></a>Na de migratie

1. De toegangs controle voor de resource groepen instellen/controleren.  
2. De functie voor het maken van een back-up en controle moet opnieuw worden geconfigureerd voor de kluis die de verplaatsing heeft voltooid. De vorige configuratie gaat verloren tijdens de verplaatsings bewerking.

## <a name="next-steps"></a>Volgende stappen

U kunt verschillende soorten resources verplaatsen tussen resource groepen en abonnementen.

Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).
