---
title: Kosten en gebruik van AWS beheren in Azure Cost Management
description: In dit artikel leert u hoe u kosten analyse en budgetten kunt gebruiken in Cost Management om uw AWS-kosten en-gebruik te beheren.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 772f6cdde575a9ac669c73ecca039914357ffe2f
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338886"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Kosten en gebruik van AWS in azure beheren

Nadat u AWS hebt ingesteld en geconfigureerd voor Azure Cost Management, kunt u de kosten en het gebruik van uw AWS gaan beheren. In dit artikel leert u hoe u kosten analyse en budgetten kunt gebruiken in Cost Management om uw AWS-kosten en-gebruik te beheren.

Zie [integratie van AWS-gebruiks rapport instellen en configureren](aws-integration-set-up-configure.md)als u de integratie nog niet hebt geconfigureerd.

_Voordat u begint_: Als u niet bekend bent met de kosten analyse, raadpleegt u de Snelstartgids [voor het verkennen en analyseren van kosten analyse](quick-acm-cost-analysis.md) . En als u niet bekend bent met budgetten in azure, raadpleegt u de zelf studie [Azure-budgetten maken en beheren](tutorial-acm-create-budgets.md) .

## <a name="view-aws-costs-in-cost-analysis"></a>Kosten voor AWS weer geven in kosten analyse

De kosten voor AWS zijn beschikbaar in kosten analyse in de volgende bereiken:

- AWS gekoppelde accounts onder een beheer groep
- Kosten AWS gekoppeld account
- Kosten van geconsolideerde AWS-accounts

In de volgende secties wordt beschreven hoe u de bereiken gebruikt, zodat u de kosten en gebruiks gegevens voor elke categorie kunt zien.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Gekoppelde AWS-accounts weer geven onder een beheer groep

Het weer geven van de kosten met behulp van het beheer groeps bereik is de enige manier om geaggregeerde kosten weer te geven die afkomstig zijn van verschillende abonnementen en gekoppelde accounts. Het gebruik van een beheer groep biedt een cross-Cloud weergave.

Open in cost analysis de scope Picker en selecteer de beheer groep die uw AWS gekoppelde accounts bevat. Hier volgt een voor beeld van een afbeelding in de Azure Portal:

![Voor beeld van de weer gave bereik selecteren](./media/aws-integration-manage/select-scope01.png)



Hier volgt een voor beeld van de kosten van de beheer groep in kosten analyse, gegroepeerd op provider (Azure en AWS).

![Voor beeld van de kosten voor Azure en AWS voor een kwar taal in kosten analyse](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>AWS gekoppelde account kosten weer geven

Als u de AWS wilt weer geven, opent u de bereik kiezer en selecteert u het gekoppelde AWS-account. Houd er rekening mee dat gekoppelde accounts zijn gekoppeld aan een beheer groep, zoals gedefinieerd in de AWS-connector.

Hier volgt een voor beeld waarin wordt getoond hoe u een AWS gekoppeld account bereik selecteert.

![Voor beeld van de weer gave bereik selecteren](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Kosten voor geconsolideerde AWS-accounts weer geven

Als u de kosten voor AWS geconsolideerde accounts wilt bekijken, opent u de bereik kiezer en selecteert u het geconsolideerde AWS-account. Hier volgt een voor beeld waarin wordt getoond hoe u een AWS geconsolideerd account bereik selecteert.

![Voor beeld van de weer gave bereik selecteren](./media/aws-integration-manage/select-scope03.png)



Dit bereik biedt een geaggregeerde weer gave van alle gekoppelde AWS-accounts die zijn gekoppeld aan het geconsolideerde AWS-account. Hier volgt een voor beeld van de kosten voor een geconsolideerd AWS-account, gegroepeerd op service naam.

![Voor beeld van het weer geven van AWS geconsolideerde kosten in kosten analyse](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Beschik bare dimensies voor filteren en groeperen

In de volgende tabel worden de dimensies beschreven die beschikbaar zijn voor groeperen en filteren op in kosten analyse.

| Dimensie | Kop van Amazon CUR | Bereiken | Opmerkingen |
| --- | --- | --- | --- |
| Beschikbaarheidszone | lineitem/AvailabilityZone | Alle |   |
| Location | product/regio | Alle |   |
| Meter |   | Alle |   |
| Metercategorie | lineItem/product code | Alle |   |
| Subcategorie van de meter | lineitem/UsageType | Alle |   |
| Bewerking | lineItem/Operation | Alle |   |
| Resource | lineItem/ResourceId | Alle |   |
| Resourcetype | product-instanceType | Alle | Als product-instanceType null is, wordt lineItem/UsageType gebruikt. |
| ResourceGuid | N/A | Alle | GUID van Azure meter. |
| Servicenaam | product/ProductName | Alle | Als product/ProductName null is, wordt lineItem/product code gebruikt. |
| Servicelaag |   |   |   |
| Abonnements-id | lineItem/UsageAccountId | Geconsolideerd account en beheer groep |   |
| Abonnementsnaam | N/A | Geconsolideerd account en beheer groep | Account namen worden verzameld met behulp van de AWS-organisatie-API. |
| Label | resourceTags/\* | Alle | Het voor voegsel _gebruiker:_ wordt verwijderd uit door de gebruiker gedefinieerde labels om cross-Cloud tags toe te staan. Het voor voegsel _AWS:_ blijft intact. |
| Factureringsaccount-id | bill/PayerAccountId | Beheergroep |   |
| Naam van het factureringsaccount | N/A | Beheergroep | Account namen worden verzameld met behulp van de AWS-organisatie-API. |
| Provider | N/A | Beheergroep | Ofwel AWS of Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Budgetten instellen voor AWS-bereiken

Gebruik budgetten om de kosten en de verantwoordelijkheid van uw organisatie proactief te beheren. Budgetten worden ingesteld op het AWS geconsolideerde account en het AWS gekoppelde account bereik. Hier volgt een voor beeld van budgetten voor een geconsolideerd AWS-account dat wordt weer gegeven in Cost Management:

![Voor beeld van budgetten voor een geconsolideerd AWS-account](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>AWS-gegevens verzamelings proces

Nadat de AWS-connector is ingesteld, starten gegevens verzameling en detectie processen. Het kan enkele uren duren om alle gebruiks gegevens te verzamelen. De duur is afhankelijk van:

- De tijd die nodig is voor het verwerken van de huidige bestanden in de AWS S3-Bucket.
- De tijd die nodig is voor het maken van het AWS geconsolideerde account en het AWS gekoppelde account bereik.
- De tijd en frequentie van AWS zijn de kosten-en gebruiks rapport bestanden in de S3-Bucket.

## <a name="aws-integration-pricing"></a>Prijzen voor AWS-integratie

Elke AWS-connector krijgt 90 gratis proef dagen. Tijdens de open bare preview-periode worden er geen kosten in rekening gebracht.

De lijst prijs is 1% van de maandelijkse kosten van uw AWS. Voor elke maand worden er kosten in rekening gebracht op basis van uw gefactureerd bedrag van de vorige maand.

Het openen van AWS-Api's kan extra kosten in beslag nemen.

## <a name="aws-integration-limitations"></a>Beperkingen voor AWS-integratie

- Cost Management ondersteunt geen kosten rapporten die meerdere valuta typen bevatten. Er wordt een fout bericht weer gegeven als u een bereik met meerdere valuta's selecteert.
- Cloud connectors bieden geen ondersteuning voor AWS GovCloud (US), AWS gov of AWS China.
- Cost Management worden alleen AWS _gebruikt_ . Belasting, ondersteuning, restitutie, RI, tegoed of andere kosten typen worden nog niet ondersteund.

## <a name="troubleshooting-aws-integration"></a>Problemen met AWS-integratie oplossen

Gebruik de volgende informatie voor het oplossen van problemen om veelvoorkomende problemen op te lossen.

### <a name="no-permission-to-aws-linked-accounts"></a>Geen machtiging voor het AWS van gekoppelde accounts

**Fout code:** _Gasten_

Er zijn twee manieren om machtigingen te krijgen voor de kosten van de AWS gekoppelde accounts:

- Toegang krijgen tot de beheer groep met de gekoppelde AWS-accounts.
- Laat iemand u toestemming geven voor het gekoppelde AWS-account.

De maker van de AWS-connector is standaard de eigenaar van alle objecten die de connector heeft gemaakt. Inclusief, het geconsolideerde AWS-account en het gekoppelde AWS-account.

Om te kunnen controleren of de connector instellingen die u nodig hebt, ten minste een rol Inzender, kan de lezer de connector instellingen niet verifiëren

### <a name="collection-failed-with-assumerole"></a>Verzameling is mislukt met AssumeRole

**Fout code:** _FailedToAssumeRole_

Deze fout betekent dat Cost Management de AssumeRole-API van AWS niet kan aanroepen. Dit probleem kan optreden vanwege een probleem met de roldefinitie. Controleer of aan de volgende voor waarden wordt voldaan:

- De externe ID is hetzelfde als die in de roldefinitie en de definitie van de connector.
- Het type rol wordt ingesteld op **een ander AWS-account dat deel uitmaakt van u of een derde partij.**
- De **vereiste MFA** -keuze is gewist.
- Het vertrouwde AWS-account in de AWS-rol is _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>De verzameling is mislukt omdat de toegang is geweigerd-huidige rapport definities

**Fout code:** _AccessDeniedReportDefinitions_ 

Deze fout betekent dat Cost Management de definities van de kosten en het gebruiks rapport niet kan zien. Deze machtiging wordt gebruikt om te controleren of het huidige is gedefinieerd als verwacht door Azure Cost Management. Zie [een kosten-en gebruiks rapport maken in AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>Verzameling mislukt met geweigerde toegang-lijst rapporten

**Fout code:** _AccessDeniedListReports_ 

Deze fout betekent dat Cost Management het object in de S3-Bucket waar het huidige zich bevindt, niet kan worden weer geven. Voor het AWS IAM-beleid is een machtiging vereist voor de Bucket en voor de objecten in de Bucket. Zie [een rol en beleid maken in AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>De verzameling is mislukt omdat de toegang is geweigerd. Download rapport 

**Fout code:** _AccessDeniedDownloadReport_ 

Deze fout betekent dat Cost Management geen toegang krijgt tot de CUR-bestanden die zijn opgeslagen in de Amazon S3-Bucket. Zorg ervoor dat het AWS JSON-beleid dat aan de rol is gekoppeld, lijkt op het voor beeld onder aan de sectie [een rol en beleid maken in AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) .

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>De verzameling is mislukt omdat het kosten-en gebruiks rapport niet zijn gevonden

**Fout code:** _FailedToFindReport_

Deze fout betekent dat Cost Management geen kosten-en gebruiks rapport kunt vinden dat in de connector is gedefinieerd. Zorg ervoor dat deze niet wordt verwijderd en dat het AWS JSON-beleid dat aan de rol is gekoppeld, lijkt op het voor beeld onder aan de sectie [een rol en beleid maken in AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) .

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Kan de connector niet maken of controleren vanwege niet-overeenkomende kosten-en gebruiks rapport definities

**Fout code:** _ReportIsNotValid_

Deze fout heeft betrekking op de definitie van het AWS-kosten-en gebruiks rapport, we hebben specifieke instellingen voor dit rapport nodig. Zie de vereisten in het [rapport Create a cost and usage in AWS voor](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws) meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Als u uw Azure-omgeving nog niet hebt geconfigureerd met beheer groepen, raadpleegt u de [eerste instellingen van beheer groepen](../governance/management-groups/overview.md#initial-setup-of-management-groups).
