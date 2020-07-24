---
title: Uw Azure Stack edge-apparaat vervangen | Microsoft Docs
description: Hierin wordt beschreven hoe u een vervangend Azure Stack edge-apparaat ophaalt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 5d5e8cc3a959c7800019107d2c682e144449b46c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097172"
---
# <a name="replace-your-azure-stack-edge-device"></a>Uw Azure Stack edge-apparaat vervangen

In dit artikel wordt beschreven hoe u een vervangend Azure Stack edge-apparaat ophaalt. Er is een vervangend apparaat nodig wanneer het bestaande apparaat een hardwarestoring heeft of een upgrade nodig heeft. 


In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Een ondersteunings ticket voor hardware-probleem openen
> * Een nieuwe resource maken voor een vervangend apparaat in Azure Portal
> * Installeren, het vervangende apparaat activeren
> * Het oorspronkelijke apparaat retour neren

## <a name="open-a-support-ticket"></a>Een ondersteunings ticket openen

Als uw bestaande apparaat een hardwarestoring heeft, opent u een ondersteunings ticket. Microsoft Ondersteuning stelt vast dat een vervangende eenheid voor een veld (FRU) niet beschikbaar is voor dit exemplaar of dat het apparaat een hardware-upgrade nodig heeft. In beide gevallen zal ondersteuning een vervangend apparaat best Ellen.

1. Open een ondersteunings ticket met Microsoft Ondersteuning dat aangeeft dat u het apparaat wilt retour neren. Selecteer het probleem type als **Azure stack edge-hardware**.

    ![Ondersteuningsticket openen](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Een Microsoft Ondersteuning-Engineer neemt contact met u op. Geef de verzend gegevens op.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>Een resource maken voor een vervangend apparaat

Volg deze stappen om een resource te maken.

1. Volg de stappen in [een nieuwe resource maken](azure-stack-edge-deploy-prep.md#create-a-new-resource) om een resource te maken voor het vervangende apparaat. 

2. Zorg ervoor dat u het selectie vakje inschakelt voor **een Azure stack edge-apparaat**. 

    ![Resource voor vervangend apparaat](media/azure-stack-edge-replace-device/replace-resource-1.png)  

## <a name="install-and-activate-the-replacement-device"></a>Het vervangende apparaat installeren en activeren

Voer de volgende stappen uit om het vervangende apparaat te installeren en te activeren:

1. [Installeer uw apparaat](azure-stack-edge-deploy-install.md).

2. [Activeer uw apparaat](azure-stack-edge-deploy-connect-setup-activate.md) met de nieuwe resource die u eerder hebt gemaakt.

## <a name="return-your-existing-device"></a>Uw bestaande apparaat retour neren

Volg alle stappen om het oorspronkelijke apparaat te retour neren:

1. [De gegevens op het apparaat wissen](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. Het [retour neren van het apparaat starten](azure-stack-edge-return-device.md#initiate-device-return) voor het oorspronkelijke apparaat.
3. [Een ophaling plannen](azure-stack-edge-return-device.md#schedule-a-pickup).
4. [Verwijder de resource](azure-stack-edge-return-device.md#delete-the-resource) die aan het geretourneerde apparaat is gekoppeld.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [retour neren van een Azure stack edge-apparaat](azure-stack-edge-return-device.md).
