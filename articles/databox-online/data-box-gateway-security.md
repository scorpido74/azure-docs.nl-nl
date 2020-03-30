---
title: Azure Data Box Gateway-beveiliging | Microsoft Documenten
description: Beschrijft de beveiligings- en privacyfuncties die uw virtuele apparaat, service en gegevens van Azure Data Box Gateway beschermen, on-premises en in de cloud.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900598"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Beveiliging en gegevensbescherming van Azure Data Box Gateway

Beveiliging is een grote zorg wanneer u een nieuwe technologie gebruikt, vooral als de technologie wordt gebruikt met vertrouwelijke of bedrijfseigen gegevens. Azure Data Box Gateway helpt u ervoor te zorgen dat alleen geautoriseerde entiteiten uw gegevens kunnen bekijken, wijzigen of verwijderen.

In dit artikel worden de beveiligingsfuncties van Azure Data Box Gateway beschreven die elk van de oplossingsonderdelen en de gegevens die daarin zijn opgeslagen, helpen beschermen.

De Data Box Gateway-oplossing bestaat uit vier hoofdcomponenten die met elkaar communiceren:

- **Data Box Gateway-service, gehost in Azure.** De beheerbron die u gebruikt om de apparaatvolgorde te maken, het apparaat te configureren en vervolgens de volgorde bij te houden tot de voltooiing.
- **Data Box Gateway-apparaat**. Het virtuele apparaat dat u inde inde hypervisor van het systeem dat u levert. Dit virtuele apparaat wordt gebruikt om uw on-premises gegevens in Azure te importeren.
- **Clients/hosts die zijn verbonden met het apparaat.** De clients in uw infrastructuur die verbinding maken met het Data Box Gateway-apparaat en gegevens bevatten die moeten worden beschermd.
- **Cloudopslag.** De locatie in het Azure-cloudplatform waar gegevens worden opgeslagen. Deze locatie is meestal het opslagaccount dat is gekoppeld aan de Data Box Gateway-bron die u maakt.


## <a name="data-box-gateway-service-protection"></a>Bescherming van de Data Box Gateway-service

De Data Box Gateway-service is een beheerservice die wordt gehost in Azure. De service wordt gebruikt om het apparaat te configureren en te beheren.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Gegevensbox gateway-apparaatbeveiliging

Het Data Box Gateway-apparaat is een virtueel apparaat dat is ingericht in de hypervisor van een on-premises systeem dat u levert. Het apparaat helpt bij het verzenden van gegevens naar Azure. Uw apparaat:

- Heeft een activeringssleutel nodig om toegang te krijgen tot de Data Box Edge/Data Box Gateway-service.
- Is te allen tijde beveiligd met een apparaatwachtwoord.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Het apparaat beveiligen via activeringssleutel

Alleen een geautoriseerd Data Box Gateway-apparaat mag lid worden van de Data Box Gateway-service die u in uw Azure-abonnement maakt. Als u een apparaat wilt autoriseren, moet u een activeringssleutel gebruiken om het apparaat te activeren met de Data Box Gateway-service.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

Zie [Een activeringssleutel opvragen voor](data-box-gateway-deploy-prep.md#get-the-activation-key)meer informatie.

### <a name="protect-the-device-via-password"></a>Het apparaat beveiligen via wachtwoord

Wachtwoorden zorgen ervoor dat alleen geautoriseerde gebruikers toegang hebben tot uw gegevens. Data Box Gateway-apparaten starten op in een vergrendelde status.

U kunt:

- Maak verbinding met de lokale webgebruikersinterface van het apparaat via een browser en geef vervolgens een wachtwoord op om u aan te melden bij het apparaat.
- Maak op afstand verbinding met de PowerShell-interface van het apparaat via HTTP. Extern beheer is standaard ingeschakeld. U vervolgens het apparaatwachtwoord opgeven om u bij het apparaat aan te melden. Zie [Op afstand verbinding maken met uw Data Box Gateway-apparaat](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)voor meer informatie.

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Gebruik de lokale web-gebruikersinterface om het wachtwoord te [wijzigen.](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access) Als u het wachtwoord wijzigt, moet u alle gebruikers met externe toegang hiervan op de hoogte stellen, zodat ze geen problemen hebben met aanmelden.


## <a name="protect-your-data"></a>Uw gegevens beveiligen

In deze sectie worden de beveiligingsfuncties van De Data Box Gateway beschreven die binnenkomende en opgeslagen gegevens beschermen.

### <a name="protect-data-at-rest"></a>Data-at-rest beschermen

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Gegevens tijdens de vlucht beveiligen

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Gegevens beveiligen via opslagaccounts

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Draai en synchroniseer vervolgens regelmatig [uw opslagaccountsleutels](data-box-gateway-manage-shares.md#sync-storage-keys) om uw opslagaccount te beschermen tegen onbevoegde gebruikers.

## <a name="manage-personal-information"></a>Persoonlijke gegevens beheren

De Data Box Gateway-service verzamelt persoonlijke gegevens in de volgende scenario's:

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Als u de lijst met gebruikers wilt bekijken die een aandeel kunnen openen of verwijderen, voert u de stappen uit in [Shares beheren op de Data Box Gateway.](data-box-gateway-manage-shares.md)

Bekijk het privacybeleid van Microsoft in het [Vertrouwenscentrum](https://www.microsoft.com/trustcenter)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Uw Data Box Gateway-apparaat implementeren](data-box-gateway-deploy-prep.md)
