---
title: Problemen oplossen met Windows kan het configureren van het systeem niet volt ooien
titlesuffix: Azure Virtual Machines
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij het Sysprep-proces het opstarten van een virtuele machine van Azure voor komt.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: 9c170607d6300c4921285e85ac78db5a8a18ad9b
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078803"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Problemen oplossen met Windows kan het configureren van het systeem niet volt ooien

Dit artikel bevat stappen voor het oplossen van problemen waarbij het Sysprep-proces het opstarten van een virtuele Azure-machine (VM) voor komt.

## <a name="symptom"></a>Symptoom

Wanneer u [Diagnostische gegevens over opstarten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat in de scherm opname een Windows-fout tijdens de installatie wordt weer gegeven tijdens het starten van de service. Met de fout wordt het volgende bericht weer gegeven:

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![In afbeelding 1 wordt een Windows-fout bij de installatie met het volgende bericht weer gegeven: "het configureren van het systeem door Windows is niet voltooid. Start de computer opnieuw op om de configuratie te hervatten. De services worden gestart](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>Oorzaak

Deze fout wordt veroorzaakt wanneer het [Sysprep-proces](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-process-overview)niet kan worden voltooid door het besturings systeem. Deze fout treedt op wanneer u een gegeneraliseerde VM het eerst probeert op te starten. Als u dit probleem ondervindt, moet u de algemene installatie kopie opnieuw maken, omdat de installatie kopie een niet-Implementeer bare status heeft en niet kan worden hersteld.

## <a name="solution"></a>Oplossing

U kunt dit probleem oplossen door de [Azure-richt lijnen voor het voorbereiden/vastleggen van een installatie kopie](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed) te volgen en een nieuwe gegeneraliseerde installatie kopie voor te bereiden.
