---
title: Gedeelde installatie kopieën voor virtuele Azure-machines voor Linux maken met behulp van de portal
description: Meer informatie over het gebruik van Azure Portal voor het maken en delen van installatie kopieën van virtuele machines.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 9dff10848c42fd730f0bc77fd325f00cec554c9d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892869"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Een galerie met gedeelde installatie kopieën maken met behulp van de Azure Portal

Een [Galerie met gedeelde afbeeldingen](shared-image-galleries.md) vereenvoudigt het delen van aangepaste afbeeldingen in uw organisatie. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatie kopieën kunnen worden gebruikt om implementatie taken te Boots trapn, zoals het vooraf laden van toepassingen, toepassings configuraties en andere besturingssysteem configuraties. 

Met de galerie voor gedeelde afbeeldingen kunt u uw aangepaste VM-installatie kopieën delen met anderen in uw organisatie, binnen of tussen verschillende regio's binnen een AAD-Tenant. Kies welke installatie kopieën u wilt delen, in welke regio's u ze beschikbaar wilt maken en met wie u wilt delen. U kunt meerdere galerieën maken zodat u gedeelde installatie kopieën logisch kunt groeperen. 

De galerie is een resource op het hoogste niveau die volledige op rollen gebaseerd toegangs beheer (RBAC) biedt. Installatie kopieën kunnen versie nummer hebben en u kunt ervoor kiezen om elke installatie kopie versie te repliceren naar een andere set Azure-regio's. De galerie werkt alleen met beheerde installatie kopieën.

De functie gedeelde installatie kopie galerie heeft meerdere bron typen. We gebruiken of maken deze in dit artikel:

| Bron | Beschrijving|
|----------|------------|
| **Beheerde installatie kopie** | Een basis installatie kopie die alleen kan worden gebruikt of gebruikt om een **installatie kopie versie** in een galerie met installatie kopieën te maken. Beheerde installatie kopieën worden gemaakt op basis van [gegeneraliseerde](shared-image-galleries.md#generalized-and-specialized-images) vm's. Een beheerde installatie kopie is een speciaal type VHD dat kan worden gebruikt om meerdere virtuele machines te maken en kan nu worden gebruikt om versies van gedeelde installatie kopieën te maken. |
| **Snapshot** | Een kopie van een VHD die kan worden gebruikt om een **installatie kopie versie**te maken. Moment opnamen kunnen worden gemaakt op basis van een [gespecialiseerde](shared-image-galleries.md#generalized-and-specialized-images) virtuele machine (een die niet is gegeneraliseerd) en vervolgens alleen worden gebruikt of met moment opnamen van gegevens schijven, om een gespecialiseerde installatie kopie versie te maken.
| **Galerie met installatie kopieën** | Net als de Azure Marketplace is een **afbeeldings galerie** een opslag plaats voor het beheren en delen van installatie kopieën, maar u bepaalt wie toegang heeft. |
| **Definitie van installatie kopie** | Installatie kopieën worden in een galerie gedefinieerd en bevatten informatie over de installatie kopie en vereisten voor het gebruik ervan binnen uw organisatie. U kunt informatie toevoegen zoals of de installatie kopie wordt gegeneraliseerd of gespecialiseerd, het besturings systeem, de minimale en maximale geheugen vereisten en release opmerkingen. Het is een definitie van een type installatie kopie. |
| **Versie van installatie kopie** | Een **installatie kopie versie** is wat u gebruikt om een virtuele machine te maken wanneer u een galerie gebruikt. U kunt meerdere versies van een installatie kopie naar behoefte hebben voor uw omgeving. Net als bij een beheerde installatie kopie wordt de versie van de installatie kopie gebruikt voor het maken van nieuwe schijven voor de virtuele machine wanneer u een **installatie kopie** gebruikt voor het maken van een virtuele machine. Installatie kopie versies kunnen meerdere keren worden gebruikt. |

<br>

> [!IMPORTANT]
> Gespecialiseerde installatie kopieën zijn momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
>
> **Bekende preview-beperkingen** Vm's kunnen alleen worden gemaakt op basis van gespecialiseerde installatie kopieën met behulp van de portal of API. De is geen CLI-of Power Shell-ondersteuning voor de preview-versie.


## <a name="before-you-begin"></a>Voordat u begint

Om het voor beeld in dit artikel te volt ooien, moet u beschikken over een bestaande beheerde installatie kopie van een gegeneraliseerde virtuele machine of een moment opname van een gespecialiseerde VM. U kunt [zelf studie volgen: Maak een aangepaste installatie kopie van een Azure-VM met Azure PowerShell](tutorial-custom-images.md) om een beheerde installatie kopie te maken of [Maak een moment opname](../windows/snapshot-copy-managed-disk.md) voor een gespecialiseerde virtuele machine. Voor zowel beheerde installatie kopieën als moment opnamen mag de grootte van de gegevens schijf niet groter zijn dan 1 TB.

Wanneer u dit artikel doorwerkt, moet u de namen van de resource groep en de VM vervangen, indien nodig.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>Virtuele machines maken 

U kunt nu een of meer nieuwe virtuele machines maken. In dit voor beeld wordt een VM gemaakt met de naam *myVMfromImage*in de *MYRESOURCEGROUP* in het *VS-Oost* -Data Center.

1. Ga naar de definitie van uw installatie kopie. U kunt het resource filter gebruiken om alle beschik bare afbeeldings definities weer te geven.
1. Selecteer op de pagina voor de definitie van de installatie kopie de optie **VM maken** in het menu boven aan de pagina.
1. Selecteer voor **resource groep**de optie **nieuwe maken** en typ *myResourceGroup* voor de naam.
1. Typ *myVM*in de naam van de **virtuele machine**.
1. Selecteer voor **regio** *VS Oost*.
1. Voor **beschikbaarheids opties**moet u de standaard waarde voor de *redundantie van de infra structuur niet*behouden.
1. De waarde voor de **installatie kopie** wordt automatisch gevuld met de versie van de `latest`-afbeelding als u bent begonnen met de pagina voor de definitie van de installatie kopie.
1. Kies bij **grootte**een VM-grootte in de lijst met beschik bare grootten en kies vervolgens **selecteren**.
1. Als de bron-VM is gegeneraliseerd onder **Administrator-account**, voert u uw **gebruikers naam** en **open bare SSH-sleutel**in. Als de bron-VM speciaal was, worden deze opties grijs weer gegeven, omdat de gegevens van de bron-VM worden gebruikt.
1. Als u externe toegang tot de virtuele machine wilt toestaan, klikt u onder **open bare binnenkomende poorten**op **geselecteerde poorten toestaan** en selecteert u **SSH (22)** in de vervolg keuzelijst. Als u externe toegang tot de virtuele machine niet wilt toestaan, laat u **niets** geselecteerd voor **open bare binnenkomende poorten**.
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

