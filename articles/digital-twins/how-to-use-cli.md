---
title: De Azure Digital Twins-CLI gebruiken
titleSuffix: Azure Digital Twins
description: Bekijk hoe u aan de slag gaat met en de Azure Digital Apparaatdubbels CLI gebruikt.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 118e137f06a49f2c125b1ca156877514d65af86f
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047060"
---
# <a name="use-the-azure-digital-twins-cli"></a>De Azure Digital Twins-CLI gebruiken

Naast het beheren van uw Azure Digital Apparaatdubbels-exemplaar in het Azure Portal, heeft Azure Digital Apparaatdubbels een **opdracht regel interface (CLI)** die u kunt gebruiken om de meeste belang rijke acties uit te voeren met de-service, waaronder:
* Een Azure Digital Apparaatdubbels-exemplaar beheren
* Modellen beheren
* Digitale apparaatdubbels beheren
* Dubbele relaties beheren
* Eind punten configureren
* [Routes](concepts-route-events.md) beheren
* [Beveiliging](concepts-security.md) configureren via op rollen gebaseerd toegangs beheer (RBAC)

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>Gebruikt (implementeren en valideren)

Naast het algemeen beheer van uw exemplaar, is de CLI ook een handig hulp middel voor implementatie en validatie.
* De opdrachten op het besturings element kunnen worden gebruikt om de implementatie van een nieuw exemplaar te herhalen of automatisch uit te voeren.
* De gegevenslaag opdrachten kunnen worden gebruikt om snel waarden in uw exemplaar te controleren en te controleren of de bewerkingen zijn voltooid zoals verwacht.

## <a name="get-the-extension"></a>De uitbrei ding ophalen

De Azure Digital Apparaatdubbels-opdrachten maken deel uit van de [Azure IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension). U kunt de volledige lijst met opdrachten en hun gebruik weer geven als onderdeel van de referentie documentatie voor de `az iot` opdrachtset: [ *AZ DT* opdracht verwijzing](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest).

Met deze stappen kunt u ervoor zorgen dat u de meest recente versie van de uitbrei ding hebt. U kunt deze opdrachten uitvoeren in de [Azure Cloud shell](../cloud-shell/overview.md) of een [lokale Azure cli](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Volgende stappen

Verken de CLI en de volledige set opdrachten via de referentie documenten:
* [*AZ DT* -opdracht verwijzing](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)