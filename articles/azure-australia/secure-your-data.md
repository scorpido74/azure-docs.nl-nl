---
title: Gegevens beveiliging in azure Australië
description: Azure configureren binnen de Australische regio's om te voldoen aan de specifieke vereisten van het beleid voor de Australische overheid, de regelgeving en de wetgeving.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0301d506fc5764dbfda496727da95b2a1f2e9c7e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608350"
---
# <a name="data-security-in-azure-australia"></a>Gegevens beveiliging in azure Australië

De overkoepelend-principes voor het beveiligen van klant gegevens zijn:

* Gegevens beveiligen met versleuteling
* Geheimen beheren
* De toegang tot gegevens beperken

## <a name="encrypting-your-data"></a>Uw gegevens versleutelen

De versleuteling van gegevens kan worden toegepast op het niveau van de schijf (op rest), in data bases (op rest en in-transit), in toepassingen (in-transit) en in het netwerk (in-transit). Er zijn verschillende manieren om versleuteling in azure te bereiken:

|Service/functie|Description|
|---|---|
|Storage Service-versleuteling|Azure Storage service versleuteling is ingeschakeld op het niveau van het opslag account, wat resulteert in blok-blobs en pagina-blobs worden automatisch versleuteld wanneer deze naar Azure Storage worden geschreven. Wanneer u de gegevens van Azure Storage leest, wordt deze door de opslag service ontsleuteld voordat ze worden geretourneerd. Gebruik SSE om uw gegevens te beveiligen zonder dat u code hoeft te wijzigen of toe te voegen aan toepassingen.|
|Azure Disk Encryption|Gebruik Azure Disk Encryption voor het versleutelen van de besturingssysteem schijven en gegevens schijven die worden gebruikt door een virtuele machine van Azure. Integratie met Azure Key Vault biedt u de controle over en helpt u bij het beheren van schijf versleutelings sleutels.|
|Versleuteling op toepassings niveau aan client zijde|Versleuteling aan de client zijde is ingebouwd in de Java-en de .NET Storage-client Bibliotheken, die gebruik kunnen maken van Azure Key Vault-Api's, waardoor het eenvoudig te implementeren is. Gebruik Azure Key Vault om toegang te krijgen tot de geheimen in Azure Key Vault voor specifieke personen die Azure Active Directory gebruiken.|
|Versleuteling in transit|De basis versleuteling die beschikbaar is voor de connectiviteit met Azure Australië ondersteunt Trans Port Level Security (TLS) 1,2-protocol en X. 509-certificaten. Federal Information Processing Standard (FIPS) 140-2 cryptografische algoritmen van niveau 1 worden ook gebruikt voor infrastructuur netwerk verbindingen tussen Azure Australia-data centers.  Windows Server 2016, Windows 10, Windows Server 2012 R2, Windows 8,1 en Azure bestands shares kunnen SMB 3,0 gebruiken voor versleuteling tussen de virtuele machine en de bestands share. Gebruik versleuteling aan de client zijde om de gegevens te versleutelen voordat ze worden overgebracht naar de opslag in een client toepassing en om de gegevens te ontsleutelen nadat deze uit de opslag ruimte zijn overgedragen.|
|IaaS Vm's|Gebruik Azure Disk Encryption. Schakel Storage Service Encryption in om de VHD-bestanden te versleutelen die worden gebruikt voor het maken van een back-up van die schijven in Azure Storage, maar hiermee versleutelt u alleen nieuwe geschreven gegevens. Dit betekent dat, als u een virtuele machine maakt en vervolgens Storage Service Encryption inschakelt op het opslag account dat het VHD-bestand bevat, alleen de wijzigingen worden versleuteld, niet op het oorspronkelijke VHD-bestand.|
|Clientversleuteling|Dit is de veiligste methode voor het versleutelen van uw gegevens, omdat deze wordt versleuteld vóór de door Voer en de gegevens in rust worden versleuteld. Hiervoor moet u echter wel code toevoegen aan uw toepassingen met behulp van opslag, wat u mogelijk niet wilt. In dergelijke gevallen kunt u HTTPS gebruiken voor uw gegevens tijdens de overdracht en Storage Service Encryption om de gegevens in rust te versleutelen. Versleuteling aan de client zijde vergt ook meer belasting op de client. hiervoor moet u dit in uw schaalbaarheids plannen doen, met name als u grote hoeveel heden gegevens versleutelt en overdraagt.|
|

Raadpleeg de [opslag beveiligings handleiding](https://docs.microsoft.com/azure/storage/storage-security-guide)voor meer informatie over de versleutelings opties in Azure.

## <a name="protecting-data-by-managing-secrets"></a>Gegevens beveiligen door geheimen te beheren

Beveiligd sleutel beheer is essentieel voor het beveiligen van gegevens in de Cloud. Klanten moeten streven om sleutel beheer te vereenvoudigen en controle te houden over sleutels die worden gebruikt door Cloud toepassingen en-services om gegevens te versleutelen.

### <a name="managing-secrets"></a>Geheimen beheren

* Gebruik Key Vault om de Risico's te minimaliseren van geheimen die worden weer gegeven via in code vastgelegde configuratie bestanden, scripts of in de bron code. Azure Key Vault versleutelt sleutels (zoals de versleutelings sleutels voor Azure Disk Encryption) en geheimen (zoals wacht woorden) door deze op te slaan in het FIPS 140-2 level 2-gevalideerde hardware security modules (Hsm's). Voor extra zekerheid kunt u sleutels importeren of genereren in deze Hsm's.
* Toepassings code en sjablonen mogen alleen URI-verwijzingen naar de geheimen bevatten (wat betekent dat de daad werkelijke geheimen zich niet in code, configuratie of bron code opslagplaatsen bevinden). Hiermee wordt voor komen dat sleutel phishing-aanvallen op interne of externe opslag plaatsen, zoals een oogst-bots in GitHub.
* Gebruik sterke RBAC-besturings elementen in Key Vault. Als een vertrouwde operator het bedrijf verlaat of overdraagt naar een nieuwe groep binnen het bedrijf, moeten ze geen toegang hebben tot de geheimen.  

Zie [Azure Key Vault](azure-key-vault.md) voor meer informatie.

## <a name="isolation-to-restrict-data-access"></a>Isolatie om de toegang tot gegevens te beperken

Isolatie is alles over het gebruik van grenzen, segmentering en containers om de toegang tot gegevens te beperken tot alleen geautoriseerde gebruikers, services en toepassingen. De schei ding tussen tenants is bijvoorbeeld een essentieel beveiligings mechanisme voor multi tenant-Cloud platforms zoals Microsoft Azure. Logische isolatie helpt voor komen dat een Tenant de bewerkingen van een andere Tenant verstoort.

#### <a name="per-customer-isolation"></a>Isolatie per klant

Azure implementeert netwerk toegangs beheer en schei ding via Layer 2 VLAN-isolatie, Toegangs beheer lijsten, load balancers en IP-filters.

Klanten kunnen hun resources verder isoleren in abonnementen, resource groepen, virtuele netwerken en subnetten.

Zie de [isolatie in de open bare Azure-Cloud](../security/fundamentals/isolation-choices.md)voor meer informatie over het isoleren van Microsoft Azure.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het artikel over [Azure VPN gateway](vpn-gateway.md)