---
title: Instellingen voor exporteren in azure API configureren voor FHIR
description: In dit artikel wordt beschreven hoe u instellingen voor exporteren configureert in azure API for FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: 46a55b83b38593a514d40a9f75d99739a1efb8a2
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "84871804"
---
# <a name="configure-export-setting-and-export-the-data-to-a-storage-account"></a>De export instelling configureren en de gegevens exporteren naar een opslag account

De Azure-API voor FHIR ondersteunt $export opdracht waarmee u de gegevens uit de Azure API voor het FHIR-account kunt exporteren naar een opslag account.

Er zijn vier stappen betrokken bij het uitvoeren van exporteren in azure API voor FHIR:

1. Beheerde identiteit inschakelen op de Azure-API voor de FHIR-service
2. Een Azure Storage-account maken (indien dit nog niet is gedaan) en machtigingen toewijzen aan de Azure-API voor FHIR aan het opslag account
3. Het opslag account in de Azure-API selecteren voor FHIR als export-opslag account
4. De export uitvoeren door $export opdracht aan te roepen in azure API voor FHIR

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Beheerde identiteit inschakelen op de Azure-API voor FHIR

De eerste stap bij het configureren van de Azure-API voor FHIR voor export is het inschakelen van System Wide Managed Identity voor de service. U kunt [hier](../active-directory/managed-identities-azure-resources/overview.md)alles lezen over beheerde identiteiten in Azure.

Hiertoe gaat u naar de Azure API for FHIR-service en selecteert u identiteits Blade. Als de status wordt gewijzigd in aan, wordt beheerde identiteit ingeschakeld in azure API voor de FHIR-service.

![Beheerde identiteit inschakelen](media/export-data/fhir-mi-enabled.png)

Nu kunnen we door gaan met de volgende stap, een opslag account maken en toestemming toewijzen aan onze service.

## <a name="adding-permission-to-storage-account"></a>Machtiging voor het opslag account toevoegen

De volgende stap bij het exporteren is het toewijzen van een machtiging voor de Azure API voor de FHIR-service om naar het opslag account te schrijven.

Nadat u een opslag account hebt gemaakt, gaat u naar de Blade Access Control (IAM) in het opslag account en selecteert u roltoewijzingen toevoegen

![Beheerde identiteit inschakelen](media/export-data/fhir-export-role-assignment.png)

Hier gaan we vervolgens rollen opslag BLOB-gegevens Inzender toevoegen aan de service naam.

![Beheerde identiteit inschakelen](media/export-data/fhir-export-role-add.png)

We zijn nu klaar voor de volgende stap, waarbij we het opslag account in azure API voor FHIR kunnen selecteren als standaard opslag account voor $export.

## <a name="selecting-the-storage-account-for-export"></a>Het opslag account voor $export selecteren

De laatste stap voor het aanroepen van $export opdracht is het toewijzen van het Azure-opslag account dat door de Azure-API voor FHIR wordt gebruikt om de gegevens te exporteren naar. Hiertoe gaat u naar de Blade integratie in azure API voor de service FHIR in Azure Portal en selecteert u het opslag account 

![Beheerde identiteit inschakelen](media/export-data/fhir-export-storage.png)

Daarna kunt u de gegevens exporteren met behulp van $export opdracht.

## <a name="exporting-the-data-using-export-command"></a>De gegevens exporteren met behulp van $export-opdracht

Nadat we Azure API voor FHIR voor export hebben geconfigureerd, kunnen we de $export opdracht gebruiken om de gegevens uit de service te exporteren naar het opslag account dat we hebben opgegeven. Raadpleeg voor meer informatie over het aanroepen van $export-opdracht in FHIR Server documentatie over $export specificatie op[https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html)

> [!IMPORTANT]
> Houd er rekening mee dat de Azure-API voor FHIR alleen de export van het systeem niveau ondersteunt zoals gedefinieerd in de specificatie export op [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . We bieden momenteel ook geen ondersteuning voor query parameters met de $export.

>[!div class="nextstepaction"]
>[Aanvullende instellingen](azure-api-for-fhir-additional-settings.md)