---
title: Controle lijst voor Azure data base Security | Microsoft Docs
description: Dit artikel bevat een set controle lijsten voor Azure data base Security.
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
ms.openlocfilehash: fc79d7204ba360696b8d9411cd56efd09d1678dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021837"
---
# <a name="azure-database-security-checklist"></a>Controle lijst voor Azure data base-beveiliging

Azure Data Base bevat een aantal ingebouwde beveiligings functies die u kunt gebruiken om de toegang te beperken en te beheren om de beveiliging te verbeteren.

Deze omvatten:

-    Een firewall waarmee u [firewall regels](../../azure-sql/database/firewall-configure.md) kunt maken die de connectiviteit beperken op basis van IP-adres,
-    Firewall op server niveau die toegankelijk is vanaf de Azure Portal
-    Firewall regels op database niveau die toegankelijk zijn vanaf SSMS
-    De connectiviteit met uw data base beveiligen met behulp van beveiligde verbindings reeksen
-    Toegangs beheer gebruiken
-    Gegevensversleuteling
-    SQL Database Auditing
-    Detectie van SQL Database-dreigingen

## <a name="introduction"></a>Inleiding
Cloud Computing vereist nieuwe beveiligings modellen die niet bekend zijn bij veel toepassings gebruikers, database beheerders en programmeurs. Als gevolg hiervan zijn sommige organisaties cloudhoster voor het implementeren van een Cloud infrastructuur voor gegevens beheer vanwege waargenomen beveiligings Risico's. Veel van deze bezorgdheid kan echter worden verkleind door een beter inzicht te krijgen in de beveiligings functies die zijn ingebouwd in Microsoft Azure en Microsoft Azure SQL Database.

## <a name="checklist"></a>Controlelijst
We raden u aan het artikel over de [Best practices van Azure data base Security](database-best-practices.md) te lezen voordat u deze controle lijst controleert. U kunt deze controle lijst optimaal benutten wanneer u de aanbevolen procedures begrijpt. U kunt deze controle lijst vervolgens gebruiken om ervoor te zorgen dat u de belang rijke problemen in azure data base Security hebt opgelost.


|Controlelijst categorie| Description|
| ------------ | -------- |
|**Gegevens beveiligen**||
| <br> Versleuteling in Motion/Transit| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), voor gegevens versleuteling wanneer gegevens worden verplaatst naar de netwerken.</li><li>Data base vereist beveiligde communicatie van clients op basis van het [TDS-protocol (Tabular Data stream)](https://msdn.microsoft.com/library/dd357628.aspx) via TLS (Transport Layer Security).</li></ul> |
|<br>Versleuteling 'at rest'| <ul><li>[Transparent Data Encryption](https://go.microsoft.com/fwlink/?LinkId=526242), wanneer inactieve gegevens fysiek worden opgeslagen in een digitaal formulier.</li></ul>|
|**Toegang beheren**||  
|<br> Toegang tot de database | <ul><li>[Verificatie](../../azure-sql/database/logins-create-manage.md) (Azure Active Directory-verificatie) ad-verificatie maakt gebruik van identiteiten die worden beheerd door Azure Active Directory.</li><li>[Autorisatie](../../azure-sql/database/logins-create-manage.md) verlenen gebruikers de mini maal benodigde bevoegdheden.</li></ul> |
|<br>Toegang tot toepassingen| <ul><li>[Beveiliging op rijniveau](https://msdn.microsoft.com/library/dn765131) (met behulp van het beveiligings beleid, tegelijk het beperken van toegang op rijniveau op basis van de identiteit, rol of uitvoerings context van een gebruiker).</li><li>[Dynamische gegevens maskering](../../azure-sql/database/dynamic-data-masking-overview.md) (met behulp van machtiging & beleid, beperkt de bloot stelling van gevoelige gegevens door deze te maskeren voor niet-gemachtigde gebruikers)</li></ul>|
|**Proactieve controle**||  
| <br>Bijhouden & detecteren| <ul><li>Met [auditing](../../sql-database/sql-database-auditing.md) worden database gebeurtenissen bijgehouden en naar een audit logboek/activiteiten logboek in uw [Azure Storage-account](../../storage/common/storage-create-storage-account.md)geschreven.</li><li>De Azure data base-status bijhouden met behulp van [Azure monitor activiteiten logboeken](../../azure-monitor/platform/platform-logs-overview.md).</li><li>Met [detectie van bedreigingen](../../azure-sql/database/threat-detection-configure.md) worden afwijkende database activiteiten gedetecteerd die potentiële beveiligings dreigingen voor de data base aangeven. </li></ul> |
|<br>Azure Security Center| <ul><li>[Gegevens bewaking](../../security-center/security-center-enable-auditing-on-sql-databases.md) Gebruik Azure Security Center als een gecentraliseerde oplossing voor beveiligings bewaking voor SQL en andere Azure-Services.</li></ul>|        

## <a name="conclusion"></a>Conclusie
Azure data base is een robuust database platform met een volledig scala aan beveiligings functies die voldoen aan de vereisten voor de organisatorische en reglementaire naleving. U kunt gegevens eenvoudig beveiligen door de fysieke toegang tot uw gegevens te beheren en verschillende opties te gebruiken voor gegevens beveiliging op bestands-, kolom-of rijniveau met Transparent Data Encryption, versleuteling op cel-niveau of beveiliging op rijniveau. Always Encrypted schakelt ook bewerkingen uit op versleutelde gegevens, waardoor het proces van toepassings updates wordt vereenvoudigd. Op zijn beurt krijgt u toegang tot de controle logboeken van SQL Database-activiteiten met de informatie die u nodig hebt, zodat u weet hoe en wanneer gegevens worden geopend.

## <a name="next-steps"></a>Volgende stappen
U hoeft slechts een paar eenvoudige stappen uit te voeren om de beveiliging van uw database tegen kwaadwillende gebruikers of onbevoegde toegang te verbeteren. In deze zelfstudie leert u het volgende:

- Stel [firewall regels](../../azure-sql/database/firewall-configure.md) in voor uw server en of Data Base.
- Bescherm uw gegevens met [versleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Schakel [SQL database controle](../../sql-database/sql-database-auditing.md)in.

