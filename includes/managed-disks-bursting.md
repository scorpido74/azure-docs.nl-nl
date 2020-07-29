---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 2af8b429b6addf6da32b34773525c51a36624e78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85242086"
---
In azure bieden we de mogelijkheid om de IOPS en MB/s-prestaties van schijf opslag te verhogen als bursting op Virtual Machines en schijven. Busting is handig in veel scenario's, zoals het afhandelen van onverwacht schijf verkeer of het verwerken van batch taken. U kunt effectief gebruikmaken van de burstisatie van virtuele machines en schijven voor een uitstekende basis lijn en burst-prestaties op zowel uw virtuele machines als uw schijf. Op deze manier kunt u uw virtuele machine en de prestaties van de basis lijn optimaal benutten. 

Houd er rekening mee dat bursting op schijven en Vm's onafhankelijk van elkaar zijn. Als u een bursting-schijf hebt, hebt u geen bursting-VM nodig om de schijf te laten burstiseren. Als u een burst-VM hebt, hebt u geen bursting-schijf nodig om uw virtuele machine te laten burstiseren. 