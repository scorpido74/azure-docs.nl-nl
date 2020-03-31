---
title: De gedeelde afbeeldingsgalerie gebruiken om een aangepaste groep te maken - Azure Batch | Microsoft Documenten
description: Maak een batchgroep met de Gedeelde afbeeldingsgalerie om aangepaste afbeeldingen in te richten op gegevensknooppunten die de software en gegevens bevatten die u nodig hebt voor uw toepassing. Aangepaste afbeeldingen zijn een efficiënte manier om compute nodes te configureren om uw Batch-workloads uit te voeren.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 08/28/2019
ms.author: labrenne
ms.openlocfilehash: 2cff6a0e48fc7bf58a642f509fcda6b114e002ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022933"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>De gedeelde afbeeldingsgalerie gebruiken om een aangepaste groep te maken

Wanneer u een Azure Batch-groep maakt met behulp van de virtuele machineconfiguratie, geeft u een VM-afbeelding op die het besturingssysteem biedt voor elk compute-knooppunt in de groep. U een pool van virtuele machines maken met een ondersteunde Azure Marketplace-afbeelding of een aangepaste afbeelding maken met de [Galerie gedeelde afbeeldingen](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Voordelen van de gedeelde afbeeldingsgalerie

Wanneer u de gedeelde afbeeldingsgalerie gebruikt voor uw aangepaste afbeelding, hebt u controle over het type en de configuratie van het besturingssysteem en het type gegevensschijven. Uw gedeelde afbeelding kan toepassingen en referentiegegevens bevatten die beschikbaar komen op alle batchgroepknooppunten zodra ze zijn ingericht.

U ook meerdere versies van een afbeelding hebben als dat nodig is voor uw omgeving. Wanneer u een afbeeldingsversie gebruikt om een vm te maken, wordt de afbeeldingsversie gebruikt om nieuwe schijven voor de vm te maken.

Het gebruik van een gedeelde afbeelding bespaart tijd bij het voorbereiden van de compute nodes van uw groep om uw batchwerkbelasting uit te voeren. Het is mogelijk om een Azure Marketplace-afbeelding te gebruiken en software te installeren op elk compute-knooppunt na inlevering, maar het gebruik van een gedeelde afbeelding is meestal efficiënter. Bovendien u meerdere replica's opgeven voor de gedeelde afbeelding, zodat u bij het maken van pools met veel VM's (meer dan 600 VM's) tijd bespaart op het maken van groepen.

Het gebruik van een gedeelde afbeelding die is geconfigureerd voor uw scenario, kan verschillende voordelen bieden:

* **Gebruik dezelfde afbeeldingen in de regio's.** U replica's voor gedeelde afbeeldingen maken in verschillende regio's, zodat al uw pools dezelfde afbeelding gebruiken.
* **Het besturingssysteem (OS) configureren.** U de configuratie van de schijf van het besturingssysteem van de afbeelding aanpassen.
* **Toepassingen vooraf installeren.** Het vooraf installeren van toepassingen op de OS-schijf is efficiënter en minder foutgevoelig dan het installeren van toepassingen nadat u de compute-knooppunten hebt ingericht met een starttaak.
* **Kopieer eenmaal grote hoeveelheden gegevens.** Maak statische gegevens onderdeel van de beheerde gedeelde afbeelding door deze te kopiëren naar de gegevensschijven van een beheerde afbeelding. Dit hoeft slechts één keer te gebeuren en maakt gegevens beschikbaar voor elk knooppunt van het zwembad.
* **Kweek zwembaden in grotere maten.** Met de gedeelde afbeeldingsgalerie u grotere pools maken met uw aangepaste afbeeldingen, samen met meer replica's voor gedeelde afbeeldingen.
* **Betere prestaties dan aangepaste afbeelding.** Met gedeelde afbeeldingen is de tijd die nodig is om de steady state te bereiken tot 25% sneller en is de VM-idle latency tot 30% korter.
* **Afbeeldingsversies en groepering voor eenvoudiger beheer.** De definitie van afbeeldingsgroepering bevat informatie over waarom de afbeelding is gemaakt, voor welk besturingssysteem het is en informatie over het gebruik van de afbeelding. Het groeperen van afbeeldingen zorgt voor eenvoudiger beeldbeheer. Zie [Afbeeldingsdefinities voor](../virtual-machines/windows/shared-image-galleries.md#image-definitions)meer informatie .

## <a name="prerequisites"></a>Vereisten

* **Een Azure Batch-account.** Als u een Batch-account wilt maken, raadpleegt u batch snel start met de [Azure-portal](quick-create-portal.md) of [Azure CLI](quick-create-cli.md).

* **Een afbeelding van een gedeelde afbeelding van de afbeelding**. Als u een gedeelde afbeelding wilt maken, moet u een beheerde afbeeldingsbron hebben of maken. De afbeelding moet worden gemaakt op maken van momentopnamen van de OS-schijf van de VM en eventueel de bijgevoegde gegevensschijven. Zie [Een beheerde afbeelding voorbereiden](#prepare-a-managed-image)voor meer informatie .

> [!NOTE]
> Uw gedeelde afbeelding moet in hetzelfde abonnement staan als het Batch-account. Uw gedeelde afbeelding kan zich in verschillende regio's bevinden, zolang deze replica's heeft in dezelfde regio als uw Batch-account.

## <a name="prepare-a-managed-image"></a>Een beheerde afbeelding voorbereiden

In Azure u een beheerde afbeelding voorbereiden op:

* Momentopnamen van het besturingssysteem en de gegevensschijven van een Azure-vm
* Een gegeneraliseerde Azure VM met beheerde schijven
* Een algemene on-premises VHD geüpload naar de cloud

Als u batchgroepen betrouwbaar wilt schalen met een aangepaste afbeelding, raden we u aan een beheerde afbeelding te maken met *alleen* de eerste methode: met behulp van momentopnamen van de schijven van de VM. Bekijk de volgende stappen om een vm voor te bereiden, een momentopname te maken en een afbeelding te maken op basis van de momentopname.

### <a name="prepare-a-vm"></a>Een VM voorbereiden

Als u een nieuwe vm voor de afbeelding maakt, gebruikt u een Azure Marketplace-afbeelding van de eerste partij die wordt ondersteund door Batch als basisafbeelding voor uw beheerde afbeelding. Alleen beelden van de eerste partij kunnen worden gebruikt als basisafbeelding. Zie de SKU's-bewerking [lijstknooppunt agent](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) voor Azure Marketplace voor een volledige lijst met Azure Marketplace-afbeeldingsverwijzingen die worden ondersteund door Azure Batch.

> [!NOTE]
> U geen afbeelding van derden gebruiken met aanvullende licentie- en aankoopvoorwaarden als basisafbeelding. Zie de richtlijnen voor [Linux-](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) of [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) Windows-VM's voor informatie over deze Marketplace-afbeeldingen.

* Zorg ervoor dat de VM is gemaakt met een beheerde schijf. Dit is de standaardopslaginstelling wanneer u een vm maakt.
* Installeer geen Azure-extensies, zoals de Aangepaste Script-extensie, op de VM. Als de afbeelding een vooraf geïnstalleerde extensie bevat, kan Azure problemen ondervinden bij het implementeren van de batchgroep.
* Wanneer u gekoppelde gegevensschijven gebruikt, moet u de schijven vanuit een vm monteren en opmaken om ze te gebruiken.
* Controleer of de basis-osafbeelding die u opgeeft, het standaardtijdelijke station gebruikt. De batchknooppuntagent verwacht momenteel het standaardtijdelijke station.
* Zodra de VM wordt uitgevoerd, verbinding maken met het via RDP (voor Windows) of SSH (voor Linux). Installeer de benodigde software of kopieer de gewenste gegevens.  

### <a name="create-a-vm-snapshot"></a>Een VM-momentopname maken

Een momentopname is een volledige, alleen-lezen kopie van een VHD. Als u een momentopname wilt maken van het besturingssysteem of de gegevensschijven van een virtuele machine, u de Azure-portal of opdrachtregelhulpprogramma's gebruiken. Zie de richtlijnen voor [Linux-](../virtual-machines/linux/snapshot-copy-managed-disk.md) of Windows-VM's voor stappen en opties om een momentopname te maken. [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

### <a name="create-an-image-from-one-or-more-snapshots"></a>Een afbeelding maken op basis van een of meer momentopnamen

Als u een beheerde afbeelding wilt maken vanuit een momentopname, gebruikt u Azure-opdrachtregelgereedschappen, zoals de opdracht [AZ-afbeelding maken.](/cli/azure/image) Maak een afbeelding door een momentopname van een OS-schijf op te geven en optioneel een of meer momentopnamen van gegevensschijf.

### <a name="create-a-shared-image-gallery"></a>Een gedeelde afbeeldingsgalerie maken

Nadat u uw beheerde afbeelding hebt gemaakt, moet u een gedeelde afbeeldingsgalerie maken om uw aangepaste afbeelding beschikbaar te maken. Zie [Een gedeelde afbeeldingsgalerie maken met Azure CLI](../virtual-machines/linux/shared-images.md) of Een gedeelde [afbeeldingsgalerie maken met de Azure Portal](../virtual-machines/linux/shared-images-portal.md)voor meer informatie over het maken van een gedeelde afbeeldingsgalerie voor uw afbeeldingen.

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Een groep maken op basis van een gedeelde afbeelding met de Azure CLI

Als u een groep wilt maken vanuit uw `az batch pool create` gedeelde afbeelding met de Azure CLI, gebruikt u de opdracht. Geef de gedeelde afbeeldings-id in het `--image` veld op. Controleer of het type besturingssysteem en De SKU overeenkomen met de versies die zijn opgegeven door`--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Een groep maken op basis van een gedeelde afbeelding met C #

U ook een pool maken van een gedeelde afbeelding met de C# SDK.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Een groep maken vanuit een gedeelde afbeelding met de Azure-portal

Gebruik de volgende stappen om een groep te maken van een gedeelde afbeelding in de Azure-portal.

1. Open de [Azure Portal](https://portal.azure.com).
1. Ga naar **Batch-accounts** en selecteer uw account.
1. Selecteer **Groepen** en **voeg toe** om een nieuwe groep te maken.
1. Selecteer In de sectie **Afbeeldingstype** de optie **Gedeelde afbeeldingsgalerie**.
1. Vul de resterende secties in met informatie over uw beheerde afbeelding.
1. Selecteer **OK**.

![Maak een pool met van een gedeelde afbeelding met de portal.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Overwegingen voor grote zwembaden

Als u van plan bent een groep met honderden of duizenden VM's of meer te maken met behulp van een gedeelde afbeelding, gebruikt u de volgende richtlijnen.

* **Replicanummers van gedeelde afbeeldingsgalerie.**  Voor elke pool met maximaal 600 exemplaren raden we u aan ten minste één replica te bewaren. Als u bijvoorbeeld een groep met 3000 VM's maakt, moet u ten minste 5 replica's van uw afbeelding bewaren. We raden altijd aan om meer replica's te bewaren dan minimumvereisten voor betere prestaties.

* **Het formaat van een time-out wijzigen.** Als uw pool een vast aantal knooppunten bevat (als deze `resizeTimeout` niet automatisch wordt geschaald), verhoogt u de eigenschap van het zwembad, afhankelijk van de grootte van het zwembad. Voor elke 1000 VM's is de aanbevolen time-out van het formaat ten minste 15 minuten. De aanbevolen time-out voor een groep met 2000 VM's is bijvoorbeeld minimaal 30 minuten.

## <a name="next-steps"></a>Volgende stappen

* Zie [Grootschalige parallelle rekenoplossingen ontwikkelen met Batch](batch-api-basics.md)voor een diepgaand overzicht van Batch.
