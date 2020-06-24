---
title: De Azure Digital Twins-CLI gebruiken
titleSuffix: Azure Digital Twins
description: Bekijk hoe u aan de slag gaat met en de Azure Digital Apparaatdubbels CLI gebruikt.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 595ab06e618fd977b75a15d3acb9a9a339b6edce
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84725291"
---
# <a name="use-the-azure-digital-twins-cli"></a>De Azure Digital Twins-CLI gebruiken

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Naast het beheren van uw Azure Digital Apparaatdubbels-exemplaar in het Azure Portal, heeft Azure Digital Apparaatdubbels een **opdracht regel interface (CLI)** die u kunt gebruiken om de meeste belang rijke acties uit te voeren met de-service, waaronder:
* Een Azure Digital Apparaatdubbels-exemplaar beheren
* Modellen beheren
* Digitale apparaatdubbels beheren
* Dubbele relaties beheren
* Eind punten configureren
* [Routes](concepts-route-events.md) beheren
* [Beveiliging](concepts-security.md) configureren via op rollen gebaseerd toegangs beheer (RBAC)

De Azure Digital Apparaatdubbels-opdrachten maken deel uit van de [Azure IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension). U kunt de referentie documentatie voor deze opdrachten weer geven als onderdeel van de `az iot` opdracht set: [AZ DT](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest).

## <a name="deploy-and-validate"></a>Implementeren en valideren

Naast het algemeen beheer van uw exemplaar, is de CLI ook een handig hulp middel voor implementatie en validatie.
* De opdrachten op het besturings element kunnen worden gebruikt om de implementatie van een nieuw exemplaar te herhalen of automatisch uit te voeren.
* De gegevenslaag opdrachten kunnen worden gebruikt om snel waarden in uw exemplaar te controleren en te controleren of de bewerkingen zijn voltooid zoals verwacht.

## <a name="next-steps"></a>Volgende stappen

Zie een Azure Digital Apparaatdubbels-exemplaar beheren met Api's en Sdk's voor een alternatief voor CLI-opdrachten:
* [Instructies: de Azure Digital Apparaatdubbels-Api's en Sdk's gebruiken](how-to-use-apis-sdks.md)
