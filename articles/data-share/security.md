---
title: Beveiligingsoverzicht van Azure Data Share
description: Beveiligingsoverzicht van Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 1fdf026e9271ef6eb30c2b4ca96a04880b65be75
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578090"
---
# <a name="security-overview-for-azure-data-share"></a>Beveiligingsoverzicht van Azure Data Share

Dit artikel bevat een overzicht van de beveiliging van de Azure data share-service.

## <a name="security-overview"></a>Beveiligingsoverzicht

Azure data share maakt gebruik van de onderliggende beveiliging die Azure biedt voor het beveiligen van gegevens in rust en onderweg. Gegevens worden op rest versleuteld, waarbij ondersteund door het onderliggende gegevens archief. Gegevens worden ook versleuteld tijdens de overdracht met behulp van TLS 1,2. Meta gegevens over een gegevens share worden ook versleuteld in rust en onderweg. De inhoud van de klant gegevens die worden gedeeld, wordt niet opgeslagen door de Azure-gegevens share.

Azure data share maakt gebruik van beheerde identiteiten (voorheen bekend als MSI) voor toegang tot gegevens archieven die worden gebruikt voor het delen van gegevens. Er is geen uitwisseling van referenties tussen een gegevens provider en een gegevens verbruiker. Raadpleeg [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)voor meer informatie over beheerde identiteiten. Raadpleeg [rollen en vereisten](concepts-roles-permissions.md)voor meer informatie over de rollen en machtigingen die nodig zijn voor het delen van gegevens.

Toegangs beheer voor Azure data share kan worden ingesteld op het resource niveau van de gegevens share om te controleren of het toegankelijk is voor gebruikers die zijn gemachtigd. 

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Gegevens delen van of naar gegevens archieven waarvoor Firewall is ingeschakeld
Als u gegevens wilt delen van of naar opslag accounts waarvoor Firewall is ingeschakeld, moet u **vertrouwde micro soft-Services** inschakelen in uw opslag account. Zie [Azure Storage firewalls en virtuele netwerken configureren](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hoe u gegevens kunt beginnen delen, gaat u verder naar de zelfstudie [uw gegevens delen](share-your-data.md).
