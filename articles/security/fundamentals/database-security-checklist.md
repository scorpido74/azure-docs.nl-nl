---
title: Checklist voor azure-databasebeveiliging| Microsoft Documenten
description: In dit artikel vindt u een checklist voor Azure-databasebeveiliging.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: d9283a36d5f7ccb82b2cc211485487d5a3dcce7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79201022"
---
# <a name="azure-database-security-checklist"></a>Checklist voor azure-databasebeveiliging

Om de beveiliging te verbeteren, bevat Azure Database een aantal ingebouwde beveiligingsbesturingselementen die u gebruiken om de toegang te beperken en te beheren.

Deze omvatten:

-    Een firewall waarmee u [firewallregels](../../sql-database/sql-database-firewall-configure.md) maken die de connectiviteit per IP-adres beperken,
-    Firewall op serverniveau toegankelijk vanuit de Azure-portal
-    Firewallregels op databaseniveau die toegankelijk zijn vanaf SSMS
-    Beveiligde verbinding met uw database met behulp van beveiligde verbindingstekenreeksen
-    Toegangsbeheer gebruiken
-    Gegevensversleuteling
-    SQL Database Auditing
-    DETECTIE van SQL-database-bedreigingen

## <a name="introduction"></a>Inleiding
Cloud computing vereist nieuwe beveiligingsparadigma's die onbekend zijn voor veel gebruikers van toepassingen, databasebeheerders en programmeurs. Als gevolg hiervan zijn sommige organisaties terughoudend om een cloudinfrastructuur voor gegevensbeheer te implementeren vanwege waargenomen beveiligingsrisico's. Echter, veel van deze zorg kan worden verlicht door een beter begrip van de beveiligingsfuncties ingebouwd in Microsoft Azure en Microsoft Azure SQL Database.

## <a name="checklist"></a>Controlelijst
We raden u aan het artikel [Best practices](database-best-practices.md) voor Azure Database Security te lezen voordat u deze checklist bekijkt. U het meeste uit deze checklist halen nadat u de beste praktijken hebt begrepen. U deze checklist vervolgens gebruiken om ervoor te zorgen dat u de belangrijke problemen in azure-databasebeveiliging hebt opgelost.


|Checklistcategorie| Beschrijving|
| ------------ | -------- |
|**Gegevens beveiligen**||
| <br> Versleuteling in beweging/doorvoer| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), voor gegevensversleuteling wanneer gegevens naar de netwerken worden verplaatst.</li><li>Database vereist veilige communicatie van clients op basis van het [TDS(Tabular Data Stream)](https://msdn.microsoft.com/library/dd357628.aspx) protocol via TLS (Transport Layer Security).</li></ul> |
|<br>Versleuteling 'at rest'| <ul><li>[Transparante gegevensversleuteling](https://go.microsoft.com/fwlink/?LinkId=526242), wanneer inactieve gegevens fysiek in een digitale vorm worden opgeslagen.</li></ul>|
|**Toegang beheren**||  
|<br> Toegang tot de database | <ul><li>[Verificatie](../../sql-database/sql-database-manage-logins.md) (Azure Active Directory Authentication) AD-verificatie maakt gebruik van identiteiten die worden beheerd door Azure Active Directory.</li><li>[Autorisatie](../../sql-database/sql-database-manage-logins.md) verleent gebruikers de minste bevoegdheden die nodig zijn.</li></ul> |
|<br>Toepassingstoegang| <ul><li>[Beveiliging op rijniveau](https://msdn.microsoft.com/library/dn765131) (met behulp van beveiligingsbeleid tegelijkertijd de toegang op rijniveau beperken op basis van de identiteit, rol of uitvoeringscontext van een gebruiker).</li><li>[Dynamic Data Masking](../../sql-database/sql-database-dynamic-data-masking-get-started.md) (Met behulp van toestemming & beleid, beperkt gevoelige blootstelling aan gegevens door het te maskeren aan niet-bevoorrechte gebruikers)</li></ul>|
|**Proactieve monitoring**||  
| <br>Tracking & detecteren| <ul><li>[Controle](../../sql-database/sql-database-auditing.md) houdt databasegebeurtenissen bij en schrijft deze naar een controlelogboek/ activiteitenlogboek in uw [Azure Storage-account.](../../storage/common/storage-create-storage-account.md)</li><li>Azure-databasestatus bijhouden met [Azure Monitor-activiteitslogboeken](../../azure-monitor/platform/platform-logs-overview.md).</li><li>[Threat Detection](../../sql-database/sql-database-threat-detection.md) detecteert afwijkende databaseactiviteiten die potentiële beveiligingsbedreigingen voor de database aangeven. </li></ul> |
|<br>Azure Security Center| <ul><li>[Gegevensbewaking](../../security-center/security-center-enable-auditing-on-sql-databases.md) Gebruik Azure Security Center als een gecentraliseerde beveiligingsbewakingsoplossing voor SQL en andere Azure-services.</li></ul>|        

## <a name="conclusion"></a>Conclusie
Azure Database is een robuust databaseplatform, met een volledig scala aan beveiligingsfuncties die voldoen aan veel vereisten voor organisatorische en wettelijke naleving. U gegevens eenvoudig beveiligen door de fysieke toegang tot uw gegevens te beheren en verschillende opties te gebruiken voor gegevensbeveiliging op bestands-, kolom- of rijniveau met transparante gegevensversleuteling, versleuteling op celniveau of rijniveaubeveiliging. Always Encrypted maakt ook bewerkingen tegen versleutelde gegevens mogelijk, waardoor het proces van toepassingsupdates wordt vereenvoudigd. Op zijn beurt biedt toegang tot controlelogboeken van SQL Database-activiteit u de informatie die u nodig hebt, zodat u weet hoe en wanneer gegevens worden geopend.

## <a name="next-steps"></a>Volgende stappen
U hoeft slechts een paar eenvoudige stappen uit te voeren om de beveiliging van uw database tegen kwaadwillende gebruikers of onbevoegde toegang te verbeteren. In deze zelfstudie leert u het volgende:

- Stel [firewallregels](../../sql-database/sql-database-firewall-configure.md) in voor uw server en of database.
- Bescherm uw gegevens met [versleuteling.](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption)
- [SQL-databasecontrole inschakelen](../../sql-database/sql-database-auditing.md).

