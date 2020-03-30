---
title: Azure Data Box Gateway & Azure Data Box Edge 1906 release notes| Microsoft Documenten
description: Beschrijft kritieke open problemen en oplossingen voor de Azure Data Box Gateway en Azure Data Box Edge met de release 1906.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099484"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure Data Box Edge en Azure Data Box Gateway 1906 release notes Azure Data Box Edge en Azure Data Box Gateway 1906 release notes Azure Data Box Edge and Azure Data Box Gateway 1906 release notes Azure Data

In de volgende releasenotes worden de kritieke open problemen en de opgeloste problemen voor de release van 1906 voor Azure Data Box Edge en Azure Data Box Gateway geïdentificeerd.

De release notes worden voortdurend bijgewerkt en als kritieke problemen die een tijdelijke oplossing vereisen worden ontdekt, worden ze toegevoegd. Voordat u uw Data Box Edge/Data Box Gateway implementeert, controleert u zorgvuldig de informatie in de releasenotes.

Deze release komt overeen met de softwareversies:

- **Data Box Gateway 1906 (1.6.978.743)**
- **Gegevensvakrand 1906 (1.6.978.743)**

> [!NOTE]
> Update 1906 kan alleen worden toegepast op Data Box Edge-apparaten waarop de algemene beschikbaarheid (GA) of 1905-versie van de software wordt uitgevoerd.

## <a name="whats-new"></a>Nieuwe functies

- **Bug fix in de recovery key management workflow** - In de eerdere release was er een bug waardoor de herstelsleutel niet werd toegepast. Deze bug is opgelost in deze release. We raden u ten zeerste aan deze update toe te passen, omdat u met de herstelsleutel de gegevens op het apparaat herstellen, in het geval het apparaat niet wordt opgestart. Zie voor meer informatie hoe [u de herstelsleutel opslaan bij het implementeren van Data Box Edge of Data Box Gateway.](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device)
- **Field Programmeble Gate Array (FPGA) logging verbeteringen** - Vanaf 1905 release, logging en alert verbeteringen met betrekking tot FPGA werden gemaakt. Dit blijft een vereiste update voor Data Box Edge als u de Edge compute-functie met de FPGA gebruikt. Zie voor meer informatie hoe [u gegevens transformeren met Edge compute op uw Data Box Edge.](data-box-edge-deploy-configure-compute-advanced.md)

## <a name="known-issues-in-ga-release"></a>Bekende problemen in GA release

Geen nieuwe problemen zijn release bekend voor deze release. Alle release opgemerkt problemen hebben overgedragen van de vorige releases. Als u een lijst met bekende problemen wilt zien, gaat u naar [Bekende problemen in de GA-versie.](data-box-gateway-release-notes.md#known-issues-in-ga-release)


## <a name="next-steps"></a>Volgende stappen

- [Voorbereidingen voor de implementatie van Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Voorbereidingen voor de implementatie van Azure Data Box Edge](data-box-edge-deploy-prep.md)
