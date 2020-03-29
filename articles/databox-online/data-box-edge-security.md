---
title: Azure Data Box Edge-beveiliging | Microsoft Documenten
description: Beschrijft de beveiligings- en privacyfuncties die uw Azure Data Box Edge-apparaat, -service en -gegevens on-premises en in de cloud beschermen.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69970889"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure Data Box Disk Edge-beveiliging en -gegevensbescherming

Beveiliging is een grote zorg wanneer u een nieuwe technologie gebruikt, vooral als de technologie wordt gebruikt met vertrouwelijke of bedrijfseigen gegevens. Azure Data Box Edge helpt u ervoor te zorgen dat alleen geautoriseerde entiteiten uw gegevens kunnen bekijken, wijzigen of verwijderen.

In dit artikel worden de beveiligingsfuncties van Data Box Edge beschreven die elk van de oplossingsonderdelen en de gegevens die erin zijn opgeslagen, helpen beschermen.

Azure Data Box Edge bestaat uit vier hoofdcomponenten die met elkaar communiceren:

- **Data Box Edge-service, gehost in Azure.** De beheerbron die u gebruikt om de apparaatvolgorde te maken, het apparaat te configureren en vervolgens de volgorde bij te houden tot de voltooiing.
- **Data Box Edge-apparaat**. Het transferapparaat dat naar u wordt verzonden, zodat u uw on-premises gegevens importeren in Azure.
- **Clients/hosts die zijn verbonden met het apparaat.** De clients in uw infrastructuur die verbinding maken met het Data Box Edge-apparaat en gegevens bevatten die moeten worden beschermd.
- **Cloudopslag.** De locatie in het Azure-cloudplatform waar gegevens worden opgeslagen. Deze locatie is meestal het opslagaccount dat is gekoppeld aan de Data Box Edge-bron die u maakt.

## <a name="data-box-edge-service-protection"></a>Gegevensbox Edge-servicebeveiliging

De Data Box Edge-service is een beheerservice die wordt gehost in Azure. De service wordt gebruikt om het apparaat te configureren en te beheren.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Gegevensvak edge-apparaatbeveiliging

Het Data Box Edge-apparaat is een on-premises apparaat dat helpt uw gegevens te transformeren door deze lokaal te verwerken en vervolgens naar Azure te verzenden. Uw apparaat:

- Heeft een activeringssleutel nodig om toegang te krijgen tot de Data Box Edge-service.
- Is te allen tijde beveiligd met een apparaatwachtwoord.
- Is een vergrendeld apparaat. Het apparaat BMC en BIOS zijn beveiligd met een wachtwoord. Het BIOS wordt beschermd door beperkte gebruikerstoegang.
- Heeft secure boot ingeschakeld.
- Met Windows Defender Device Guard. Met Device Guard u alleen vertrouwde toepassingen uitvoeren die u definieert in uw code-integriteitsbeleid.

### <a name="protect-the-device-via-activation-key"></a>Het apparaat beveiligen via activeringssleutel

Alleen een geautoriseerd Data Box Edge-apparaat mag lid worden van de Data Box Edge-service die u in uw Azure-abonnement maakt. Als u een apparaat wilt autoriseren, moet u een activeringssleutel gebruiken om het apparaat te activeren met de Data Box Edge-service.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Zie [Een activeringssleutel opvragen voor](data-box-edge-deploy-prep.md#get-the-activation-key)meer informatie.

### <a name="protect-the-device-via-password"></a>Het apparaat beveiligen via wachtwoord

Wachtwoorden zorgen ervoor dat alleen geautoriseerde gebruikers toegang hebben tot uw gegevens. Data Box Edge-apparaten starten op in een vergrendelde status.

U kunt:

- Maak verbinding met de lokale webgebruikersinterface van het apparaat via een browser en geef vervolgens een wachtwoord op om u aan te melden bij het apparaat.
- Maak op afstand verbinding met de PowerShell-interface van het apparaat via HTTP. Extern beheer is standaard ingeschakeld. U vervolgens het apparaatwachtwoord opgeven om u bij het apparaat aan te melden. Zie [Op afstand verbinding maken met uw Data Box Edge-apparaat](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)voor meer informatie.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Gebruik de lokale web-gebruikersinterface om het wachtwoord te [wijzigen.](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access) Als u het wachtwoord wijzigt, moet u alle gebruikers van externe toegang hiervan op de hoogte stellen, zodat ze geen problemen hebben met aanmelden.

## <a name="protect-your-data"></a>Uw gegevens beveiligen

In deze sectie worden de beveiligingsfuncties van Data Box Edge beschreven die binnenkomende en opgeslagen gegevens beschermen.

### <a name="protect-data-at-rest"></a>Data-at-rest beschermen

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256-bits versleuteling wordt gebruikt om lokale gegevens te beschermen.


### <a name="protect-data-in-flight"></a>Gegevens tijdens de vlucht beveiligen

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Gegevens beveiligen via opslagaccounts

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Draai en synchroniseer vervolgens regelmatig [uw opslagaccountsleutels](data-box-edge-manage-shares.md#sync-storage-keys) om uw opslagaccount te beschermen tegen onbevoegde gebruikers.

## <a name="manage-personal-information"></a>Persoonlijke gegevens beheren

De Data Box Edge-service verzamelt persoonlijke gegevens in de volgende scenario's:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Als u de lijst met gebruikers wilt bekijken die een aandeel kunnen openen of verwijderen, voert u de stappen uit in Aandelen beheren op de rand van [het gegevensvak](data-box-edge-manage-shares.md).

Bekijk het privacybeleid van Microsoft in het [Vertrouwenscentrum](https://www.microsoft.com/trustcenter)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Uw Data Box Edge-apparaat implementeren](data-box-edge-deploy-prep.md)
