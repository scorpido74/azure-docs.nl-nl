---
title: V2-accounts van Azure Media Services beheren | Microsoft Documenten
description: Als u wilt beginnen met het beheren, versleutelen, coderen, analyseren en streamen van media-inhoud in Azure, moet u een Media Services-account maken. In dit artikel wordt uitgelegd hoe u V2-accounts van Azure Media Services beheert.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 09a5f004570430fafe5c86f4f8ae048f2d1fe4c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981935"
---
# <a name="manage-azure-media-services-v2-accounts"></a>V2-accounts van Azure Media Services beheren

Als u wilt beginnen met het beheren, versleutelen, coderen, analyseren en streamen van media-inhoud in Azure, moet u een Media Services-account maken. Als u een Media Services-account gaat maken, moet u de naam van een Azure Storage-accountresource opgeven. Het opgegeven opslagaccount wordt gekoppeld aan uw Media Services-account. Het Media Services-account en alle gekoppelde opslagaccounts moeten zich in hetzelfde Azure-abonnement bevinden.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Een Media Services-account verplaatsen tussen abonnementen 

Als u een Media Services-account naar een nieuw abonnement moet verplaatsen, moet u eerst de volledige brongroep met het Media Services-account naar het nieuwe abonnement verplaatsen. U moet alle gekoppelde resources verplaatsen: Azure Storage-accounts, Azure CDN-profielen, enz. Zie [Resources verplaatsen naar een nieuwe resourcegroep of -abonnement voor](../../azure-resource-manager/management/move-resource-group-and-subscription.md)meer informatie . Zoals bij alle resources in Azure, kan het even duren voordat resourcegroepverplaatsingen zijn voltooid.

Media Services v2 ondersteunt geen multi-tenancy model. Als u een Media Services-account naar een abonnement in een nieuwe tenant moet verplaatsen, maakt u een nieuwe Azure Active Directory-toepassing (Azure AD) in de nieuwe tenant. Verplaats uw account vervolgens naar het abonnement in de nieuwe tenant. Nadat de tenant is voltooid, u een Azure AD-toepassing van de nieuwe tenant gebruiken om toegang te krijgen tot het Media Services-account met behulp van de v2-API's. 

> [!IMPORTANT]
> U moet de [Azure AD-verificatiegegevens](media-services-portal-get-started-with-aad.md) opnieuw instellen om toegang te krijgen tot Media Services v2 API.  
### <a name="considerations"></a>Overwegingen

* Maak back-ups van alle gegevens in uw account voordat u migreert naar een ander abonnement.
* Je moet alle streaming endpoints en live streaming bronnen stoppen. Uw gebruikers hebben geen toegang tot uw inhoud gedurende de duur van de verplaatsing van de brongroep. 

> [!IMPORTANT]
> Start het streamingeindpunt pas als de verhuizing is voltooid.

### <a name="troubleshoot"></a>Problemen oplossen 

Als een Media Services-account of een gekoppeld Azure Storage-account 'verbroken' wordt na de verplaatsing van de brongroep, probeert u de opslagaccountsleutels te roteren. Als het roteren van de opslagaccountsleutels de 'losgekoppeld'-status van het Media Services-account niet wordt opgelost, dient u een nieuw ondersteuningsverzoek in het menu 'Ondersteuning + probleemoplossing' in het Media Services-account.  
 
## <a name="next-steps"></a>Volgende stappen

[Een account maken](media-services-portal-create-account.md)
