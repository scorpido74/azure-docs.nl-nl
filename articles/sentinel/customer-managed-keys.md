---
title: Door de klant beheerde sleutels instellen in Azure Sentinel| Microsoft Documenten
description: Meer informatie over het instellen van door klanten beheerde sleutels (CMK) in Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: yelevin
ms.openlocfilehash: 5eed208ed79aeab4e46ed90dd4d340a8b445be96
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461630"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Azure Sentinel-sleutel voor de klant instellen


In dit artikel vindt u achtergrondinformatie en stappen om een door de klant beheerde sleutel (CMK) voor Azure Sentinel te configureren. Met CMK kunnen alle gegevens die zijn opgeslagen of verzonden naar Azure Sentinel worden versleuteld in alle relevante opslagbronnen met een Azure Key Vault-sleutel die door u is gemaakt of eigendom is.

> [!NOTE]
> -   De Azure Sentinel CMK-mogelijkheid wordt alleen geleverd aan klanten die **nieuw** zijn en de toegang tot deze mogelijkheid wordt beheerd door Azure-functieregistratie.U toegang aanvragen azuresentinelCMK@microsoft.comdoor contact op te nemen en als er capaciteit beschikbaar is, worden in behandeling zijnde aanvragen goedgekeurd.
> -   De Azure Sentinel CMK-mogelijkheid is alleen beschikbaar in regio's Oost-VS, West-VS 2 en Zuid-Centraal-VS.
> -   De CMK-mogelijkheid is alleen beschikbaar voor klanten die 1 TB per dag of meer verzenden. U ontvangt informatie over aanvullende prijzen wanneer u een aanvraag indient bij Microsoft voor het inrichten van CMK op uw Azure-abonnement. Meer informatie over het opladen van [Log Analytics.](../azure-monitor/platform/customer-managed-keys.md#disclaimers)

## <a name="how-cmk-works"></a>Hoe CMK werkt 

De Azure Sentinel-oplossing maakt gebruik van een aantal opslagbronnen voor logboekverzameling en -functies, waaronder Log Analytics en andere opslagbronnen. Als onderdeel van de Azure Sentinel CMK-configuratie moet u ook de CMK-instellingen configureren op de bijbehorende opslagbronnen. Gegevens die zijn opgeslagen in andere opslagbronnen dan Log Analytics, worden ook versleuteld.

> [!NOTE]
> Als u CMK inschakelt op Azure Sentinel, wordt geen Openbare Preview-functie ingeschakeld die CMK niet ondersteunt.

## <a name="enable-cmk"></a>CMK inschakelen 

Voer de volgende stappen uit om CMK in te richten: 

1.  Maak een Azure Key Vault en opslagsleutel.

2.  Schakel CMK in op uw Log Analytics-werkruimte.

3.  Schrijf je in voor Cosmos DB.

4.  Voeg een toegangsbeleid toe aan uw Azure Key Vault-exemplaar.

5.  Schakel CMK in Azure Sentinel in.

6.  Azure Sentinel inschakelen.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>STAP 1: Een Azure Key Vault maken en een sleutel opslaan

1.  [Maak Azure Key Vault-bron](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)en genereer of importeer vervolgens een sleutel die moet worden gebruikt voor gegevensversleuteling.
    > [!NOTE]
    >  Azure Key Vault moet zijn geconfigureerd als herstelbaar om uw sleutel en de toegang te beschermen.

1.  [Herstelopties inschakelen:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   Controleer of [Soft Delete](../key-vault/general/overview-soft-delete.md) is ingeschakeld.

    -   Schakel [Purge bescherming](../key-vault/general/overview-soft-delete.md#purge-protection) om te waken tegen gedwongen verwijdering van de geheime / kluis, zelfs na zachte verwijderen.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>STAP 2: CMK inschakelen op uw Log Analytics-werkruimte

Volg de instructies in [de door de klant beheerde sleutelconfiguratie](../azure-monitor/platform/customer-managed-keys.md) van Azure Monitor om een CMK-werkruimte te maken die in de volgende stappen wordt gebruikt als azure sentinel-werkruimte.

### <a name="step-3-register-for-cosmos-db"></a>STAP 3: Registreer voor Cosmos DB

Azure Sentinel werkt met Cosmos DB als extra opslagbron. Zorg ervoor dat u zich registreert bij Cosmos DB.

Volg de Cosmos DB-instructie om [de Azure Cosmos DB-bronprovider](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) te registreren voor uw Azure-abonnement.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>STAP 4: Een toegangsbeleid toevoegen aan uw Azure Key Vault-exemplaar

Zorg ervoor dat u toegang van Cosmos DB toevoegt aan uw Azure Key Vault-exemplaar. Volg de Cosmos DB-instructie om [een toegangsbeleid toe te voegen aan uw Azure Key Vault-exemplaar](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) met Azure Cosmos DB-principal.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>STAP 5: CMK inschakelen in Azure Sentinel

De Azure Sentinel CMK-mogelijkheid wordt pas aan nieuwe klanten geleverd nadat ze rechtstreeks toegang hebben gekregen vanuit de Azure-productgroep. Gebruik uw contactpersonen bij Microsoft om goedkeuring te krijgen van het Azure Sentinel-team om CMK in uw oplossing in te schakelen.

Nadat u goedkeuring hebt gekregen, wordt u gevraagd om de volgende informatie te verstrekken om de CMK-functie in te schakelen.

-  Werkruimte-id waarop u CMK wilt inschakelen

-  Url van key vault: kopieer de sleutel-id van de sleutel tot de laatste slash door:  
    

    ![sleutel-id](./media/customer-managed-keys/key-identifier.png)

    Het Azure Sentinel-team schakelt de Azure Sentinel CMK-functie in voor uw geleverde werkruimte.

-  Verificatie van het Azure Sentinel-productteam dat u bent goedgekeurd om deze functie te gebruiken. Je moet dit hebben voordat je verder gaat.

### <a name="step-6-enable-azure-sentinel"></a>STAP 6: Azure Sentinel inschakelen


Ga naar de Azure-portal en schakel Azure Sentinel in op de werkruimte waarop u CMK instelt. Zie [Azure Sentinel Onboarding](quickstart-onboard.md)voor meer informatie.

## <a name="key-encryption-key-revocation-or-deletion"></a>Intrekking of verwijdering van sleutelversleutelingssleutel


In het geval dat een gebruiker de sleutelversleutelingssleutel intrekt, door deze te verwijderen of de toegang voor Azure Sentinel te verwijderen, zal Azure Sentinel de wijziging nakomen en zich gedragen alsof de gegevens niet meer beschikbaar zijn. Op dit moment wordt elke bewerking die wordt uitgevoerd en die gebruikmaakt van permanente opslagbronnen, zoals gegevensopname, permanente configuratiewijzigingen en het maken van incidenten, voorkomen. Eerder opgeslagen gegevens worden niet verwijderd, maar blijven ontoegankelijk. Ontoegankelijke gegevens worden beheerst door het beleid voor het bewaren van gegevens en worden gezuiverd in overeenstemming met dat beleid.

De enige bewerking die mogelijk is nadat de versleutelingssleutel is ingetrokken of verwijderd, is het verwijderen van het account.

Als de toegang na intrekking wordt hersteld, herstelt Azure Sentinel de toegang tot de gegevens binnen een uur.

Zie Intrekking van [Azure Monitor CMK](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation)voor meer informatie over hoe dit werkt in Azure Monitor.

## <a name="key-encryption-key-rotation"></a>Sleutelcoderingssleutelrotatie


Azure Sentinel en Log Analytics ondersteunen sleutelrotatie. Wanneer een gebruiker sleutelrotatie uitvoert in Key Vault, ondersteunt Azure Sentinel de nieuwe sleutel binnen een uur.

In Key Vault u sleutelrotatie uitvoeren door een nieuwe versie van de sleutel te maken:

![toetsrotatie](./media/customer-managed-keys/key-rotation.png)

U de vorige versie van de sleutel na 24 uur uitschakelen of nadat de azure key vault-controlelogboeken geen activiteit meer weergeven die de vorige versie gebruikt.

Als u dezelfde sleutel gebruikt in Azure Sentinel en in Log Analytics, is het noodzakelijk om sleutelrotatie uit te voeren, moet u de clusterbron in Log Analytics expliciet bijwerken met de nieuwe Azure Key Vault-sleutelversie. Zie [Azure Monitor CMK-rotatie](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation)voor meer informatie .

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u een door de klant beheerde sleutel in Azure Sentinel instelt. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.

