---
title: Include-bestand
description: Include-bestand
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 0d5947f669b600b544cd7e5265e2cce8de118374
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788983"
---
## <a name="create-an-image-gallery"></a>Een galerie met installatie kopieën maken

Een galerie met installatie kopieën is de primaire resource die wordt gebruikt voor het inschakelen van het delen van afbeeldingen. Toegestane tekens voor de naam van de galerie bestaan uit hoofd letters of kleine letters, cijfers, punten en punten. De naam van de galerie mag geen streepjes bevatten.  Galerie namen moeten uniek zijn binnen uw abonnement. 

In het volgende voor beeld wordt een galerie gemaakt met de naam *myGallery* in de resource groep *myGalleryRG* .

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Gebruik de **Galerie met gedeelde installatie kopieën** in het zoekvak en selecteer **gedeelde installatie kopie galerie** in de resultaten.
1. Klik op de pagina **Galerie met gedeelde afbeeldingen** op **toevoegen**.
1. Selecteer op de pagina **Galerie met gedeelde afbeeldingen maken** het juiste abonnement.
1. Selecteer in **resource groep**de optie **nieuwe maken** en typ *myGalleryRG* voor de naam.
1. Typ in **naam** *myGallery* voor de naam van de galerie.
1. De standaard waarde voor de **regio**behouden.
1. U kunt een korte beschrijving van de galerie typen, zoals de galerie met afbeeldingen die u wilt *testen.* en klik vervolgens op **beoordeling + maken**.
1. Nadat de validatie is geslaagd, selecteert u **maken**.
1. Wanneer de implementatie is voltooid, selecteert **u naar resource gaan**.


## <a name="create-an-image-definition"></a>Een definitie van een installatie kopie maken 

Afbeeldings definities maken een logische groepering voor installatie kopieën. Ze worden gebruikt voor het beheren van informatie over de installatie kopieën die in deze versies worden gemaakt. Definitie namen van afbeeldingen kunnen bestaan uit hoofd letters, kleine letters, cijfers, punten, streepjes en punten. Zie [afbeeldings definities](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)voor meer informatie over de waarden die u kunt opgeven voor de definitie van een installatie kopie.

Maak de definitie van de galerie-afbeelding in uw galerie. In dit voor beeld heeft de galerie-afbeelding de naam *myImageDefinition*.

1. Selecteer op de pagina voor de nieuwe galerie met installatie kopieën **een nieuwe definitie van de installatie kopie toevoegen** aan de bovenkant van de pagina. 
1. Selecteer in de **Galerie nieuwe definitie van installatie kopie toevoegen aan gedeelde installatie kopie**voor **regio**de optie *VS Oost*.
1. Typ *myImageDefinition*bij **naam van definitie van installatie kopie**.
1. Voor **besturings systeem**selecteert u de juiste optie op basis van de bron-VM.  
1. Voor het **genereren van vm's**selecteert u de optie op basis van de bron-VM. In de meeste gevallen is dit *gen 1*. Zie [ondersteuning voor virtuele machines van de tweede generatie](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)voor meer informatie.
1. Selecteer de optie voor de status van het **besturings systeem**op basis van de bron-VM. Zie voor meer informatie [gegeneraliseerde en gespecialiseerde](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images).
1. Typ *myPublisher*voor **Publisher**. 
1. Typ *myOffer*voor **aanbieding**.
1. Typ *mySKU*voor **SKU**.
1. Wanneer u klaar bent, selecteert u **controleren + maken**.
1. Wanneer de definitie van de installatie kopie wordt door gegeven, selecteert u **maken**.
1. Wanneer de implementatie is voltooid, selecteert **u naar resource gaan**.


## <a name="create-an-image-version"></a>De versie van een installatiekopie maken

Een installatie kopie versie maken op basis van een beheerde installatie kopie. In dit voor beeld is de versie van de installatie kopie *1.0.0* en wordt deze gerepliceerd naar zowel *West-Centraal VS* -en *Zuid-Centraal VS* -data centers. Houd er rekening mee dat u bij het kiezen van doel regio's voor replicatie ook de *bron* regio moet toevoegen als doel voor replicatie.

Toegestane tekens voor de versie van de installatie kopie zijn getallen en punten. Getallen moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*. *MinorVersion*. *Patch*.

De stappen voor het maken van een installatie kopie versie zijn iets anders, afhankelijk van het feit of de bron een gegeneraliseerde installatie kopie of een moment opname van een gespecialiseerde virtuele machine is. 

### <a name="option-generalized"></a>Optie: gegeneraliseerd

1. Selecteer op de pagina voor de definitie van de installatie kopie de optie **versie toevoegen** aan de bovenkant van de pagina.
1. Selecteer in **regio**de regio waar uw beheerde installatie kopie wordt opgeslagen. Installatie kopie versies moeten worden gemaakt in dezelfde regio als de beheerde installatie kopie die ze hebben gemaakt.
1. Typ *1.0.0*voor **naam**. De naam van de installatie kopie versie *moet volgen.* *secundair*. de indeling van de *patch* met gehele getallen. 
1. Selecteer in **bron installatie kopie**de door de bron beheerde installatie kopie in de vervolg keuzelijst.
1. Laat de standaard waarde *Nee*staan in **uitgesloten van laatste**.
1. Selecteer voor **einde levens datum**een datum in de kalender die in de toekomst een paar maanden is.
1. In **replicatie**, moet u het **aantal standaard replica's** op 1 laten staan. U moet naar de bron regio repliceren, dus verlaat de eerste replica als de standaard waarde en kies vervolgens een tweede replica regio om *VS-Oost*te zijn.
1. Wanneer u klaar bent, selecteert u **controleren + maken**. De configuratie wordt door Azure gevalideerd.
1. Wanneer de versie van de installatie kopie wordt goedgekeurd, selecteert u **maken**.
1. Wanneer de implementatie is voltooid, selecteert **u naar resource gaan**.

Het kan even duren om de installatie kopie te repliceren naar alle doel regio's.

### <a name="option-specialized"></a>Optie: speciaal

1. Selecteer op de pagina voor de definitie van de installatie kopie de optie **versie toevoegen** aan de bovenkant van de pagina.
1. Selecteer in **regio**de regio waar uw moment opname is opgeslagen. Installatie kopie versies moeten worden gemaakt in dezelfde regio als de bron van waaruit ze zijn gemaakt.
1. Typ *1.0.0*voor **naam**. De naam van de installatie kopie versie *moet volgen.* *secundair*. de indeling van de *patch* met gehele getallen. 
1. Selecteer in de **moment opname van de besturingssysteem schijf**de moment opname van de bron-vm in de vervolg keuzelijst. Als de bron-VM een gegevens schijf bevat die u wilt toevoegen, selecteert u het juiste **LUN** -nummer in de vervolg keuzelijst en selecteert u vervolgens de moment opname van de gegevens schijf voor de **moment opname van de gegevens schijf**. 
1. Laat de standaard waarde *Nee*staan in **uitgesloten van laatste**.
1. Selecteer voor **einde levens datum**een datum in de kalender die in de toekomst een paar maanden is.
1. In **replicatie**, moet u het **aantal standaard replica's** op 1 laten staan. U moet naar de bron regio repliceren, dus verlaat de eerste replica als de standaard waarde en kies vervolgens een tweede replica regio om *VS-Oost*te zijn.
1. Wanneer u klaar bent, selecteert u **controleren + maken**. De configuratie wordt door Azure gevalideerd.
1. Wanneer de versie van de installatie kopie wordt goedgekeurd, selecteert u **maken**.
1. Wanneer de implementatie is voltooid, selecteert **u naar resource gaan**.

## <a name="share-the-gallery"></a>De galerie delen

U wordt aangeraden toegang te delen op het niveau van de galerie met installatie kopieën. Hieronder vindt u instructies voor het delen van de galerie die u zojuist hebt gemaakt.

1. Open [Azure Portal](https://portal.azure.com).
1. Selecteer **resource groepen**in het menu aan de linkerkant. 
1. Selecteer **myGalleryRG**in de lijst met resource groepen. De Blade voor de resource groep wordt geopend.
1. Selecteer **toegangs beheer (IAM)** in het menu aan de linkerkant van de pagina **myGalleryRG** . 
1. Selecteer onder **een roltoewijzing toevoegen de**optie **toevoegen**. Het deel venster **toewijzing van een rol toevoegen** wordt geopend. 
1. Selecteer onder **rol** **lezer**.
1. Onder **toegang toewijzen aan**, de standaard waarde van de **gebruiker, groep of Service-Principal van Azure AD**laten staan.
1. Typ onder **selecteren**het e-mail adres van de persoon die u wilt uitnodigen.
1. Als de gebruiker zich buiten uw organisatie bevindt, wordt het bericht weer gegeven **dat deze gebruiker een e-mail ontvangt waarmee ze kunnen samen werken met micro soft.** Selecteer de gebruiker met het e-mail adres en klik vervolgens op **Opslaan**.

Als de gebruiker zich buiten uw organisatie bevindt, krijgen ze een e-mail uitnodiging om lid te worden van de organisatie. De gebruiker moet de uitnodiging accepteren. vervolgens kunnen ze de galerie en alle afbeeldings definities en-versies in hun lijst met resources zien.

