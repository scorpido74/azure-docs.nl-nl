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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75460553"
---
## <a name="protect-your-access-keys"></a>Uw toegangs sleutels beveiligen

De toegangs sleutels voor uw opslag account zijn vergelijkbaar met een basis wachtwoord voor uw opslag account. Zorg ervoor dat u altijd uw toegangs sleutels beveiligt. Gebruik Azure Key Vault om uw sleutels veilig te beheren en te draaien. Vermijd het distribueren van toegangs sleutels naar andere gebruikers, het afwijzen van de code of het opslaan van berichten naar een wille keurige locatie in tekst zonder opmaak die voor anderen toegankelijk is. Roteer uw sleutels als u denkt dat ze mogelijk zijn aangetast.

Gebruik, indien mogelijk, Azure Active Directory (Azure AD) voor het machtigen van aanvragen voor Blob-en wachtrij opslag in plaats van een gedeelde sleutel. Azure AD biedt een superieure beveiliging en gebruiks vriendelijk gebruik over gedeelde sleutels. Zie [toegang tot Azure-blobs en-wacht rijen toestaan met Azure Active Directory](../articles/storage/common/storage-auth-aad.md)voor meer informatie over het machtigen van toegang tot gegevens met Azure AD.
