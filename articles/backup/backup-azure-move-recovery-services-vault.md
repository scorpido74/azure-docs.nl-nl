---
title: Azure Backup Recovery Services-kluizen verplaatsen
description: Instructies voor het verplaatsen van de kluis van herstelservices over Azure-abonnementen en resourcegroepen.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 3cfd442d49de2661d68de3c4e4b3575119504eb4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804415"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Een kluis voor Herstelservices verplaatsen naar Azure-abonnementen en resourcegroepen

In dit artikel wordt uitgelegd hoe u een vault voor Herstelservices die is geconfigureerd voor Azure Backup over Azure-abonnementen of naar een andere brongroep in hetzelfde abonnement verplaatst. U de Azure-portal of PowerShell gebruiken om een vault voor Herstelservices te verplaatsen.

## <a name="supported-regions"></a>Ondersteunde regio’s

Resource move for Recovery Services vault wordt ondersteund in Australië East, Australia South East, Canada Central, Canada East, South East Asia, East Asia, Central US, North Central US, East US2, South Central US, West Central US, West Central US2, West US, Central India, South India, Japan East, Japan West, Korea Central, Korea South, North Europe, West-Europe, South Africa North , Zuid-Afrika West, Uk South, en UK West.

## <a name="unsupported-regions"></a>Niet-ondersteunde regio's

Frankrijk Centraal, Frankrijk Zuid, Duitsland Noordoost, Duitsland Centraal, US Gov Iowa, China North, China North2, China East, China East2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Voorwaarden voor het verplaatsen van de kluis van Recovery Services

- Tijdens de vault move over resourcegroepen worden zowel de bron- als de doelgroepgroepen vergrendeld, waardoor de schrijf- en verwijderingsbewerkingen worden voorkomen. Zie voor meer informatie dit [artikel.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- Alleen een beheerdersabonnement heeft de machtigingen om een kluis te verplaatsen.
- Voor het verplaatsen van kluizen over abonnementen moet het doelabonnement zich in dezelfde tenant bevinden als het bronabonnement en moet de status ervan zijn ingeschakeld.
- U moet toestemming hebben om schrijfbewerkingen uit te voeren op de doelgroep.
- Als u de kluis verplaatst, wordt alleen de brongroep gewijzigd. De kluis Recovery Services bevindt zich op dezelfde locatie en kan niet worden gewijzigd.
- U slechts één kluis van Recovery Services tegelijk verplaatsen, per regio.
- Als een VM niet wordt verplaatst met de kluis Herstelservices over abonnementen of naar een nieuwe resourcegroep, blijven de huidige VM-herstelpunten intact in de kluis totdat ze verlopen.
- Of de VM nu met de kluis wordt verplaatst of niet, u de VM altijd herstellen uit de bewaarde back-upgeschiedenis in de kluis.
- De Azure Disk Encryption vereist dat de sleutelkluis en VM's zich in dezelfde Azure-regio en hetzelfde Azure-abonnement bevinden.
- Zie dit [artikel](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/)om een virtuele machine met beheerde schijven te verplaatsen.
- De opties voor het verplaatsen van resources die via het Klassieke model worden geïmplementeerd, verschillen afhankelijk van of u de resources binnen een abonnement verplaatst of naar een nieuw abonnement. Zie voor meer informatie dit [artikel.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- Back-upbeleid dat is gedefinieerd voor de kluis, wordt behouden nadat de kluis over abonnementen of naar een nieuwe brongroep is verplaatst.
- Het verplaatsen van kluizen met de Azure Files, Azure File Sync of SQL in IaaS VM's over abonnementen en brongroepen wordt niet ondersteund.
- Als u een kluis met VM-back-upgegevens over verschillende abonnementen verplaatst, moet u uw VM's naar hetzelfde abonnement verplaatsen en dezelfde naam van de vm-brongroep gebruiken (zoals in het oude abonnement) om back-ups voort te zetten.

> [!NOTE]
> De vaults voor Azure Backup verplaatsen voor Azure Backup in Azure-regio's worden niet ondersteund.<br><br>
> Als u VM's (Azure IaaS, Hyper-V, VMware) of fysieke machines hebt geconfigureerd voor herstel na noodgevallen met **Azure Site Recovery,** wordt de verplaatsingsbewerking geblokkeerd. Als u kluizen wilt verplaatsen voor Azure Site Recovery, bekijkt u [dit artikel](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) voor meer informatie over het handmatig verplaatsen van kluizen.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Azure-portal gebruiken om de vault van Recovery Services naar een andere brongroep te verplaatsen

Een kluis voor herstelservices en de bijbehorende resources verplaatsen naar verschillende resourcegroepen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Open de lijst met **vaults** van Recovery Services en selecteer de kluis die u wilt verplaatsen. Wanneer het dashboard van de kluis wordt geopend, wordt het weergegeven zoals weergegeven in de volgende afbeelding.

   ![Open Herstel servicekluis](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Als u de **Essentials-informatie** voor uw kluis niet ziet, klikt u op het vervolgkeuzepictogram. U moet nu de Essentials informatie voor uw kluis zien.

   ![Tabblad Gegevens van Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Klik in het menu van het kluisoverzicht op **Wijzigen** naast de **groep Resource**om het **hoofdvan** resources verplaatsen te openen.

   ![Resourcegroep wijzigen](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. In het blad **Move resources** wordt voor de geselecteerde kluis aanbevolen om de optionele gerelateerde resources te verplaatsen door het selectievakje in te schakelen zoals weergegeven in de volgende afbeelding.

   ![Abonnement verplaatsen](./media/backup-azure-move-recovery-services/move-resource.png)

5. Als u de doelgroepgroep wilt toevoegen, selecteert u in de vervolgkeuzelijst **Resourcegroep** een bestaande resourcegroep of klikt u op **een nieuwe groepsoptie maken.**

   ![Resource maken](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Na het toevoegen van de resourcegroep bevestigt **u dat ik begrijp dat hulpprogramma's en scripts die zijn gekoppeld aan verplaatste resources niet werken totdat ik ze bijwerk om de optie nieuwe resource-id's te gebruiken** en vervolgens op **OK** klikt om het verplaatsen van de kluis te voltooien.

   ![Bevestigingsbericht](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Azure-portal gebruiken om de vault van Recovery Services naar een ander abonnement te verplaatsen

U een vault van Recovery Services en de bijbehorende resources verplaatsen naar een ander abonnement

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Open de lijst met vaults van Recovery Services en selecteer de kluis die u wilt verplaatsen. Wanneer het dashboard van de kluis wordt geopend, wordt het weergegeven zoals de volgende afbeelding wordt weergegeven.

    ![Open Herstel servicekluis](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Als u de **Essentials-informatie** voor uw kluis niet ziet, klikt u op het vervolgkeuzepictogram. U moet nu de Essentials informatie voor uw kluis zien.

    ![Tabblad Gegevens van Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Klik in het menu van het kluisoverzicht op **Wijzigen** naast **Abonnement**om het **hoofdblad van** de bronnen verplaatsen te openen.

   ![Abonnement wijzigen](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selecteer de resources die moeten worden verplaatst, hier raden we u aan om de optie **Alles selecteren** te gebruiken om alle vermelde optionele bronnen te selecteren.

   ![resource verplaatsen](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Selecteer het doelabonnement in de vervolgkeuzelijst **Abonnement,** waar u de kluis wilt verplaatsen.
6. Als u de doelgroepgroep wilt toevoegen, selecteert u in de vervolgkeuzelijst **Resourcegroep** een bestaande resourcegroep of klikt u op **een nieuwe groepsoptie maken.**

   ![Abonnement toevoegen](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Klik **op ik begrijp dat hulpprogramma's en scripts die zijn gekoppeld aan verplaatste resources niet werken totdat ik ze bijwerk om de optie nieuwe resource-id's te gebruiken** om te bevestigen en klik vervolgens op **OK**.

> [!NOTE]
> Cross subscription backup (RS vault en protected VM's zijn in verschillende abonnementen) is geen ondersteund scenario. Ook opslagredundantie optie van lokale redundante opslag (LRS) naar global redundant storage (GRS) en vice versa kan niet worden gewijzigd tijdens de vault move operatie.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>PowerShell gebruiken om de kluis van Recovery Services te verplaatsen

Als u een kluis van Herstelservices `Move-AzureRMResource` naar een andere resourcegroep wilt verplaatsen, gebruikt u de cmdlet. `Move-AzureRMResource`vereist de resourcenaam en het type resource. Je beide `Get-AzureRmRecoveryServicesVault` uit de cmdlet halen.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Als u de resources naar `-DestinationSubscriptionId` een ander abonnement wilt verplaatsen, neemt u de parameter op.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Nadat u de bovenstaande cmdlets hebt uitgevoerd, wordt u gevraagd te bevestigen dat u de opgegeven resources wilt verplaatsen. Type **Y** om te bevestigen. Na een succesvolle validatie wordt de resource verplaatst.

## <a name="use-cli-to-move-recovery-services-vault"></a>CLI gebruiken om de kluis van Recovery Services te verplaatsen

Als u een kluis van Recovery Services naar een andere resourcegroep wilt verplaatsen, gebruikt u de volgende cmdlet:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Als u naar een nieuw `--destination-subscription-id` abonnement wilt gaan, geeft u de parameter op.

## <a name="post-migration"></a>Na de migratie

1. Stel de toegangsbesturingselementen voor de brongroepen in/verifieer deze.  
2. De functie Back-uprapportage en -bewaking moet opnieuw worden geconfigureerd voor de kluispost die de verplaatsing voltooit. De vorige configuratie gaat verloren tijdens de verplaatsingsbewerking.

## <a name="next-steps"></a>Volgende stappen

U veel verschillende typen resources verplaatsen tussen resourcegroepen en abonnementen.

Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
