---
title: Wacht woord van AD DS-opslag account bijwerken
description: Meer informatie over het bijwerken van het wacht woord van het Active Directory Domain Services-account dat uw opslag account vertegenwoordigt. Zo voor komt u dat het opslag account wordt opgeruimd wanneer het wacht woord is verlopen, waardoor verificatie fouten worden voor komen.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 53dcc69b6e9ae94846330077f07ae57af1b0b414
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85214380"
---
# <a name="update-the-password-of-your-storage-account-identity-in-ad-ds"></a>Het wacht woord van de identiteit van uw opslag account bijwerken in AD DS

Als u de identiteit/het account van Active Directory Domain Services (AD DS) hebt geregistreerd voor uw opslag account in een organisatie-eenheid of domein waarin de verloop tijd van het wacht woord is afgedwongen, moet u het wacht woord wijzigen voor de maximale wachtwoord duur. Uw organisatie kan geautomatiseerde opschoon scripts uitvoeren waarmee accounts worden verwijderd zodra het wacht woord is verlopen. Als u uw wacht woord niet wijzigt voordat het verloopt, kan uw account dus worden verwijderd, waardoor u geen toegang meer hebt tot uw Azure-bestands shares.

Als u het draaien van wacht woorden wilt activeren, kunt u de `Update-AzStorageAccountADObjectPassword` opdracht uitvoeren vanuit de [module AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases). Deze opdracht moet worden uitgevoerd in een on-premises AD DS-omgeving met behulp van een hybride gebruiker met eigenaars machtigingen voor het opslag account en AD DS machtigingen om het wacht woord te wijzigen van de identiteit die het opslag account vertegenwoordigt. De opdracht voert acties uit die vergelijkbaar zijn met de rotatie van de opslag account sleutel. Met name de tweede Kerberos-sleutel van het opslag account wordt opgehaald en wordt gebruikt om het wacht woord van het geregistreerde account bij AD DS bij te werken. Vervolgens wordt de Kerberos-doel sleutel van het opslag account opnieuw gegenereerd en wordt het wacht woord van het geregistreerde account in AD DS bijgewerkt. U moet deze opdracht uitvoeren in een on-premises AD DS-omgeving.

```PowerShell
# Update the password of the AD DS account registered for the storage account
# You may use either kerb1 or kerb2
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```
