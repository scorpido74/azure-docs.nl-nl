---
title: Overzicht van beveiliging voor Azure-gegevens share
description: Overzicht van beveiliging voor Azure-gegevens share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: e299f63777847c557fc13afb4d36d01c12b3b52a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84637121"
---
# <a name="security-overview-for-azure-data-share"></a>Overzicht van beveiliging voor Azure-gegevens share

Dit artikel bevat een overzicht van de beveiliging van de Azure data share-service.

## <a name="security-overview"></a>Beveiligingsoverzicht

Azure data share maakt gebruik van de onderliggende beveiliging die Azure biedt voor het beveiligen van gegevens in rust en onderweg. Gegevens worden op rest versleuteld, waarbij ondersteund door het onderliggende gegevens archief. Gegevens worden ook tijdens de overdracht versleuteld. Meta gegevens over een gegevens share worden ook versleuteld in rust en onderweg. 

Access-besturings elementen kunnen worden ingesteld op het niveau van de Azure-gegevens share-resource om te controleren of het toegankelijk is voor gebruikers die zijn gemachtigd. 

Azure data share maakt gebruik van beheerde identiteiten (voorheen bekend als MSI) voor toegang tot gegevens archieven die worden gebruikt voor het delen van gegevens. Er is geen uitwisseling van referenties tussen een gegevens provider en een gegevens verbruiker. Raadpleeg [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)voor meer informatie over beheerde identiteiten. Raadpleeg [rollen en vereisten](concepts-roles-permissions.md)voor meer informatie over de rollen en machtigingen die nodig zijn voor het delen van gegevens.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hoe u gegevens kunt beginnen delen, gaat u verder naar de zelfstudie [uw gegevens delen](share-your-data.md).




