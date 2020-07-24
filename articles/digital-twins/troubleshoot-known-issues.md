---
title: Bekende problemen-Azure Digital Apparaatdubbels
description: Hulp krijgen bij het herkennen en beperken van bekende problemen met Azure Digital Apparaatdubbels.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: bdde2076039a6f7687e06edef6dfd6f6f5148ce4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044144"
---
# <a name="known-issues-in-azure-digital-twins"></a>Bekende problemen in azure Digital Apparaatdubbels

Dit artikel bevat informatie over bekende problemen met Azure Digital Apparaatdubbels.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400-client fout: ongeldige aanvraag" in Cloud Shell

Opdrachten in Cloud Shell kunnen af en toe mislukken met de fout ' 400-client fout: ongeldige aanvraag voor URL: http://localhost:50342/oauth2/token ' gevolgd door de volledige Stack tracering.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

U kunt dit oplossen door de opdracht opnieuw uit te voeren `az login` en de volgende aanmeldings stappen te volt ooien.

Daarna kunt u de opdracht opnieuw uitvoeren...

### <a name="possible-causes"></a>Mogelijke oorzaken

Dit is het resultaat van een bekend probleem in Cloud Shell: het [*verkrijgen van tokens van Cloud shell af en toe mislukt met 400-client fout: ongeldige aanvraag*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="next-steps"></a>Volgende stappen

Lees meer over beveiliging en machtigingen op Azure Digital Apparaatdubbels:
* [*Concepten: beveiliging voor Azure Digital Apparaatdubbels Solutions*](concepts-security.md)