---
title: Een aangepaste groep inrichten vanuit een beheerde afbeelding - Azure Batch | Microsoft Documenten
description: Maak een batchgroep van een beheerde afbeeldingsbron om compute nodes in te richten met de software en gegevens voor uw toepassing.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/16/2019
ms.author: labrenne
ms.openlocfilehash: 1ef6be2ba9364203dceba54ab51325c05dbbbe41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020145"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Een beheerde afbeelding gebruiken om een pool van virtuele machines te maken

Als u een aangepaste afbeelding wilt maken voor de virtuele machines (VM's) van uw batchgroep, u de [gedeelde afbeeldingsgalerie](batch-sig-images.md)of een *beheerde afbeeldingsbron* gebruiken.

> [!TIP]
> In de meeste gevallen moet u aangepaste afbeeldingen maken met behulp van de gedeelde afbeeldingsgalerie. Door de gedeelde afbeeldingsgalerie te gebruiken, u groepen sneller inrichten, grotere hoeveelheden VM's schalen en de betrouwbaarheid verbeteren bij het inrichten van VM's. Zie [De galerie Voor gedeelde afbeeldingen gebruiken om een aangepaste groep te maken.](batch-sig-images.md)

## <a name="prerequisites"></a>Vereisten

- **Een beheerde afbeeldingsbron**. Als u een groep virtuele machines wilt maken met behulp van een aangepaste afbeelding, moet u een beheerde afbeeldingsbron hebben of maken in hetzelfde Azure-abonnement en dezelfde regio als het Batch-account. De afbeelding moet worden gemaakt op maken van momentopnamen van de OS-schijf van de VM en eventueel de bijgevoegde gegevensschijven. Zie de volgende sectie voor meer informatie en stappen om een beheerde afbeelding voor te bereiden.
  - Gebruik een unieke aangepaste afbeelding voor elke groep die u maakt.
  - Als u een groep met de afbeelding wilt maken met de Batch-API's, geeft u de **bron-id** van de afbeelding op, die van het formulier `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`is. Als u de portal wilt gebruiken, gebruikt u de **naam** van de afbeelding.  
  - De beheerde afbeeldingsbron moet bestaan voor de levensduur van de groep om opschaing mogelijk te maken en kan worden verwijderd nadat de groep is verwijderd.

- **Azure Active Directory (AAD)-verificatie**. De Batch client API moet AAD-verificatie gebruiken. Azure Batch-ondersteuning voor AAD is gedocumenteerd in [Verificatie batchserviceoplossingen met Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Een aangepaste afbeelding voorbereiden

In Azure u een beheerde afbeelding voorbereiden op:

- Momentopnamen van het besturingssysteem en de gegevensschijven van een Azure-vm
- Een gegeneraliseerde Azure VM met beheerde schijven
- Een algemene on-premises VHD geüpload naar de cloud

Als u batchgroepen betrouwbaar wilt schalen met een aangepaste afbeelding, raden we u aan een beheerde afbeelding te maken met *alleen* de eerste methode: met behulp van momentopnamen van de schijven van de VM. Bekijk de volgende stappen om een vm voor te bereiden, een momentopname te maken en een afbeelding te maken op basis van de momentopname.

### <a name="prepare-a-vm"></a>Een VM voorbereiden

Als u een nieuwe vm voor de afbeelding maakt, gebruikt u een Azure Marketplace-afbeelding van de eerste partij die wordt ondersteund door Batch als basisafbeelding voor uw beheerde afbeelding. Alleen beelden van de eerste partij kunnen worden gebruikt als basisafbeelding. Zie de SKU's-bewerking [lijstknooppunt agent](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) voor Azure Marketplace voor een volledige lijst met Azure Marketplace-afbeeldingsverwijzingen die worden ondersteund door Azure Batch.

> [!NOTE]
> U geen afbeelding van derden gebruiken met aanvullende licentie- en aankoopvoorwaarden als basisafbeelding. Zie de richtlijnen voor [Linux-](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) of [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) Windows-VM's voor informatie over deze Marketplace-afbeeldingen.

- Zorg ervoor dat de VM is gemaakt met een beheerde schijf. Dit is de standaardopslaginstelling wanneer u een vm maakt.
- Installeer geen Azure-extensies, zoals de Aangepaste Script-extensie, op de VM. Als de afbeelding een vooraf geïnstalleerde extensie bevat, kan Azure problemen ondervinden bij het implementeren van de batchgroep.
- Wanneer u gekoppelde gegevensschijven gebruikt, moet u de schijven vanuit een vm monteren en opmaken om ze te gebruiken.
- Controleer of de basis-osafbeelding die u opgeeft, het standaardtijdelijke station gebruikt. De batchknooppuntagent verwacht momenteel het standaardtijdelijke station.
- Zodra de VM wordt uitgevoerd, verbinding maken met het via RDP (voor Windows) of SSH (voor Linux). Installeer de benodigde software of kopieer de gewenste gegevens.  

### <a name="create-a-vm-snapshot"></a>Een VM-momentopname maken

Een momentopname is een volledige, alleen-lezen kopie van een VHD. Als u een momentopname wilt maken van het besturingssysteem of de gegevensschijven van een virtuele machine, u de Azure-portal of opdrachtregelhulpprogramma's gebruiken. Zie de richtlijnen voor [Linux-](../virtual-machines/linux/snapshot-copy-managed-disk.md) of Windows-VM's voor stappen en opties om een momentopname te maken. [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

### <a name="create-an-image-from-one-or-more-snapshots"></a>Een afbeelding maken op basis van een of meer momentopnamen

Als u een beheerde afbeelding wilt maken vanuit een momentopname, gebruikt u Azure-opdrachtregelgereedschappen, zoals de opdracht [AZ-afbeelding maken.](/cli/azure/image) U een afbeelding maken door een momentopname van een OS-schijf op te geven en optioneel een of meer momentopnamen van gegevensschijf.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Een pool maken op basis van een aangepaste afbeelding in de portal

Zodra u uw aangepaste afbeelding hebt opgeslagen en u de bron-id of naam ervan kent, maakt u een batchgroep van die afbeelding. In de volgende stappen ziet u hoe u een groep maakt vanuit de Azure-portal.

> [!NOTE]
> Als u de groep maakt met een van de Batch-API's, controleert u of de identiteit die u gebruikt voor AAD-verificatie machtigingen heeft voor de afbeeldingsbron. Zie [Batch-serviceoplossingen verifiëren met Active Directory](batch-aad-auth.md).
>
> De bron voor de beheerde afbeelding moet bestaan voor de levensduur van de groep. Als de onderliggende resource wordt verwijderd, kan de groep niet worden geschaald.

1. Ga in Azure Portal naar uw Batch-account. Dit account moet zich in hetzelfde abonnement en dezelfde regio bevinden als de resourcegroep die de aangepaste afbeelding bevat.
2. Selecteer in het venster **Instellingen** aan de linkerkant het **menu-item Groeping.**
3. Selecteer **in** het venster Pools de opdracht **Toevoegen.**
4. Selecteer **in** het venster Groep toevoegen de optie **Aangepaste afbeelding (Linux/Windows)** in de vervolgkeuzelijst **Afbeeldingstype.** Selecteer in de vervolgkeuzelijst **Aangepaste VM-afbeelding** de afbeeldingsnaam (korte vorm van de resource-id).
5. Selecteer de juiste **Uitgever/Aanbieding/Sku** voor uw aangepaste afbeelding.
6. Geef de resterende vereiste instellingen op, waaronder de **grootte van knooppunt,** **speciale knooppunten voor doel**en knooppunten met lage **prioriteit,** evenals de gewenste optionele instellingen.

    Voor een aangepaste microsoft Windows Server Datacenter 2016-afbeelding wordt bijvoorbeeld het venster **Groep toevoegen** weergegeven zoals hieronder wordt weergegeven:

    ![Groep toevoegen vanuit aangepaste Windows-afbeelding](media/batch-custom-images/add-pool-custom-image.png)
  
Zie de eigenschap **Besturingssysteem** in het gedeelte resourceoverzicht van het **poolvenster** om te controleren of een bestaande groep is gebaseerd op een aangepaste afbeelding. Als de groep is gemaakt op basis van een aangepaste afbeelding, wordt deze ingesteld op **Aangepaste VM-afbeelding**.

Alle aangepaste afbeeldingen die aan een groep zijn gekoppeld, worden weergegeven in het **venster Eigenschappen van** het zwembad.

## <a name="considerations-for-large-pools"></a>Overwegingen voor grote zwembaden

Als u van plan bent een groep met honderden VM's of meer te maken met behulp van een aangepaste afbeelding, is het belangrijk om de voorgaande richtlijnen te volgen om een afbeelding te gebruiken die is gemaakt van een VM-momentopname.

Let ook op de volgende overwegingen:

- **Groottelimieten** - Batch beperkt de grootte van de groep tot 2500 toegewezen compute-knooppunten of 1000 knooppunten met een lage prioriteit wanneer u een aangepaste afbeelding gebruikt.

  Als u dezelfde afbeelding (of meerdere afbeeldingen op basis van dezelfde onderliggende momentopname) gebruikt om meerdere groepen te maken, kunnen de totale rekenknooppunten in de groepen de voorgaande limieten niet overschrijden. We raden u af om een afbeelding of de onderliggende momentopname te gebruiken voor meer dan één groep.

  Limieten kunnen worden verlaagd als u de pool configureert met [binnenkomende NAT-groepen.](pool-endpoint-configuration.md)

- **Time-out wijzigen** - Als uw groep een vast aantal knooppunten bevat (niet automatisch schalen), verhoogt u de eigenschap Formaat Time-out van de groep tot een waarde van 20-30 minuten. Als uw pool de doelgrootte niet binnen de time-outperiode bereikt, voert u een andere [groottebewerking](/rest/api/batchservice/pool/resize)uit.

  Als u een groep met meer dan 300 rekenknooppunten plant, moet u het formaat van de groep mogelijk meerdere keren wijzigen om de doelgrootte te bereiken.
  
Door de [gedeelde afbeeldingsgalerie te](batch-sig-images.md)gebruiken, u grotere pools maken met uw aangepaste afbeeldingen, samen met meer replica's voor gedeelde afbeeldingen. Met gedeelde afbeeldingen is de tijd die nodig is om de steady state te bereiken tot 25% sneller en is de VM-idle latency tot 30% korter.

## <a name="considerations-for-using-packer"></a>Overwegingen voor het gebruik van Packer

Het rechtstreeks maken van een beheerde afbeeldingsbron met Packer kan alleen worden gedaan met batchaccounts in de gebruikersabonnementsmodus. Voor batchservicemodusaccounts moet u eerst een VHD maken en vervolgens de VHD importeren in een beheerde afbeeldingsbron. Afhankelijk van de toewijzingsmodus voor een groep (gebruikersabonnement of Batch-service) zijn uw stappen om een beheerde afbeeldingsbron te maken, gewijzigd.

Controleer of de resource die wordt gebruikt om de beheerde afbeelding te maken, bestaat voor de levensduur van een groep die verwijst naar de aangepaste afbeelding. Als u dit niet doet, kan dit leiden tot fouten in de toewijzing van de groep en/of het formaat van fouten wijzigen.

Als de afbeelding of de onderliggende resource wordt verwijderd, u een fout krijgen die vergelijkbaar is met: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Als u deze fout krijgt, moet u ervoor zorgen dat de onderliggende resource niet is verwijderd.

Zie [Een Linux-afbeelding bouwen met Packer](../virtual-machines/linux/build-image-with-packer.md) of [Een Windows-afbeelding bouwen met Packer voor](../virtual-machines/windows/build-image-with-packer.md)meer informatie over het gebruik van Packer om een VM te maken.

## <a name="next-steps"></a>Volgende stappen

Zie [Grootschalige parallelle rekenoplossingen ontwikkelen met Batch](batch-api-basics.md)voor een diepgaand overzicht van Batch.
