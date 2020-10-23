---
title: Beveiligingsoverzicht van Azure Data Share
description: Beveiligingsoverzicht van Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 06/05/2020
ms.openlocfilehash: 5d815c27ecc7825f0bc1e6772654b094a799b63d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216552"
---
# <a name="security-overview-for-azure-data-share"></a>Beveiligingsoverzicht van Azure Data Share

Dit artikel bevat een overzicht van de beveiliging van de Azure data share-service.

## <a name="security-overview"></a>Beveiligingsoverzicht

Azure data share maakt gebruik van de onderliggende beveiliging die Azure biedt voor het beveiligen van gegevens in rust en onderweg. Gegevens worden op rest versleuteld, waarbij ondersteund door het onderliggende gegevens archief. Gegevens worden ook tijdens de overdracht versleuteld. Meta gegevens over een gegevens share worden ook versleuteld in rust en onderweg. 

Access-besturings elementen kunnen worden ingesteld op het niveau van de Azure-gegevens share-resource om te controleren of het toegankelijk is voor gebruikers die zijn gemachtigd. 

Azure data share maakt gebruik van beheerde identiteiten (voorheen bekend als MSI) voor toegang tot gegevens archieven die worden gebruikt voor het delen van gegevens. Er is geen uitwisseling van referenties tussen een gegevens provider en een gegevens verbruiker. Raadpleeg [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)voor meer informatie over beheerde identiteiten. Raadpleeg [rollen en vereisten](concepts-roles-permissions.md)voor meer informatie over de rollen en machtigingen die nodig zijn voor het delen van gegevens.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hoe u gegevens kunt beginnen delen, gaat u verder naar de zelfstudie [uw gegevens delen](share-your-data.md).