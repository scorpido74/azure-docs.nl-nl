---
title: Gedeelde Azure Linux VM-afbeeldingen maken met behulp van de portal
description: Meer informatie over het gebruik van Azure-portal om virtuele machineafbeeldingen van Linux te maken en te delen.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: article
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 74c87d589f1c50551ac5685fe0fa126a82bffbde
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758430"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Een Azure Shared Image Gallery maken met de portal

Een [gedeelde afbeeldingsgalerie](shared-image-galleries.md) vereenvoudigt het delen van aangepaste afbeeldingen in uw hele organisatie. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste afbeeldingen kunnen worden gebruikt om implementatietaken op te start zetten, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere OS-configuraties. 

Met de gedeelde afbeeldingsgalerie u uw aangepaste VM-afbeeldingen delen met anderen in uw organisatie, binnen of tussen regio's, binnen een AAD-tenant. Kies in welke afbeeldingen u wilt delen, in welke regio's u ze beschikbaar wilt maken en met wie u ze wilt delen. U meerdere galerieën maken, zodat u op logischmogelijke wijze gedeelde afbeeldingen groeperen. 

De galerie is een bron op het hoogste niveau die volledige role-based access control (RBAC) biedt. Afbeeldingen kunnen worden geversioneerd en u ervoor kiezen om elke afbeeldingsversie te repliceren naar een andere set Azure-regio's. De galerie werkt alleen met Beheerde afbeeldingen.

De functie Gedeelde afbeeldingsgalerie heeft meerdere resourcetypen. We zullen deze gebruiken of bouwen in dit artikel:

| Resource | Beschrijving|
|----------|------------|
| **Beheerde afbeelding** | Een basisafbeelding die alleen kan worden gebruikt of kan worden gebruikt om een **afbeeldingsversie** in een afbeeldingsgalerie te maken. Beheerde afbeeldingen worden gemaakt op [gealgemene](shared-image-galleries.md#generalized-and-specialized-images) VM's. Een beheerde afbeelding is een speciaal type VHD dat kan worden gebruikt om meerdere VM's te maken en kan nu worden gebruikt om versies van gedeelde afbeeldingen te maken. |
| **Momentopname** | Een kopie van een VHD die kan worden gebruikt om een **afbeeldingsversie**te maken. Momentopnamen kunnen worden genomen van een [gespecialiseerde](shared-image-galleries.md#generalized-and-specialized-images) VM (een die niet is gegeneraliseerd) dan alleen gebruikt of met snapshots van gegevensschijven, om een gespecialiseerde afbeeldingversie te maken.
| **Afbeeldingsgalerie** | Net als de Azure Marketplace is een **afbeeldingsgalerie** een opslagplaats voor het beheren en delen van afbeeldingen, maar u bepaalt wie toegang heeft. |
| **Afbeeldingsdefinitie** | Afbeeldingen worden gedefinieerd in een galerie en bevatten informatie over de afbeelding en vereisten voor het gebruik ervan binnen uw organisatie. U informatie opnemen zoals of de afbeelding algemeen of gespecialiseerd is, het besturingssysteem, minimale en maximale geheugenvereisten en releasenotes. Het is een definitie van een type beeld. |
| **Versie van installatiekopie** | Een **afbeeldingsversie** is wat u gebruikt om een vm te maken wanneer u een galerie gebruikt. U meerdere versies van een afbeelding hebben als dat nodig is voor uw omgeving. Net als een beheerde afbeelding wordt de afbeeldingsversie gebruikt om nieuwe schijven voor de virtuele machine te maken wanneer u een **afbeeldingsversie** gebruikt om een afbeeldingsversie te maken. Afbeeldingsversies kunnen meerdere keren worden gebruikt. |

<br>

> [!IMPORTANT]
> Gespecialiseerde afbeeldingen zijn momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.
>
> **Bekende preview-beperkingen** VM's kunnen alleen worden gemaakt op basis van gespecialiseerde afbeeldingen met behulp van de portal of API. Het is geen CLI of PowerShell ondersteuning voor de preview.


## <a name="before-you-begin"></a>Voordat u begint

Als u het voorbeeld in dit artikel wilt voltooien, moet u een bestaande beheerde afbeelding van een gegeneraliseerde vm of een momentopname van een gespecialiseerde vm hebben. U [Zelfstudie volgen: maak een aangepaste afbeelding van een Azure VM met Azure PowerShell](tutorial-custom-images.md) om een beheerde afbeelding te maken of [Maak een momentopname](../windows/snapshot-copy-managed-disk.md) voor een gespecialiseerde virtuele machine. Voor zowel beheerde afbeeldingen als momentopnamen mag de grootte van de gegevensschijf niet meer dan 1 TB bedragen.

Wanneer u dit artikel doorloopt, vervangt u de resourcegroep en VM-namen waar nodig.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>Virtuele machines maken 

Nu u een of meer nieuwe VM's maken. In dit voorbeeld wordt een VM met de naam *myVMfromImage*gemaakt in de *myResourceGroup* in het *datacenter in oost-VS.*

1. Ga naar uw afbeeldingsdefinitie. U het resourcefilter gebruiken om alle beschikbare afbeeldingsdefinities weer te geven.
1. Selecteer op de pagina voor uw afbeeldingsdefinitie **VM maken** in het menu boven aan de pagina.
1. Selecteer **Voor resourcegroep**De optie **Nieuw maken** en typ *myResourceGroup* voor de naam.
1. Typ *myVM*in **virtuele machinenaam**.
1. Selecteer **Voor Regio** *Oost-VS*.
1. Voor **beschikbaarheidsopties**laat u de *standaardredundantie*van de infrastructuur niet overbodig maken .
1. De waarde voor **Afbeelding** wordt `latest` automatisch gevuld met de afbeeldingsversie als u vanaf de pagina voor de afbeeldingsdefinitie bent gestart.
1. Kies **bij Grootte**een VM-grootte in de lijst met beschikbare formaten en kies Selecteer **Selecteren**.
1. Voer onder **Administrator-account,** als de bron-VM is gegeneraliseerd, uw **gebruikersnaam** en **SSH-openbare sleutel in**. Als de bron-VM is gespecialiseerd, worden deze opties grijs weergegeven omdat de informatie van de bron-VM wordt gebruikt.
1. Als u externe toegang tot de VM wilt toestaan, kiest u onder **Openbare binnenkomende poorten**de optie **Geselecteerde poorten toestaan** en selecteert u **Vervolgens SSH (22)** in de vervolgkeuzelijst. Als u geen externe toegang tot de vm wilt toestaan, laat **u Geen** geselecteerde voor **openbare binnenkomende poorten .**
1. Wanneer u klaar bent, selecteert u de knop **Controleren + maken** onder aan de pagina.
1. Nadat de VM is gevalideerd, selecteert **u Maken** onder aan de pagina om de implementatie te starten.


## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. Wanneer u dit wilt doen, selecteert u de resourcegroep voor de virtuele machine en selecteert u **Verwijderen**. Vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

Als u afzonderlijke bronnen wilt verwijderen, moet u deze in omgekeerde volgorde verwijderen. Als u bijvoorbeeld een afbeeldingsdefinitie wilt verwijderen, moet u alle afbeeldingsversies verwijderen die uit die afbeelding zijn gemaakt.

## <a name="next-steps"></a>Volgende stappen

U ook bronnen voor Shared Image Gallery maken met sjablonen. Er zijn verschillende Azure Quickstart-sjablonen beschikbaar: 

- [Een gedeelde afbeeldingsgalerie maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een afbeeldingsdefinitie maken in een gedeelde afbeeldingsgalerie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een afbeeldingsversie maken in een gedeelde afbeeldingsgalerie](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken op basis van afbeeldingsversie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Zie het [overzicht](shared-image-galleries.md)voor meer informatie over gedeelde galerieën. Zie [Problemen oplossen met gedeelde afbeeldingsgalerieën](troubleshooting-shared-images.md)als u problemen ondervindt.

