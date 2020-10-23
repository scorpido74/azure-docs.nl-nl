---
title: Een Azure virtual machine-aanbieding maken op Azure Marketplace met uw eigen installatie kopie
description: Meer informatie over het publiceren van een aanbieding van een virtuele machine naar Azure Marketplace met uw eigen installatie kopie.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 42022d1204c3b524ee2e9ef2770f616fba89dc8c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284304"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Een virtuele machine maken met uw eigen installatie kopie

In dit artikel wordt beschreven hoe u een installatie kopie van een door de gebruiker gedefinieerde virtuele machine (VM) maakt en implementeert.

> [!NOTE]
> Voordat u met deze procedure begint, controleert u de [technische vereisten](marketplace-virtual-machines.md#technical-requirements) voor Azure VM-aanbiedingen, inclusief vereisten voor virtuele harde schijven (VHD).

Als u in plaats daarvan een goedgekeurde basis installatie kopie wilt gebruiken, volgt u de instructies in [een VM-installatie kopie maken van een goedgekeurde basis](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>De virtuele machine configureren

In deze sectie wordt beschreven hoe u een Azure-VM kunt verg Roten of verkleinen, bijwerken en generaliseren. Deze stappen zijn nodig om uw VM voor te bereiden voor implementatie op Azure Marketplace.

### <a name="size-the-vhds"></a>Grootte van de Vhd's

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>De meest recente updates installeren

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Aanvullende beveiligings controles uitvoeren

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Aangepaste configuratie en geplande taken uitvoeren

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

## <a name="upload-the-vhd-to-azure"></a>De VHD uploaden naar Azure

Configureer en bereid de virtuele machine die moet worden geüpload, zoals beschreven in [voorbereiden van een Windows VHD of VHDX voor het uploaden naar Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) of [het maken en uploaden van een virtuele Linux-VHD](../virtual-machines/linux/create-upload-generic.md).

## <a name="extract-the-vhd-from-image-if-using-image-building-services"></a>De VHD extra heren uit de installatie kopie (als u gebruikmaakt van services voor het maken van afbeeldingen)

Als u een service voor het maken van installatie kopieën, zoals [Packer](https://www.packer.io/), gebruikt, moet u de VHD mogelijk uit de installatie kopie extra heren. Er is geen rechtstreekse manier om dit te doen. U moet een virtuele machine maken en de VHD extra heren van de VM-schijf.

### <a name="create-the-vm-on-the-azure-portal"></a>Maak de virtuele machine op de Azure Portal

Volg deze stappen om de basis installatie kopie van de virtuele machine te maken op de [Azure Portal](https://ms.portal.azure.com/).

1. Meld u aan bij de [Azure-portal](https://ms.portal.azure.com/).
2. Selecteer **Virtuele machines**.
3. Selecteer **+ toevoegen** om het scherm **een virtuele machine maken** te openen.
4. Selecteer de installatie kopie in de vervolg keuzelijst of selecteer **Bladeren alle open bare en persoonlijke installatie kopieën** om te zoeken of door alle beschik bare installatie kopieën van virtuele machines te bladeren.
5. Als u een virtuele machine van **generatie 2** wilt maken, gaat u naar het tabblad **Geavanceerd** en selecteert u de optie **generatie 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selecteer gen 1 of gen 2.":::

6. Selecteer de grootte van de virtuele machine die u wilt implementeren.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selecteer gen 1 of gen 2.":::

7. Geef de overige vereiste gegevens op om de virtuele machine te maken.
8. Selecteer **beoordeling + maken** om uw keuzes te controleren. Wanneer het bericht **door gegeven validatie** wordt weer gegeven, selecteert u **maken**.

Azure begint met het inrichten van de virtuele machine die u hebt opgegeven. Volg de voortgang van de taak door het tabblad **virtual machines** te selecteren in het menu links. Nadat de status van de virtuele machine is gemaakt, wordt deze gewijzigd in **actief**.

### <a name="connect-to-your-vm"></a>Verbinding maken met uw VM

Raadpleeg de volgende documentatie om verbinding te maken met uw virtuele [Windows](../virtual-machines/windows/connect-logon.md) -of [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) -machine.

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Volgende stappen

- Aanbevolen volgende stap: [Test uw VM-installatie kopie](azure-vm-image-test.md) om te controleren of deze voldoet aan de publicatie vereisten voor Azure Marketplace. Dit is optioneel.
- Als u de VM-installatie kopie niet test, gaat u door met [het genereren van de SAS-URI](azure-vm-get-sas-uri.md).
- Zie [Veelgestelde vragen over vm's voor Azure Marketplace](azure-vm-create-faq.md)als u problemen ondervindt bij het maken van uw nieuwe VHD op basis van Azure.
