---
title: Problemen oplossen Azure IoT Hub 500xxx Interne fouten
description: Begrijpen hoe u 500xxx interne fouten oplossen
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7f3f5177e084693c45bed1088a4e1d091be100ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271042"
---
# <a name="500xxx-internal-errors"></a>500xxx Internal errors

Dit artikel beschrijft de oorzaken en oplossingen voor **500xxx interne fouten**.

## <a name="symptoms"></a>Symptomen

Uw verzoek om IoT Hub mislukt met een fout die begint met 500 en/of een soort "serverfout". Enkele mogelijkheden zijn:

* **500001 ServerError**: IoT Hub liep tegen een server-side probleem.

* **500008 GenericTimeout**: IoT Hub kon de verbindingsaanvraag niet voltooien voordat de timing uitging.

* **ServiceNiet beschikbaar (geen foutcode):** IoT Hub heeft een interne fout opgelopen.

* **InternalServerError (geen foutcode):** IoT Hub heeft een interne fout opgelopen.

## <a name="cause"></a>Oorzaak

Er kunnen een aantal oorzaken zijn voor een 500xxx foutreactie. In alle gevallen is het probleem waarschijnlijk van voorbijgaande aard. Hoewel het IoT Hub-team hard werkt om [de SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)te behouden, kunnen kleine subsets van IoT Hub-knooppunten af en toe tijdelijke fouten ervaren. Wanneer uw apparaat verbinding probeert te maken met een knooppunt dat problemen heeft, ontvangt u deze fout.

## <a name="solution"></a>Oplossing

Als u 500xxx-fouten wilt beperken, geeft u een nieuwe poging vanaf het apparaat. Als u [opnieuw zoeken automatisch wilt beheren,](./iot-hub-reliability-features-in-sdks.md#connection-and-retry)moet u ervoor zorgen dat u de nieuwste versie van de Azure [IoT-SDK's gebruikt.](./iot-hub-devguide-sdks.md) Zie [Tijdelijke foutafhandeling](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults)voor aanbevolen procedures voor tijdelijke foutafhandeling en -pogingen .  Als het probleem zich blijft voordoen, schakelt u [Resourcestatus](./iot-hub-monitor-resource-health.md#use-azure-resource-health) en [Azure-status](https://status.azure.com/) in om te zien of IoT Hub een bekend probleem heeft. U ook de [handmatige failoverfunctie](./tutorial-manual-failover.md)gebruiken. Als er geen bekende problemen zijn en het probleem blijft, neem [dan contact op met de ondersteuning](https://azure.microsoft.com/support/options/) voor verder onderzoek.
