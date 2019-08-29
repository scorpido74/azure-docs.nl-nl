---
title: Gedeelde installatie kopieën voor virtuele Azure-machines maken voor Windows met behulp van de portal | Microsoft Docs
description: Meer informatie over het gebruik van Azure Portal voor het maken en delen van installatie kopieën van virtuele machines.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: e04cbd4750a97857166eb5939045bdb8ace1b426
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102384"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Een galerie met gedeelde installatie kopieën maken met behulp van de Azure Portal

Een [Galerie met gedeelde afbeeldingen](shared-image-galleries.md) vereenvoudigt het delen van aangepaste afbeeldingen in uw organisatie. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatie kopieën kunnen worden gebruikt om implementatie taken te Boots trapn, zoals het vooraf laden van toepassingen, toepassings configuraties en andere besturingssysteem configuraties. 

Met de galerie voor gedeelde afbeeldingen kunt u uw aangepaste VM-installatie kopieën delen met anderen in uw organisatie, binnen of tussen verschillende regio's binnen een AAD-Tenant. Kies welke installatie kopieën u wilt delen, in welke regio's u ze beschikbaar wilt maken en met wie u wilt delen. U kunt meerdere galerieën maken zodat u gedeelde installatie kopieën logisch kunt groeperen. 

De galerie is een resource op het hoogste niveau die volledige op rollen gebaseerd toegangs beheer (RBAC) biedt. Installatie kopieën kunnen versie nummer hebben en u kunt ervoor kiezen om elke installatie kopie versie te repliceren naar een andere set Azure-regio's. De galerie werkt alleen met beheerde installatie kopieën.

De functie gedeelde installatie kopie galerie heeft meerdere bron typen. We gebruiken of maken deze in dit artikel:

| Resource | Description|
|----------|------------|
| **Beheerde installatie kopie** | Dit is een basis installatie kopie die alleen kan worden gebruikt of gebruikt om een **installatie kopie versie** in een galerie met installatie kopieën te maken. Beheerde installatie kopieën worden gemaakt op basis van gegeneraliseerde Vm's. Een beheerde installatie kopie is een speciaal type VHD dat kan worden gebruikt om meerdere virtuele machines te maken en kan nu worden gebruikt om versies van gedeelde installatie kopieën te maken. |
| **Galerie met installatie kopieën** | Net als de Azure Marketplace is een **afbeeldings galerie** een opslag plaats voor het beheren en delen van installatie kopieën, maar u bepaalt wie toegang heeft. |
| **Definitie van installatie kopie** | Installatie kopieën worden in een galerie gedefinieerd en bevatten informatie over de installatie kopie en de vereisten voor het intern gebruik. Dit betekent of de installatie kopie Windows of Linux, release opmerkingen en minimale en maximale geheugen vereisten is. Het is een definitie van een type installatie kopie. |
| **Versie van installatie kopie** | Een **installatie kopie versie** is wat u gebruikt om een virtuele machine te maken wanneer u een galerie gebruikt. U kunt meerdere versies van een installatie kopie naar behoefte hebben voor uw omgeving. Net als bij een beheerde installatie kopie wordt de versie van de installatie kopie gebruikt voor het maken van nieuwe schijven voor de virtuele machine wanneer u een **installatie kopie** gebruikt voor het maken van een virtuele machine. Installatie kopie versies kunnen meerdere keren worden gebruikt. |


## <a name="before-you-begin"></a>Voordat u begint

Om het voor beeld in dit artikel te volt ooien, moet u een bestaande beheerde installatie kopie hebben. U kunt zelf [studie volgen: Maak een aangepaste installatie kopie van een Azure-VM](tutorial-custom-images.md) met Azure PowerShell om er een te maken, indien nodig. Als de beheerde installatie kopie een gegevens schijf bevat, kan de grootte van de gegevens schijf niet groter zijn dan 1 TB.

Wanneer u dit artikel doorwerkt, moet u de namen van de resource groep en de VM vervangen, indien nodig.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms-from-an-image"></a>Vm's maken op basis van een installatie kopie

Zodra de versie van de installatie kopie is voltooid, kunt u een of meer nieuwe virtuele machines maken. 

> [!IMPORTANT]
> U kunt de portal niet gebruiken om een virtuele machine te implementeren op basis van een installatie kopie in een andere Azure-Tenant. Als u een virtuele machine wilt maken op basis van een installatie kopie die tussen tenants wordt gedeeld, moet u de [Azure cli](../linux/shared-images.md#create-a-vm) of [Power shell](shared-images.md#create-vms-from-an-image)gebruiken.

In dit voor beeld wordt een VM gemaakt met de naam *myVMfromImage*in de *MYRESOURCEGROUP* in het *VS-Oost* -Data Center.

1. Selecteer op de pagina voor de versie van de installatie kopie de optie **VM maken** in het menu boven aan de pagina.
1. Selecteer voor **resource groep**de optie **nieuwe maken** en typ *myResourceGroup* voor de naam.
1. Typ *myVM*in de naam van de **virtuele machine**.
1. Selecteer voor **regio** *VS Oost*.
1. Voor **beschikbaarheids opties**moet u de standaard waarde voor de redundantie van de *infra structuur niet*behouden.
1. De waarde voor de **afbeelding** moet automatisch worden ingevuld als u vanaf de pagina bent begonnen voor de versie van de installatie kopie.
1. Kies voor **grootte**een VM-grootte in de lijst met beschik bare grootten en klik vervolgens op selecteren.
1. Geeft onder **Administrator-account**, een gebruikersnaam op, bijvoorbeeld *azureuser*, en een wachtwoord. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. Als u externe toegang tot de virtuele machine wilt toestaan, klikt u onder **open bare binnenkomende poorten**op **geselecteerde poorten toestaan** en selecteert u vervolgens **RDP (3389)** in de vervolg keuzelijst. Als u externe toegang tot de virtuele machine niet wilt toestaan, laat u **niets** geselecteerd voor **open bare binnenkomende poorten**.
1. Wanneer u klaar bent, selecteert u de knop **controleren + maken** onder aan de pagina.
1. Nadat de validatie van de virtuele machine is geslaagd, selecteert u onder aan de pagina **maken** om de implementatie te starten.



## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. Wanneer u dit wilt doen, selecteert u de resourcegroep voor de virtuele machine en selecteert u **Verwijderen**. Vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

Als u afzonderlijke resources wilt verwijderen, moet u deze in omgekeerde volg orde verwijderen. Als u bijvoorbeeld een definitie van een installatie kopie wilt verwijderen, moet u alle installatie kopieën verwijderen die zijn gemaakt op basis van die installatie kopie.

## <a name="next-steps"></a>Volgende stappen

U kunt ook een resource voor de galerie met gedeelde afbeeldingen maken met behulp van sjablonen. Er zijn verschillende Azure Quick Start-sjablonen beschikbaar: 

- [Een galerie met gedeelde afbeeldingen maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een definitie van een installatie kopie maken in een galerie met gedeelde afbeeldingen](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een installatie kopie versie maken in een galerie met gedeelde afbeeldingen](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken op basis van de installatie kopie versie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Zie het [overzicht](shared-image-galleries.md)voor meer informatie over gedeelde afbeeldings galerieën. Als u problemen ondervindt, raadpleegt u [problemen met de galerie met gedeelde afbeeldingen oplossen](troubleshooting-shared-images.md).

