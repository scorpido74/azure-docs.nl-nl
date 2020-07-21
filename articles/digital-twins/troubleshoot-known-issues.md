---
title: Bekende problemen-Azure Digital Apparaatdubbels
description: Hulp krijgen bij het herkennen en beperken van bekende problemen met Azure Digital Apparaatdubbels.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8fce451d9b806d2fa9a4f3d9e1c117de0aaa9fc0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531734"
---
# <a name="known-issues-in-azure-digital-twins"></a>Bekende problemen in azure Digital Apparaatdubbels

Dit artikel bevat informatie over bekende problemen met Azure Digital Apparaatdubbels.

## <a name="managing-event-routes-in-the-azure-portal"></a>Gebeurtenis routes in de Azure Portal beheren

Als u bent aangemeld bij de portal met een persoonlijk [**Microsoft-account (MSA)**](https://account.microsoft.com/account/Account), zoals een *@outlook.com* account, wordt er een scherm weer *gegeven met de melding dat u toestemming hebt om gebeurtenis routes te bekijken* bij het beheren van gebeurtenis routes in de portal, ongeacht uw machtigings niveau.

:::image type="content" source="media/troubleshoot-known-issues/event-route-need-permission.png" alt-text="Scherm opname van de Azure Portal van de machtigings fout bij het maken van gebeurtenis routes voor een Azure Digital Apparaatdubbels-exemplaar":::

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Gebruikers die momenteel geen gebeurtenis routes kunnen beheren in de portal, kunnen ook gebeurtenis routes beheren met de Azure Digital Apparaatdubbels Api's of CLI. Overschakelen naar een van deze hulpprogram ma's voor gebeurtenis route beheer is de aanbevolen strategie om dit probleem te verhelpen.

De instructies hiervoor vindt u in instructies [*: eind punten en routes beheren*](how-to-manage-routes.md).

### <a name="possible-causes"></a>Mogelijke oorzaken

U bent aangemeld bij de portal met een persoonlijk [Microsoft-account (MSA)](https://account.microsoft.com/account/Account), zoals een *@outlook.com* account. Het beheren van gebeurtenis routes in de Azure Portal is momenteel alleen beschikbaar voor Azure-gebruikers op accounts in een bedrijfs domein.

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