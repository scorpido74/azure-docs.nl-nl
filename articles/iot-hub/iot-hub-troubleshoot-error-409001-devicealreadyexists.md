---
title: Azure IoT Hub-fout oplossen 409001 DeviceBestaat al bestaat
description: Begrijpen hoe u fout 409001 DeviceAlreadyExists oplossen
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960787"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

In dit artikel worden de oorzaken en oplossingen voor **409001 DeviceAlreadyExists-fouten beschreven.**

## <a name="symptoms"></a>Symptomen

Wanneer u een apparaat probeert te registreren in IoT Hub, mislukt de aanvraag met de fout **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Oorzaak

Er is al een apparaat met dezelfde apparaat-ID in de IoT-hub. 

## <a name="solution"></a>Oplossing

Gebruik een andere apparaat-ID en probeer het opnieuw.