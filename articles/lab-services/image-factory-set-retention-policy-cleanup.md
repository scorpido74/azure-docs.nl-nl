---
title: Bewaarbeleid instellen in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het configureren van een bewaarbeleid, het opruimen van de fabriek en het stoppen van oude afbeeldingen van DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a472c500ee6b968b1459e65e49a352b81e5ea6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759411"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Bewaarbeleid instellen in Azure DevTest Labs
Dit artikel behandelt het instellen van een bewaarbeleid, het opruimen van de fabriek en het terugtrekken van oude afbeeldingen van alle andere DevTest Labs in de organisatie. 

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u deze artikelen hebt gevolgd voordat u verder gaat:

- [Een fabrieksinstallatiekopie maken](image-factory-create.md)
- [Een fabrieksinstallatiekopie uitvoeren vanuit Azure DevOps](image-factory-set-up-devops-lab.md)
- [Aangepaste installatiekopieën opslaan en distribueren naar meerdere labs](image-factory-save-distribute-custom-images.md)

De volgende items moeten al op hun plaats zijn:

- Een lab voor de imagefabriek in Azure DevTest Labs
- Een of meer doel Azure DevTest Labs waar de fabriek gouden beelden zal distribueren
- Een Azure DevOps-project dat wordt gebruikt om de afbeeldingsfabriek te automatiseren.
- Broncodelocatie met de scripts en configuratie (in ons voorbeeld in hetzelfde DevOps-project hierboven gebruikt)
- Een builddefinitie voor het orkestreren van de Azure Powershell-taken
 
## <a name="setting-the-retention-policy"></a>Het bewaarbeleid instellen
Voordat u de opschonestappen configureert, bepaalt u hoeveel historische afbeeldingen u wilt behouden in de DevTest Labs. Wanneer u het artikel [Een afbeeldingsfabriek uitvoeren van Azure DevOps](image-factory-set-up-devops-lab.md) hebt gevolgd, hebt u verschillende buildvariabelen geconfigureerd. Een van hen was **ImageRetention**. U stelt deze `1`variabele in op , wat betekent dat de DevTest Labs geen geschiedenis van aangepaste afbeeldingen zal onderhouden. Alleen de meest recente gedistribueerde afbeeldingen zullen beschikbaar zijn. Als u deze `2`variabele wijzigt in , wordt de meest recente gedistribueerde afbeelding plus de vorige weergegeven. U deze waarde instellen om het aantal historische afbeeldingen te definiëren dat u wilt behouden in uw DevTest Labs.

## <a name="cleaning-up-the-factory"></a>Het schoonmaken van de fabriek
De eerste stap in het schoonmaken van de fabriek is het verwijderen van de gouden Beeld VM's uit de beeldfabriek. Er is een script om deze taak te doen, net als onze vorige scripts. De eerste stap is het toevoegen van een andere **Azure Powershell-taak** aan de builddefinitie zoals weergegeven in de volgende afbeelding:

![PowerShell-stap](./media/set-retention-policy-cleanup/powershell-step.png)

Zodra u de nieuwe taak in de lijst hebt, selecteert u het item en vult u alle details in zoals in de volgende afbeelding wordt weergegeven:

![Oude afbeeldingen opruimen PowerShell-taak](./media/set-retention-policy-cleanup/configure-powershell-task.png)

De scriptparameters `-DevTestLabName $(devTestLabName)`zijn: .

## <a name="retire-old-images"></a>Oude afbeeldingen met pensioen laten gaan 
Met deze taak worden alle oude afbeeldingen verwijderd, zodat alleen een geschiedenis overeenkomt met de variabele **ImageRetention build.** Voeg een extra **Azure Powershell-buildtaak** toe aan onze builddefinitie. Zodra deze is toegevoegd, selecteert u de taak en vult u de details in zoals weergegeven in de volgende afbeelding: 

![Oude afbeeldingen met pensioen zetten PowerShell-taak](./media/set-retention-policy-cleanup/retire-old-image-task.png)

De scriptparameters zijn:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Wachtrij de build
Nu u de builddefinitie hebt voltooid, moet u een nieuwe build in de wachtrij plaatsen om ervoor te zorgen dat alles werkt. Nadat de build is voltooid, worden de nieuwe aangepaste afbeeldingen weergegeven in het doellab en als u het laboratorium voor de afbeeldingsfabriek controleert, ziet u geen ingerichte VM's. Bovendien als u in de rij verder bouwt, ziet u de opruimtaken taken met pensioen oude aangepaste beelden uit de DevTest Labs in overeenstemming met de retentiewaarde in de build variabelen.

> [!NOTE]
> Als u de buildpijplijn aan het einde van het laatste artikel in de reeks hebt uitgevoerd, verwijdert u handmatig de virtuele machines die in het laboratorium voor de afbeeldingsfabriek zijn gemaakt voordat u een nieuwe build in de rij staat.  De handmatige opruimstap is alleen nodig terwijl we alles instellen en controleren of het werkt.



## <a name="summary"></a>Samenvatting
Nu heb je een draaiende beeldfabriek die op aanvraag aangepaste afbeeldingen kan genereren en distribueren naar je labs. Op dit punt, het is gewoon een kwestie van het krijgen van uw beelden goed ingesteld en het identificeren van de doellaboratoria. Zoals vermeld in het vorige artikel, de **Labs.json** bestand in uw **configuratie** map geeft aan welke afbeeldingen beschikbaar moeten worden gesteld in elk van de doellabs. Terwijl u andere DevTest Labs aan uw organisatie toevoegt, hoeft u alleen maar een vermelding in de Labs.json toe te voegen voor het nieuwe lab.

Het toevoegen van een nieuwe afbeelding aan uw fabriek is ook eenvoudig. Wanneer u een nieuwe afbeelding in uw fabriek wilt opnemen, opent u de [Azure-portal,](https://portal.azure.com)navigeert u naar uw factory DevTest Labs, selecteert u de knop om een VM toe te voegen en kiest u de gewenste marketplace-afbeelding en artefacten. In plaats van de knop **Maken** te selecteren om de nieuwe vm te maken, selecteert u **Sjabloon Azure Resource Manager weergeven**en slaat u de sjabloon op als een .json-bestand ergens in de map **GoldenImages** in uw opslagplaats. De volgende keer dat u uw afbeeldingsfabriek uitvoert, wordt uw aangepaste afbeelding gemaakt.


## <a name="next-steps"></a>Volgende stappen
1. [Plan uw build/release](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) om de afbeeldingsfabriek periodiek uit te voeren. Het vernieuwt uw in de fabriek gegenereerde afbeeldingen regelmatig.
2. Maak meer gouden beelden voor uw fabriek. U ook overwegen [artefacten](devtest-lab-artifact-author.md) te maken om extra onderdelen van uw VM-installatietaken te scripten en de artefacten in uw fabrieksafbeeldingen op te nemen.
4. Maak een [aparte build/release](/azure/devops/pipelines/overview?view=azure-devops-2019) om het **DistributeImages-script** afzonderlijk uit te voeren. U dit script uitvoeren wanneer u wijzigingen aanbrengt in Labs.json en afbeeldingen laten kopiëren naar doellabs zonder dat u alle afbeeldingen opnieuw hoeft te maken.

