---
title: Azure Block Chain Service-grootboek versies, patches, & upgrade
description: Overzicht van de ondersteunde versies van de groot boeken in azure Block Chain Service, inclusief beleids regels voor systeem patches en door systeem beheer en door gebruikers beheerde upgrades.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74325191"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Ondersteunde Azure Block Chain Service-grootboek versies

De Azure Block Chain-service maakt gebruik van het op Ethereum gebaseerde [quorum](https://www.goquorum.com/developers) grootboek dat is ontworpen voor de verwerking van persoonlijke trans acties binnen een groep bekende deel nemers, geïdentificeerd als een consortium in azure Block Chain service.

Op dit moment ondersteunt de Azure Block Chain-service [quorum versie 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) en [Tessera-transactie beheer](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Updates en upgrades beheren

Het maken van versies in quorum geschiedt via een belang rijke, secundaire en patch releases. Als de quorum versie bijvoorbeeld 2.0.1 is, wordt release type als volgt gecategoriseerd:

|Primair | Secundair  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

De Azure Block Chain-service werkt patch releases van quorum automatisch bij van bestaande actieve leden binnen 30 dagen na de beschik baarheid van quorum.

## <a name="availability-of-new-ledger-versions"></a>Beschik baarheid van nieuwe grootboek versies

De Azure Block Chain-service biedt de meest recente primaire en secundaire versies van het quorum grootboek binnen 60 dagen na de beschik baarheid van de quorum fabrikant. Er zijn Maxi maal vier secundaire releases beschikbaar voor consortia waaruit u kunt kiezen bij het inrichten van een nieuw lid en consortium. Een upgrade van naar een primaire of secundaire versie wordt momenteel niet ondersteund. Als u bijvoorbeeld versie 2. x uitvoert, wordt een upgrade naar versie 3. x op dit moment niet ondersteund. Op dezelfde manier geldt dat als u versie 2,2 uitvoert, een upgrade naar versie 2,3 momenteel niet wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen

[Limieten in azure Block Chain Service](limits.md)
