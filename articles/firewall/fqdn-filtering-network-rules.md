---
title: Azure Firewall FQDN-filtering in netwerk regels (preview-versie)
description: Azure Firewall FQDN-filtering gebruiken in netwerk regels
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7a30238250c9fcb86f1cc01226d44ab385c61843
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086622"
---
# <a name="use-fqdn-filtering-in-network-rules-preview"></a>FQDN-filtering in netwerk regels gebruiken (preview-versie)

> [!IMPORTANT]
> FQDN-filtering in netwerk regels is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Een Fully Qualified Domain Name (FQDN) vertegenwoordigt een domein naam van een host. Een domein naam is gekoppeld aan een of meer IP-adressen. U kunt FQDN-en FQDN-Tags in toepassings regels toestaan of blok keren. Met aangepaste DNS-en DNS-proxy-instellingen kunt u ook FQDN-filtering in netwerk regels gebruiken.

## <a name="how-it-works"></a>Uitleg

Azure Firewall vertaalt de FQDN naar een IP-adres (sen) met behulp van de DNS-instellingen en wordt de regel verwerkt op basis van Azure DNS of een aangepaste DNS-configuratie.

Als u FQDN-waarden in netwerk regels wilt gebruiken, dient u DNS-proxy in te scha kelen. Als u geen DNS-proxy inschakelt, loopt de betrouw bare regel verwerking risico. Wanneer deze is ingeschakeld, wordt DNS-verkeer omgeleid naar Azure Firewall, waar u uw aangepaste DNS-server kunt configureren. Vervolgens gebruiken de firewall en clients dezelfde geconfigureerde DNS-server. Als de DNS-proxy niet is ingeschakeld, kan Azure Firewall een andere reactie veroorzaken, omdat de client en firewall mogelijk verschillende servers gebruiken voor naam omzetting. FQDN-filtering in netwerk regels is mogelijk beschadigd of inconsistent als de client en firewall verschillende DNS-antwoorden ontvangen.

U kunt ervoor kiezen om deze vereiste te negeren door het risico te bevestigen voordat u **Opslaan** in de regel verzameling selecteert.

## <a name="next-steps"></a>Volgende stappen

[DNS-instellingen Azure Firewall](dns-settings.md)
