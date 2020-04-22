---
title: PTA uitschakelen bij gebruik van Azure AD Connect 'Niet configureren' | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u PTA uitschakelt met de functie 'Niet configureren' van Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa1046dc64fed3edb6c9d04f76a96f488769ff42
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726798"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>PTA uitschakelen bij gebruik van Azure AD Connect "Niet configureren"

Als u Pass-through Authentication gebruikt met Azure AD Connect en u deze hebt ingesteld op 'Niet configureren', u deze uitschakelen. Het uitschakelen van PTA kan worden gedaan met behulp van de volgende cmdlets. 

## <a name="prerequisites"></a>Vereisten
De volgende voorwaarden zijn vereist:
- Elke windows machine die de PTA agent geïnstalleerd. 
- Agent moet bij versie 1.5.1742.0 of hoger zijn. 
- Een Azure-globale beheerdersaccount om de PowerShell-cmdlets uit te voeren om PTA uit te schakelen.

>[!NOTE]
> Als uw agent ouder is, heeft het mogelijk niet de cmdlets die nodig zijn om deze bewerking te voltooien. U een nieuwe agent van Azure Portal een installatie op elke Windows-machine en admin referenties. (Het installeren van de agent heeft geen invloed op de PTA-status in de cloud)

> [!IMPORTANT]
> Als u de Azure Government-cloud gebruikt, moet u de parameter ENVIRONMENTNAME met de volgende waarde doorgeven. 
>
>| Naam van omgeving | Cloud |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>PTA uitschakelen
Gebruik vanuit een PowerShell-sessie het volgende om PTA uit te schakelen:
1. PS C:\Program Files\Microsoft Azure AD Connect Authentication Agent>`Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` of `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` of `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Als u geen toegang hebt tot een agent

Als u geen agentmachine hebt, u de volgende opdracht gebruiken om een agent te installeren.

1. Download de nieuwste Auth Agent van portal.azure.com.
2. Installeer de `.\AADConnectAuthAgentSetup.exe` functie: of`.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Volgende stappen

- [Gebruikersaanmelding met Pass Through-verificatie in Azure Active Directory](how-to-connect-pta.md)