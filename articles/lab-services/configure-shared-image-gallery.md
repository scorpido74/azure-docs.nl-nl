---
title: Een gedeelde afbeeldingsgalerie configureren in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het configureren van een gedeelde afbeeldingsgalerie in Azure DevTest Labs
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 9593d60f76802cd515ca85616bce028cf3aa0d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589314"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Een gedeelde galerie met installatiekopieën configureren in Azure DevTest Labs
DevTest Labs ondersteunt nu de functie [Shared Image Gallery.](../virtual-machines/windows/shared-image-galleries.md) Het stelt labgebruikers in staat om toegang te krijgen tot afbeeldingen vanaf een gedeelde locatie terwijl ze labbronnen maken. Het helpt u ook structuur en organisatie op te bouwen rond uw op maat gemaakte VM-afbeeldingen. De functie Gedeelde afbeeldingsgalerie ondersteunt:

- Beheerde globale replicatie van afbeeldingen
- Versiebeheer en groepering van afbeeldingen voor eenvoudiger beheer
- Maak uw afbeeldingen zeer beschikbaar met ZRS-accounts (Zone Redundant Storage) in regio's die beschikbaarheidszones ondersteunen. ZRS biedt een betere veerkracht tegen zonale mislukkingen.
- Delen tussen abonnementen en zelfs tussen tenants, met behulp van op rollen gebaseerd toegangscontrole (RBAC).

Zie [Documentatie van gedeelde afbeeldingengalerij](../virtual-machines/windows/shared-image-galleries.md)voor meer informatie . 
 
Als u een groot aantal beheerde afbeeldingen hebt die u moet onderhouden en deze beschikbaar wilt stellen in uw hele bedrijf, u een gedeelde afbeeldingsgalerie gebruiken als opslagplaats waarmee u uw afbeeldingen eenvoudig bijwerken en delen. Als eigenaar van een lab u een bestaande gedeelde afbeeldingsgalerie aan uw lab koppelen. Zodra deze galerij is aangesloten, kunnen labgebruikers machines maken op de nieuwste afbeeldingen. Een belangrijk voordeel van deze functie is dat DevTest Labs nu het voordeel kan halen van het delen van afbeeldingen in laboratoria, verschillende abonnementen en in verschillende regio's. 

> [!NOTE]
> Zie [Facturering voor gedeelde afbeeldingsgalerie voor](../virtual-machines/windows/shared-image-galleries.md#billing)meer informatie over de kosten die zijn gekoppeld aan de gedeelde imagegalerie.

## <a name="considerations"></a>Overwegingen
- U slechts één gedeelde afbeeldingsgalerie tegelijk aan een lab koppelen. Als u een andere galerie wilt koppelen, moet u de bestaande galerie loskoppelen en een andere koppelen. 
- DevTest Labs ondersteunt momenteel alleen gegeneraliseerde afbeeldingen met gedeelde afbeeldingen.
- DevTest Labs ondersteunt momenteel geen ondersteuning voor het uploaden van afbeeldingen naar de galerij via het lab. 
- Tijdens het maken van een virtuele machine met behulp van een gedeelde afbeelding galerij afbeelding, DevTest Labs maakt altijd gebruik van de nieuwste gepubliceerde versie van deze afbeelding. Als een afbeelding echter meerdere versies heeft, kan de gebruiker ervoor kiezen om een machine te maken vanaf een eerdere versie door naar het tabblad Geavanceerde instellingen te gaan tijdens het maken van virtuele machines.  
- Hoewel DevTest Labs automatisch een beste poging doet om ervoor te zorgen dat gedeelde afbeeldingsgalerie afbeeldingen repliceert naar de regio waarin het Lab bestaat, is het niet altijd mogelijk. Om te voorkomen dat gebruikers problemen hebben met het maken van VM's van deze afbeeldingen, moet u ervoor zorgen dat de afbeeldingen al worden gerepliceerd naar de regio van het lab."

## <a name="use-azure-portal"></a>Azure Portal gebruiken
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Alle services** in het linkernavigatiemenu.
1. Selecteer **DevTest Labs** uit de lijst.
1. Selecteer in de lijst met labs uw **lab.**
1. Selecteer **Configuratie en beleid** in de sectie **Instellingen** in het linkermenu.
1. Selecteer **Gedeelde afbeeldingsgalerieën** onder **Virtuele machinebases** in het linkermenu.

    ![Menu Gedeelde galerieën](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Voeg een bestaande gedeelde afbeeldingsgalerie toe aan uw lab door op de knop **Bijvoegen** te klikken en uw galerie in de vervolgkeuzelijst te selecteren.

    ![Koppelen](./media/configure-shared-image-gallery/attach-options.png)
1. Ga naar de bijgevoegde galerie en configureer uw galerie om gedeelde afbeeldingen **in te schakelen of uit te schakelen** voor het maken van vm's. Selecteer een afbeeldingsgalerie in de lijst om deze te configureren. 

    Standaard **is toestaan dat alle afbeeldingen worden gebruikt als virtuele machinebases** ingesteld op **Ja**. Dit betekent dat alle afbeeldingen die beschikbaar zijn in de bijgevoegde gedeelde afbeeldingsgalerie beschikbaar zijn voor een labgebruiker bij het maken van een nieuwe lab-vm. Als de toegang tot bepaalde afbeeldingen moet worden beperkt, **wijzigt u Toestaan dat alle afbeeldingen worden gebruikt als basis voor virtuele machines** op **Nee**en selecteert u de afbeeldingen die u wilt toestaan bij het maken van VM's en selecteert u vervolgens de knop **Opslaan.**

    ![Inschakelen of uitschakelen](./media/configure-shared-image-gallery/enable-disable.png)
1. Lab-gebruikers kunnen vervolgens een virtuele machine maken met behulp van de ingeschakelde afbeeldingen door op **+Toevoegen** te klikken en de afbeelding in de **basispagina kiezen** te vinden.

    ![Labgebruikers](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Sjabloon Azure Resource Manager gebruiken

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Een gedeelde afbeeldingsgalerie aan uw lab toevoegen
Als u een sjabloon azure resource manager gebruikt om een gedeelde afbeeldingsgalerie aan uw lab toe te voegen, moet u deze toevoegen onder het gedeelte Resources van uw resourcemanagersjabloon, zoals in het volgende voorbeeld wordt weergegeven:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

Zie deze sjabloonvoorbeelden voor Resource Manager voor een volledig voorbeeld van resourcebeheer in onze openbare GitHub-opslagplaats: [Een gedeelde afbeeldingsgalerie configureren terwijl u een lab maakt.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)

## <a name="use-api"></a>API gebruiken

### <a name="shared-image-galleries---create-or-update"></a>Gedeelde afbeeldingsgalerieën - maken of bijwerken

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="shared-image-galleries-images---list"></a>Afbeeldingen van gedeelde afbeeldingen van galerieën - Lijst 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende artikelen over het maken van een vm met behulp van een afbeelding uit de bijgevoegde gedeelde afbeeldingsgalerie: [Een vm maken met een gedeelde afbeelding uit de galerie](add-vm-use-shared-image.md)
