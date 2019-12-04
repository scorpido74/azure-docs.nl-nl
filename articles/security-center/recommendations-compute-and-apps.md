---
title: Aanbevelingen voor Azure Security Center voor computers & apps
description: De beveiligings aanbevelingen van Azure Security Center waarmee u uw virtuele machines, computers, Web-apps en App Service omgevingen kunt beveiligen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2019
ms.author: memildin
ms.openlocfilehash: c0cf5951daf004a0c805b688f08d1ccfa4679615
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782162"
---
# <a name="compute-and-app-recommendations---reference-guide"></a>Berekenings-en app-aanbevelingen-referentie gids

In dit artikel vindt u de volledige lijst met aanbevelingen die u in Azure Security Center kunt zien met betrekking tot de volgende resource typen:

* Vm's en computers
* Schaalsets van virtuele machines
* Cloud Services
* App-services
* Containers
* Reken resources

Zie [hier](security-center-virtual-machine-protection.md)voor een uitleg over het vinden van deze oplossingen en hoe u deze kunt oplossen.

## Aanbevelingen voor Compute en apps<a name="compute-and-app-recs"></a>
|Resourcetype|Beveiligingsscore|Aanbeveling|Beschrijving|
|----|----|----|----|
|App service|20|Webtoepassing mag alleen toegankelijk zijn via HTTPS|Beperk de toegang tot webtoepassingen alleen via HTTPS.|
|App service|20|functie-app mag alleen toegankelijk zijn via HTTPS|Beperk de toegang tot functie-apps alleen via HTTPS.|
|App service|5|Diagnostische logboeken in App Services moeten worden ingeschakeld|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |
|App service|10|Externe fout opsporing moet worden uitgeschakeld voor webtoepassing|Schakel fout opsporing voor webtoepassingen uit als u deze niet meer nodig hebt. Voor fout opsporing op afstand moeten binnenkomende poorten worden geopend op een functie-app.|
|App service|10|Fout opsporing op afstand moet worden uitgeschakeld voor functie toepassing|Schakel fout opsporing voor functie-app uit als u deze niet meer nodig hebt. Voor fout opsporing op afstand moeten binnenkomende poorten worden geopend op een functie-app.|
|App service|10|Niet alle (' * ') bronnen toestaan om toegang te krijgen tot uw toepassing| Stel geen set WEBSITE_LOAD_CERTIFICATES-para meter in op ' '. Als u de para meter instelt op ' ', worden alle certificaten geladen in het persoonlijke certificaat archief van uw webtoepassingen. Dit kan leiden tot misbruik van het principe van minimale bevoegdheden, omdat het onwaarschijnlijk is dat de site toegang moet hebben tot alle certificaten tijdens runtime.|
|App service|20|CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw webtoepassingen|Stel alleen vereiste domeinen in staat om te communiceren met uw webtoepassing. Cross Origin Resource Sharing (CORS) mag niet toestaan dat alle domeinen toegang hebben tot uw webtoepassing.|
|App service|20|CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw functie-app| Sta toe dat alleen de vereiste domeinen met uw functie toepassing kunnen communiceren. Cross Origin Resource Sharing (CORS) mag niet toestaan dat alle domeinen toegang hebben tot uw functie toepassing.|
|Reken resources (batch)|1|Metrische waarschuwings regels moeten worden geconfigureerd voor batch-accounts|Metrische waarschuwings regels configureren voor een batch-account en de metrische groep inschakelen Complete gebeurtenissen en groep verwijderen start gebeurtenissen verwijderen|
|Reken resources (Service Fabric)|10|Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor client verificatie|Voer client verificatie alleen uit via Azure Active Directory in Service Fabric.|
|Reken bronnen (Automation-account)|5|De variabelen van het Automation-account moeten worden versleuteld|Versleuteling van variabele-assets van Automation-account inschakelen bij het opslaan van gevoelige gegevens.|
|Reken bronnen (zoeken)|5|Het inschakelen van Diagnostische logboeken voor zoek services controleren|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |
|Reken resources (Service Bus)|5|Diagnostische logboeken in Service Bus moeten worden ingeschakeld|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |
|Reken bronnen (stream Analytics)|5|Diagnostische logboeken in Azure Stream Analytics moeten worden ingeschakeld|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |
|Reken resources (batch)|5|Diagnostische logboeken inschakelen in batch-accounts|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |
|Reken bronnen (Event Hub)|5|Diagnostische logboeken in Event hub moeten worden ingeschakeld|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |
|Reken resources (Logic apps)|5|Diagnostische logboeken inschakelen in Logic Apps|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |
|Reken resources (Service Fabric)|15|Stel de eigenschap ClusterProtectionLevel in op EncryptAndSign in Service Fabric|Service Fabric biedt drie beveiligings niveaus (geen, ondertekening en EncryptAndSign) voor communicatie tussen knoop punten met behulp van een primair cluster certificaat. Stel het beveiligings niveau in om ervoor te zorgen dat alle knoop punt-naar-knooppunt berichten zijn versleuteld en digitaal zijn ondertekend. |
|Reken resources (Service Bus)|1|Alle autorisatie regels verwijderen behalve RootManageSharedAccessKey uit Service Bus naam ruimte |Service Bus-clients mogen geen toegangs beleid op naam ruimte niveau gebruiken dat toegang biedt tot alle wacht rijen en onderwerpen in een naam ruimte. Als u wilt uitlijnen met het beveiligings model met minimale bevoegdheden, moet u toegangs beleid maken op het niveau van de entiteit voor wacht rijen en onderwerpen om alleen toegang te verlenen aan de specifieke entiteit.|
|Reken bronnen (Event Hub)|1|Alle autorisatie regels behalve RootManageSharedAccessKey moeten worden verwijderd uit de Event hub-naam ruimte|Event hub-clients mogen geen toegangs beleid op naam ruimte niveau gebruiken dat toegang biedt tot alle wacht rijen en onderwerpen in een naam ruimte. Als u wilt uitlijnen met het beveiligings model met minimale bevoegdheden, moet u toegangs beleid maken op het niveau van de entiteit voor wacht rijen en onderwerpen om alleen toegang te verlenen aan de specifieke entiteit.|
|Reken bronnen (Event Hub)|5|Autorisatie regels voor de Event hub-entiteit moeten worden gedefinieerd|Verificatie regels controleren op de Event hub-entiteit om beperkte toegang te verlenen.|
|Machine|50|Bewakings agent op uw computers installeren|Installeer de bewakings agent om het verzamelen van gegevens, het scannen van updates, het scannen van basis lijnen en Endpoint Protection op elke computer in te scha kelen.|
|Machine|50|Automatische inrichting en gegevens verzameling inschakelen voor uw abonnementen |Schakel automatische inrichting en gegevens verzameling voor computers in uw abonnementen in om het verzamelen van gegevens, het scannen van updates, het scannen van basis lijnen en Endpoint Protection op elke computer die is toegevoegd aan uw abonnementen in te scha kelen.|
|Machine|40|Problemen met de status van de bewakings agent op uw computers oplossen|Los de problemen met de bewakings agent op uw computers op door de instructies in de hand leiding voor probleem oplossing te volgen voor volledige Security Center beveiliging| 
|Machine|40|Problemen met de status van Endpoint Protection op uw computers oplossen|Los de problemen met de bewakings agent op uw computers op door de instructies in de hand leiding voor het oplossen van problemen te volgen voor volledige Security Center beveiliging.|
|Machine|40|Problemen met ontbrekende scan gegevens op uw computers oplossen|Problemen met ontbrekende scan gegevens op virtuele machines en computers oplossen. Ontbrekende scan gegevens op uw computers leiden tot ontbrekende beveiligings beoordelingen zoals het scannen van updates, het scannen van basis lijnen en het scannen van de eindpunt beveiligings oplossing ontbreekt.|
|Machine|40|Systeem updates moeten worden geïnstalleerd op uw computers|Ontbrekende systeem beveiliging en essentiële updates installeren om uw virtuele Windows-en Linux-machines en-computers te beveiligen
|Machine|15|Een firewall voor webtoepassingen toevoegen| Implementeer een Web Application Firewall-oplossing (WAF) om uw webtoepassingen te beveiligen. |
|Machine|40|De versie van het besturings systeem bijwerken voor de Cloud service rollen|Werk de versie van het besturings systeem (OS) bij voor de Cloud service rollen in de meest recente versie die beschikbaar is voor uw besturingssysteem familie.|
|Machine|35|Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld|Herstel beveiligings problemen in beveiligings configuratie op uw computers om ze te beschermen tegen aanvallen.|
|Machine|35|Beveiligings problemen in de beveiligings configuratie in uw containers herstellen|Herstel beveiligings problemen in beveiligings configuratie op computers waarop docker is geïnstalleerd om ze te beschermen tegen aanvallen.|
|Machine|25|Besturings elementen voor adaptieve toepassingen inschakelen|Schakel toepassings beheer in om te bepalen welke toepassingen kunnen worden uitgevoerd op uw virtuele machines die zich in azure bevinden. Dit helpt uw Vm's te beschermen tegen schadelijke software. Security Center gebruikt machine learning voor het analyseren van de toepassingen die op elke virtuele machine worden uitgevoerd en helpt u bij het Toep assen van regels die deze intelligentie gebruiken toe te passen. Deze mogelijkheid vereenvoudigt het proces van het configureren en onderhouden van regels voor het toestaan van toepassingen.|
|Machine|20|Endpoint Protection-oplossing op uw computers installeren|Installeer een Endpoint Protection-oplossing op uw virtuele machines om ze te beschermen tegen bedreigingen en beveiligings problemen.|
|Machine|20|Start uw machines opnieuw op om systeem updates toe te passen|Start uw machines opnieuw op om de systeem updates toe te passen en de computer te beveiligen tegen beveiligings problemen.|
|Machine|15|Schijf versleuteling moet worden toegepast op virtuele machines|Versleutel de schijven van uw virtuele machine met Azure Disk Encryption voor virtuele Windows-en Linux-machines. Azure Disk Encryption (ADE) maakt gebruik van de industrie standaard BitLocker-functie van Windows en de DM-cryptografie functie van Linux om besturings systeem-en gegevens schijven te versleutelen om uw gegevens te beschermen en te beveiligen en om te voldoen aan de beveiliging en naleving van uw organisatie verplichtingen in de Azure-sleutel kluis van de klant. Als aan uw nalevings-en beveiligings vereiste de gegevens end moet worden versleuteld met behulp van uw versleutelings sleutels, inclusief versleuteling van de tijdelijke (lokaal gekoppelde tijdelijke) schijf, moet u gebruikmaken van Azure Disk Encryption. Managed Disks worden standaard versleuteld op rest met behulp van Azure Storage-service versleuteling, waarbij de versleutelings sleutels door micro soft beheerde sleutels zijn in Azure. Als deze voldoet aan uw nalevings-en beveiligings vereisten, kunt u gebruikmaken van de standaard Managed Disk Encryption om te voldoen aan uw vereisten.|
|Machine|30|Een oplossing voor de evaluatie van beveiligings problemen installeren op uw virtuele machines|Een oplossing voor de evaluatie van beveiligings problemen installeren op uw virtuele machines|
|Machine|15|Een firewall voor webtoepassingen toevoegen| Implementeer een Web Application Firewall-oplossing (WAF) om uw webtoepassingen te beveiligen. |
|Machine|30|Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken|Virtuele machines waarvoor een beveiligings probleem van een oplossing van derden wordt geïmplementeerd, worden voortdurend beoordeeld op basis van de beveiligings problemen van toepassingen en besturings systemen. Wanneer dergelijke beveiligings problemen worden gevonden, zijn deze beschikbaar voor meer informatie als onderdeel van de aanbeveling.|
|Machine|30|Een oplossing voor de evaluatie van beveiligings problemen installeren op uw virtuele machines|Een oplossing voor de evaluatie van beveiligings problemen installeren op uw virtuele machines|
|Machine|1|Virtuele machines moeten worden gemigreerd naar nieuwe AzureRM-resources|Gebruik Azure Resource Manager voor uw virtuele machines om beveiligings uitbreidingen te bieden zoals: sterker toegangs beheer (RBAC), betere controle, implementatie en beheer op basis van Resource Manager, toegang tot beheerde identiteiten, toegang tot de sleutel kluis voor geheimen, Verificatie op basis van Azure AD en ondersteuning voor Tags en resource groepen voor eenvoudiger beveiligings beheer. |
|Machine|30|Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken|Virtuele machines waarvoor een beveiligings probleem van een oplossing van derden wordt geïmplementeerd, worden voortdurend beoordeeld op basis van de beveiligings problemen van toepassingen en besturings systemen. Wanneer dergelijke beveiligings problemen worden gevonden, zijn deze beschikbaar voor meer informatie als onderdeel van de aanbeveling.|
|Schaalset voor virtuele machines |4|Diagnostische logboeken in Virtual Machine Scale Sets moeten worden ingeschakeld|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor opnieuw maken voor onderzoek doeleinden. Dit is handig als er een beveiligings incident optreedt of als uw netwerk is aangetast.|
|Schaalset voor virtuele machines|35|Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld|Herstel beveiligings problemen in de beveiligings configuratie van de schaal sets voor virtuele machines om ze te beschermen tegen aanvallen. |
|Schaalset voor virtuele machines|5|Endpoint Protection-status fouten herstellen op schaal sets voor virtuele machines|Herstel de status van eindpunt beveiliging op uw schaal sets voor virtuele machines om ze te beschermen tegen bedreigingen en beveiligings problemen. |
|Schaalset voor virtuele machines|10|Endpoint Protection moet worden geïnstalleerd op virtuele machines|Installeer een Endpoint Protection-oplossing op uw schaal sets voor virtuele machines om ze te beschermen tegen bedreigingen en beveiligings problemen. |
|Schaalset voor virtuele machines|40|Systeem updates op virtuele-machine schaal sets moeten worden geïnstalleerd|Installeer de ontbrekende systeem beveiliging en essentiële updates om de schaal sets voor virtuele Windows-en Linux-machines te beveiligen. |
|


## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor meer informatie over de aanbevelingen die van toepassing zijn op andere Azure-resource typen:

* [Identiteit en toegang bewaken in Azure Security Center](security-center-identity-access.md)
* [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)
* [Uw Azure SQL-service beveiligen in Azure Security Center](security-center-sql-service-recommendations.md)
