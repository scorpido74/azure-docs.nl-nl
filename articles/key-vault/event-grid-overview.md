---
title: Key Vault bewaken met Azure Event Grid
description: Azure Event Grid gebruiken om u te abonneren op Key Vault-gebeurtenissen
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 039b7e57c1f98368ab33f9f17e19a741dc9eb5f4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78184907"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>Key Vault bewaken met Azure Event Grid (voorbeeld)

Key Vault-integratie met Event Grid is momenteel in preview. Hiermee kunnen gebruikers op de hoogte worden gesteld wanneer de status van een geheim dat is opgeslagen in de sleutelkluis is gewijzigd. Een statuswijziging wordt gedefinieerd als een geheim dat op het punt staat te verlopen (binnen 30 dagen na het verstrijken), een geheim dat is verlopen of een geheim met een nieuwe versie beschikbaar. Meldingen voor alle drie de geheime typen (sleutel, certificaat en geheim) worden ondersteund.

Toepassingen kunnen reageren op deze gebeurtenissen met behulp van moderne serverloze architecturen, zonder de noodzaak van ingewikkelde code of dure en inefficiënte polling diensten. Gebeurtenissen worden via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) naar gebeurtenishandlers zoals [Azure-functies,](https://azure.microsoft.com/services/functions/) [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)of zelfs naar uw eigen Webhook gepusht en u betaalt alleen voor wat u gebruikt. Zie Prijzen voor [gebeurtenisrasters](https://azure.microsoft.com/pricing/details/event-grid/)voor informatie over prijzen .

## <a name="key-vault-events-and-schemas"></a>Key Vault-gebeurtenissen en -schema's

Gebeurtenisraster gebruikt [gebeurtenisabonnementen](../event-grid/concepts.md#event-subscriptions) om gebeurtenisberichten door te sturen naar abonnees. Key Vault-gebeurtenissen bevatten alle informatie die u nodig hebt om te reageren op wijzigingen in uw gegevens. U een Key Vault-gebeurtenis identificeren omdat de eigenschap eventType begint met 'Microsoft.KeyVault'.

Zie het [gebeurtenisschema Key Vault](../event-grid/event-schema-key-vault.md)voor meer informatie .

> [!WARNING]
> Meldingsgebeurtenissen worden alleen geactiveerd op nieuwe versies van geheimen, sleutels en certificaten en u moet zich eerst abonneren op het evenement op uw sleutelkluis om deze meldingen te ontvangen.
> 
> U ontvangt alleen meldingsgebeurtenissen op certificaten wanneer het certificaat automatisch wordt verlengd volgens het beleid dat u voor uw certificaat hebt opgegeven.

## <a name="practices-for-consuming-events"></a>Praktijken voor het consumeren van gebeurtenissen

Toepassingen die Key Vault-gebeurtenissen afhandelen, moeten een aantal aanbevolen procedures volgen:

* Meerdere abonnementen kunnen worden geconfigureerd om gebeurtenissen naar dezelfde gebeurtenishandler te leiden. Het is belangrijk om niet aan te nemen dat gebeurtenissen van een bepaalde bron zijn, maar om het onderwerp van het bericht te controleren om ervoor te zorgen dat het afkomstig is van de sleutelkluis die u verwacht.
* Controleer ook of het eventType een gebeurtenis is die u bereid bent te verwerken en ga er niet van uit dat alle gebeurtenissen die u ontvangt, de typen zijn die u verwacht.
* Velden negeren die u niet begrijpt.  Deze praktijk zal u helpen om u bestand te houden tegen nieuwe functies die in de toekomst kunnen worden toegevoegd.
* Gebruik het voorvoegsel en achtervoegsel van onderwerp om gebeurtenissen te beperken tot een bepaalde gebeurtenis.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Key Vault](key-vault-overview.md)
- [Overzicht van Azure Event Grid](../event-grid/overview.md)
- How to: [Key Vault-gebeurtenissen routeren naar automatiseringsrunbook (voorbeeld)](event-grid-tutorial.md).
- How to: [E-mail ontvangen wanneer een sleutelkluis geheim verandert](event-grid-logicapps.md)
- [Azure Event Grid-gebeurtenisschema voor Azure Key Vault (voorbeeld)](../event-grid/event-schema-key-vault.md)
- [Overzicht van Azure Automation](../automation/index.yml)
