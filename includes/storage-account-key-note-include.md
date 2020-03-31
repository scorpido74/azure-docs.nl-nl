---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460553"
---
## <a name="protect-your-access-keys"></a>Uw toegangssleutels beveiligen

De toegangssleutels van uw opslagaccount zijn vergelijkbaar met een hoofdwachtwoord voor uw opslagaccount. Wees altijd voorzichtig om uw toegangssleutels te beschermen. Gebruik Azure Key Vault om uw sleutels veilig te beheren en te roteren. Vermijd het distribueren van toegangssleutels onder andere gebruikers, hard-codering ze, of opslaan ze overal in platte tekst die toegankelijk is voor anderen. Draai uw sleutels als u denkt dat ze mogelijk zijn aangetast.

Gebruik indien mogelijk Azure Active Directory (Azure AD) om aanvragen te autoriseren voor blob- en wachtrijopslag in plaats van gedeelde sleutel. Azure AD biedt superieure beveiliging en gebruiksgemak via Shared Key. Zie [Toegang tot Azure blobs en wachtrijen autoriseren met Azure Active Directory](../articles/storage/common/storage-auth-aad.md)voor meer informatie over het autoriseren van toegang tot gegevens met Azure AD.
