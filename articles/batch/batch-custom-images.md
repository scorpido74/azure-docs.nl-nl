---
title: Een aangepaste groep inrichten vanuit een beheerde installatie kopie-Azure Batch | Microsoft Docs
description: Maak een batch-pool van een beheerde installatie kopie bron om reken knooppunten in te richten met de software en gegevens voor uw toepassing.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 09/16/2019
ms.author: lahugh
ms.openlocfilehash: 82a5f79ca7b7a16cd8f7294ebd1f70816b40ad82
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "73519233"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Een beheerde installatie kopie gebruiken om een pool van virtuele machines te maken

Als u een aangepaste installatie kopie wilt maken voor de virtuele machines van de batch-pool (Vm's), kunt u de [Galerie met gedeelde afbeeldingen](batch-sig-images.md)of een *beheerde afbeeldings* bron gebruiken.

> [!TIP]
> In de meeste gevallen moet u aangepaste installatie kopieën maken met behulp van de galerie met gedeelde afbeeldingen. Met de galerie gedeelde afbeeldingen kunt u Pools sneller inrichten, grotere aantallen Vm's schalen en de betrouw baarheid verbeteren bij het inrichten van Vm's. Zie [de galerie met gedeelde afbeeldingen gebruiken om een aangepaste groep te maken](batch-sig-images.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- **Een beheerde installatie kopie bron**. Als u een groep virtuele machines wilt maken met behulp van een aangepaste installatie kopie, moet u een beheerde installatie kopie bron hebben of maken in hetzelfde Azure-abonnement en dezelfde regio als het batch-account. De installatie kopie moet worden gemaakt op basis van moment opnamen van de besturingssysteem schijf van de VM en optioneel op de gekoppelde gegevens schijven. Zie de volgende sectie voor meer informatie en stappen om een beheerde installatie kopie voor te bereiden.
  - Gebruik een unieke aangepaste installatie kopie voor elke groep die u maakt.
  - Als u een groep wilt maken met de installatie kopie met behulp van de batch-Api's, geeft u de **resource-id** op van de afbeelding, die van het formulier is `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Als u de portal wilt gebruiken, gebruikt u de **naam** van de installatie kopie.  
  - De beheerde installatie kopie bron moet bestaan voor de levens duur van de groep zodat deze kan worden geschaald en kan worden verwijderd nadat de groep is verwijderd.

- **Azure Active Directory (Aad)-verificatie**. De batch-client-API moet gebruikmaken van AAD-verificatie. Azure Batch ondersteuning voor AAD wordt beschreven in [batch-service oplossingen verifiëren met Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Een aangepaste installatie kopie voorbereiden

In azure kunt u een beheerde installatie kopie voorbereiden vanaf:

- Moment opnamen van het besturings systeem en de gegevens schijven van een virtuele machine van Azure
- Een gegeneraliseerde Azure-VM met beheerde schijven
- Een gegeneraliseerde on-premises VHD die is geüpload naar de Cloud

Als u batch-Pools betrouwbaar wilt schalen met een aangepaste installatie kopie, kunt u het beste een beheerde installatie kopie maken met *alleen* de eerste methode: met behulp van moment opnamen van de schijven van de virtuele machine. Raadpleeg de volgende stappen om een virtuele machine voor te bereiden, een moment opname te maken en een installatie kopie van de moment opname.

### <a name="prepare-a-vm"></a>Een virtuele machine voorbereiden

Als u een nieuwe VM voor de installatie kopie maakt, gebruikt u een Azure Marketplace-installatie kopie van de eerste partij die door batch wordt ondersteund als basis installatie kopie voor uw beheerde installatie kopie. Alleen installatie kopieën van de eerste partij kunnen worden gebruikt als basis installatie kopie. Voor een volledige lijst met voor beelden van Azure Marketplace-installatie kopieën die door Azure Batch worden ondersteund, raadpleegt u de [List node agent sku's](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) -bewerking.

> [!NOTE]
> U kunt geen afbeelding van derden gebruiken die aanvullende licentie-en aankoop voorwaarden als uw basis installatie kopie heeft. Zie de richt lijnen voor [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) -of [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) -vm's voor meer informatie over deze installatie kopieën voor Marketplace.

- Zorg ervoor dat de virtuele machine is gemaakt met een beheerde schijf. Dit is de standaard instelling voor opslag wanneer u een VM maakt.
- Installeer geen Azure-extensies, zoals de aangepaste script extensie, op de VM. Als de installatie kopie een vooraf geïnstalleerde extensie bevat, kan Azure problemen ondervinden bij het implementeren van de batch-pool.
- Wanneer u gekoppelde gegevens schijven gebruikt, moet u de schijven koppelen en Format teren vanuit een VM om ze te gebruiken.
- Zorg ervoor dat de basis installatie kopie van het besturings systeem dat u opgeeft, gebruikmaakt van het standaard tijdelijke station. De batch-knooppunt agent verwacht momenteel het standaard tijdelijke station.
- Zodra de virtuele machine wordt uitgevoerd, maakt u verbinding met de VM via RDP (voor Windows) of SSH (voor Linux). Installeer de benodigde software of kopieer de gewenste gegevens.  

### <a name="create-a-vm-snapshot"></a>Een VM-moment opname maken

Een moment opname is een volledige, alleen-lezen kopie van een VHD. Als u een moment opname van het besturings systeem of de gegevens schijven van een virtuele machine wilt maken, kunt u de Azure Portal of opdracht regel Programma's gebruiken. Zie de richt lijnen voor [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) -of [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) -vm's voor een overzicht van de stappen en opties voor het maken van een moment opname.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Een installatie kopie maken van een of meer moment opnamen

Als u een beheerde installatie kopie wilt maken op basis van een moment opname, gebruikt u de opdracht regel Programma's van Azure, zoals de opdracht [AZ image Create](/cli/azure/image) . U kunt een installatie kopie maken door een moment opname van de besturingssysteem schijf en eventueel een of meer moment opnamen van de gegevens schijf op te geven.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Een groep maken op basis van een aangepaste installatie kopie in de portal

Nadat u uw aangepaste installatie kopie hebt opgeslagen en u de resource-ID of-naam weet, maakt u een batch-pool van die afbeelding. De volgende stappen laten zien hoe u een groep maakt op basis van de Azure Portal.

> [!NOTE]
> Als u de pool maakt met behulp van een van de batch-Api's, moet u ervoor zorgen dat de identiteit die u voor AAD-verificatie gebruikt, machtigingen heeft voor de afbeeldings bron. Zie [batch-service oplossingen verifiëren met Active Directory](batch-aad-auth.md).
>
> De resource voor de beheerde installatie kopie moet bestaan voor de levens duur van de groep. Als de onderliggende resource wordt verwijderd, kan de groep niet worden geschaald.

1. Ga in Azure Portal naar uw Batch-account. Dit account moet zich in hetzelfde abonnement en dezelfde regio bevinden als de resource groep met de aangepaste installatie kopie.
2. Selecteer in het venster **instellingen** aan de linkerkant de menu opdracht **groepen** .
3. Selecteer in het venster **groepen** de opdracht **toevoegen** .
4. Selecteer in het venster **groep toevoegen** de optie **aangepaste installatie kopie (Linux/Windows)** in de vervolg keuzelijst **afbeeldings type** . Selecteer in de vervolg keuzelijst **aangepaste VM-installatie** kopie de naam van de installatie kopie (korte vorm van de resource-id).
5. Selecteer de juiste **Uitgever/aanbieding/SKU** voor uw aangepaste installatie kopie.
6. Geef de resterende vereiste instellingen op, zoals de **knooppunt grootte**, het **doel toegewezen knoop punten**en **knoop punten met een lage prioriteit**, evenals de gewenste optionele instellingen.

    Bijvoorbeeld, voor een aangepaste installatie kopie van micro soft Windows Server Data Center 2016, wordt het venster **groep toevoegen** weer gegeven, zoals hieronder wordt weer gegeven:

    ![Groep toevoegen vanuit aangepaste Windows-installatie kopie](media/batch-custom-images/add-pool-custom-image.png)
  
Als u wilt controleren of een bestaande groep is gebaseerd op een aangepaste installatie kopie, raadpleegt u de eigenschap van het **besturings systeem** in de sectie Resource Summary van het venster **groep** . Als de groep is gemaakt op basis van een aangepaste installatie kopie, wordt deze ingesteld op een **aangepaste VM-installatie kopie**.

Alle aangepaste installatie kopieën die aan een groep zijn gekoppeld, worden weer gegeven in het **Eigenschappen** venster van de groep.

## <a name="considerations-for-large-pools"></a>Overwegingen voor grote Pools

Als u van plan bent een pool met honderden Vm's of meer te maken met behulp van een aangepaste installatie kopie, is het belang rijk dat u de voor gaande richt lijnen volgt voor het gebruik van een installatie kopie die is gemaakt van een VM-moment opname.

Houd ook rekening met de volgende overwegingen:

- **Grootte limieten** : batch beperkt de pool grootte tot 2500 toegewezen reken knooppunten of de knoop punten met een lage prioriteit van 1000 wanneer u een aangepaste installatie kopie gebruikt.

  Als u dezelfde installatie kopie (of meerdere installatie kopieën op basis van dezelfde onderliggende moment opname) gebruikt om meerdere groepen te maken, kunnen de totale reken knooppunten in de groepen niet groter zijn dan de voor gaande limieten. Het is niet raadzaam een afbeelding of de onderliggende moment opname te gebruiken voor meer dan één groep.

  Limieten kunnen worden verminderd als u de groep configureert met [binnenkomende NAT-Pools](pool-endpoint-configuration.md).

- **Grootte van de time-out wijzigen** : als uw pool een vast aantal knoop punten bevat (niet automatisch schalen), verhoogt u de eigenschap resizeTimeout van de groep naar een waarde van 20-30 minuten. Als uw pool niet binnen de time-outperiode de doel grootte bereikt, moet u een andere grootte voor het [formaat van de bewerking](/rest/api/batchservice/pool/resize)uitvoeren.

  Als u een pool plant met meer dan 300 reken knooppunten, moet u mogelijk meerdere malen het formaat van de groep wijzigen om de doel grootte te bereiken.
  
Met de [Galerie gedeelde afbeeldingen](batch-sig-images.md)kunt u met uw aangepaste installatie kopieën grote groepen maken, samen met meer gedeelde afbeeldings replica's. Als u gedeelde installatie kopieën gebruikt, is de tijd die nodig is om de groep te bereiken, tot 25% sneller en is de latentie van de virtuele machine niet langer dan 30%.

## <a name="considerations-for-using-packer"></a>Overwegingen voor het gebruik van Packer

Het maken van een beheerde installatie kopie bron rechtstreeks met de Packer kan alleen worden uitgevoerd met de modus voor de gebruikers abonnement-batch accounts. Voor accounts van de batch-service modus moet u eerst een VHD maken en vervolgens de VHD importeren in een beheerde installatie kopie bron. Afhankelijk van de pool toewijzings modus (gebruikers abonnement of batch-service), kunnen de stappen voor het maken van een beheerde afbeeldings resource verschillen.

Zorg ervoor dat de resource die wordt gebruikt om de beheerde installatie kopie te maken, bestaat voor de levens duur van elke groep die verwijst naar de aangepaste installatie kopie. Als u dit niet doet, kan dit leiden tot geheugen toewijzings fouten en/of het formaat van fouten.

Als de afbeelding of de onderliggende resource wordt verwijderd, wordt er mogelijk een fout bericht weer gegeven die vergelijkbaar is met: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Als u deze fout ontvangt, controleert u of de onderliggende resource niet is verwijderd.

Zie [een Linux-installatie kopie bouwen met een Packer](../virtual-machines/linux/build-image-with-packer.md) of [een Windows-installatie kopie bouwen met Packer](../virtual-machines/windows/build-image-with-packer.md)voor meer informatie over het gebruik van Packer voor het maken van een virtuele machine.

## <a name="next-steps"></a>Volgende stappen

Zie [grootschalige parallelle reken oplossingen ontwikkelen met batch](batch-api-basics.md)voor een uitgebreid overzicht van batch.
