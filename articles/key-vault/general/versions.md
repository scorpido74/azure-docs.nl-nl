---
title: Azure Key Vault versies
description: De verschillende versies van Azure Key Vault
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: mbaldwin
ms.openlocfilehash: 58c5fdcfef5e866d0c69d65412582c0ed649f7c6
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86230822"
---
# <a name="key-vault-versions"></a>Versies van Key Vault

Dit is what's nieuw met Azure Key Vault. Nieuwe functies en verbeteringen worden ook aangekondigd op de [Azure-updates Key Vault kanaal](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="june-2020"></a>Juni 2020

Azure Monitor voor Key Vault is nu beschikbaar als preview-versie.  Azure Monitor biedt uitgebreide bewaking van uw sleutel kluizen door een uniforme weer gave te bieden van uw Key Vault aanvragen, prestaties, fouten en latentie. Zie [Azure monitor voor Key Vault (preview)](../../azure-monitor/insights/key-vault-insights-overview.md)voor meer informatie.

## <a name="may-2020"></a>Mei 2020

Key Vault ' uw eigen sleutel halen ' (BYOK) is nu algemeen beschikbaar. Raadpleeg de [Azure Key Vault BYOK-specificatie](../keys/byok-specification.md)en leer hoe u met [HSM beveiligde sleutels kunt importeren in Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Maart 2020

Privé-eind punten zijn nu beschikbaar als preview-versie. Met Azure Private Link service kunt u toegang krijgen tot Azure Key Vault en Azure gehoste klanten/partner services via een persoonlijk eind punt in uw virtuele netwerk.  Meer informatie over het [integreren van Key Vault met een persoonlijke Azure-koppeling](private-link-service.md).

## <a name="2019"></a>2019

- Release van de Azure Key Vault Sdk's voor de volgende generatie. Voor voor beelden van het gebruik van, raadpleegt u de Azure Key Vault Secret-Quick starts voor [python](../secrets/quick-create-python.md), [.net](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md)en [Node.js](../secrets/quick-create-node.md)
- Nieuwe Azure-beleids regels voor het beheren van sleutel kluis certificaten. Zie de [Azure Policy ingebouwde definities voor Key Vault](../policy-samples.md).
- Azure Key Vault extensie van de virtuele machine nu algemeen beschikbaar.  Zie [Key Vault extensie van de virtuele machine voor Linux](../../virtual-machines/extensions/key-vault-linux.md) en [Key Vault extensie voor virtuele machines voor Windows](../../virtual-machines/extensions/key-vault-windows.md).
- Beheer van gebeurtenis gerichte geheimen voor Azure Key Vault nu beschikbaar in Azure Event Grid. Zie [het Event Grid schema voor gebeurtenissen in Azure Key Vault] (. voor meer informatie. /.. /Event-grid/Event-schema-Key-Vault.MD] en meer informatie over het [ontvangen en reageren op belang rijke kluis meldingen met Azure Event grid](event-grid-tutorial.md).

## <a name="2018"></a>2018

Nieuwe functies en integraties die dit jaar zijn uitgebracht:

- Integratie met Azure Functions. Zie [de rotatie van een geheim automatiseren](../secrets/tutorial-rotation.md)voor een voorbeeld scenario met [Azure functions](../../azure-functions/index.yml) voor sleutel kluis bewerkingen. 
- [Integratie met Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Met deze Azure Databricks ondersteunt nu twee typen geheime bereiken: Azure Key Vault-back-ups en Databricks. Zie [een geheim voor Azure Key Vault-back-ups maken](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope) voor meer informatie
- [Virtuele netwerk service-eind punten voor Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Nieuwe functies die dit jaar zijn uitgebracht:

- Sleutels voor beheerde opslag accounts. Met de functie voor het maken van opslag accounts is de integratie met Azure Storage eenvoudiger geworden. Zie het onderwerp overzicht voor meer informatie, [overzicht van sleutels voor beheerde-opslag accounts](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).
- Zacht verwijderen. Met de functie voor voorlopig verwijderen wordt de gegevens bescherming van uw sleutel kluizen en de sleutel kluis objecten verbeterd. Zie het [onderwerp overzicht voor meer informatie.](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

## <a name="2015"></a>2015

Nieuwe functies die dit jaar zijn uitgebracht:
- Certificaatbeheer. Toegevoegd als een functie aan de GA-versie 2015-06-01 op 26 september 2016.

Algemene Beschik baarheid (versie 2015-06-01) werd aangekondigd op 24 juni 2015. In deze release zijn de volgende wijzigingen aangebracht: 
- Een sleutel verwijderen-veld gebruiken is verwijderd.
- Informatie ophalen over een sleutel-veld gebruik verwijderd.
- Een sleutel in een kluis importeren-veld gebruik verwijderd.
- Een sleutel herstellen-veld gebruiken is verwijderd.     
- RSA_OAEP gewijzigd in ' RSA-OAEP ' voor RSA-algoritmen. Zie [over sleutels, geheimen en certificaten](about-keys-secrets-certificates.md).    
 
De tweede preview-versie (versie 2015-02-01-preview) is aangekondigd 20 april 2015. Zie REST API blog bericht [bijwerken](https://docs.microsoft.com/archive/blogs/kv/rest-api-update) voor meer informatie. De volgende taken zijn bijgewerkt:
 
- Vermeld de sleutels in een kluis toegevoegde paginerings ondersteuning voor de bewerking.
- Geef een lijst weer met de versies van een sleutel-toegevoegde bewerking om de versies van een sleutel weer te geven.  
- Vermeld geheimen in een kluis toegevoegde paginerings ondersteuning.
- Lijst met versies van een geheim-toevoeg bewerking om de versies van een geheim weer te geven.  
- Alle bewerkingen-toegevoegde/bijgewerkte tijds tempels toegevoegd aan kenmerken.  
- Maak een geheim toegevoegd inhoud-type aan geheimen.
- Maak een sleutel-toegevoegde labels als optionele informatie.
- Maak een geheim toegevoegde Tags als optionele informatie.
- Een sleutel Tags toevoegen als optionele informatie.
- Een geheim toegevoegde Tags als optionele gegevens bijwerken.
- De maximale grootte voor geheimen is gewijzigd van 10 K tot 25 KB bytes. Zie [informatie over sleutels, geheimen en certificaten](about-keys-secrets-certificates.md).    
 
## <a name="2014"></a>2014
 
De eerste preview versie (versie 2014-12-08-preview) werd aangekondigd op 8 januari 2015.  
 
## <a name="next-steps"></a>Volgende stappen

- [Over sleutels, geheimen en certificaten](about-keys-secrets-certificates.md)
- [Sleutels](../keys/index.yml)
- [Geheimen](../secrets/index.yml)
- [Certificaten](../certificates/index.yml)
