---
title: Beveiligingsaanbevelingen voor wachtrijopslag
titleSuffix: Azure Storage
description: Meer informatie over beveiligingsaanbevelingen voor wachtrijopslag. Door deze richtlijnen uit te voeren, u voldoen aan uw beveiligingsverplichtingen zoals beschreven in ons gedeelde verantwoordelijkheidsmodel.
services: storage
author: tamram
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 8bb56db9eed962ac8f8202c61a7446527c15dfc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060904"
---
# <a name="security-recommendations-for-queue-storage"></a>Beveiligingsaanbevelingen voor wachtrijopslag

Dit artikel bevat beveiligingsaanbevelingen voor wachtrijopslag. Door deze aanbevelingen uit te voeren, u voldoen aan uw beveiligingsverplichtingen zoals beschreven in ons gedeelde verantwoordelijkheidsmodel. Lees [Gedeelde verantwoordelijkheden voor cloud computing voor](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)meer informatie over wat Microsoft doet om de verantwoordelijkheden van serviceproviders te vervullen.

Sommige aanbevelingen in dit artikel kunnen automatisch worden gecontroleerd door Azure Security Center. Azure Security Center is de eerste verdedigingslinie bij het beveiligen van uw resources in Azure. Zie [Het Azure Security Center?](../../security-center/security-center-intro.md)

Azure Security Center analyseert periodiek de beveiligingsstatus van uw Azure-bronnen om potentiële beveiligingsproblemen te identificeren. Het geeft u vervolgens aanbevelingen over hoe ze aan te pakken. Zie [Beveiligingsaanbevelingen in Azure Security Center](../../security-center/security-center-recommendations.md)voor meer informatie over aanbevelingen voor Azure Security Center.

## <a name="data-protection"></a>Gegevensbeveiliging

| Aanbeveling | Opmerkingen | Security Center |
|-|----|--|
| Het implementatiemodel azure resource manager gebruiken | Maak nieuwe opslagaccounts met behulp van het Azure Resource Manager-implementatiemodel voor belangrijke beveiligingsverbeteringen, waaronder superieure toegangscontrole (RBAC) en controle, implementatie en beheer op basis van Resource Manager, toegang tot beheerde identiteiten, toegang naar Azure Key Vault voor geheimen en verificatie en autorisatie op basis van Azure AD voor toegang tot Azure Storage-gegevens en -bronnen. Migreer indien mogelijk bestaande opslagaccounts die het klassieke implementatiemodel gebruiken om Azure Resource Manager te gebruiken. Zie overzicht azure [resource manager](/azure/azure-resource-manager/resource-group-overview)voor meer informatie over Azure Resource Manager. | - |
| De **vereiste** optie Beveiligde overdracht inschakelen op al uw opslagaccounts | Wanneer u de vereiste optie **Veilige overdracht** inschakelt, moeten alle aanvragen die tegen het opslagaccount worden ingediend, plaatsvinden via beveiligde verbindingen. Alle aanvragen die via HTTP worden gedaan, mislukken. Zie [Veilige overdracht in Azure Storage vereisen](../common/storage-require-secure-transfer.md)voor meer informatie. | [Ja](../../security-center/security-center-sql-service-recommendations.md) |
| Geavanceerde bescherming tegen bedreigingen inschakelen voor al uw opslagaccounts | Geavanceerde bedreigingsbeveiliging voor Azure Storage biedt een extra laag beveiligingsinformatie die ongebruikelijke en mogelijk schadelijke pogingen detecteert om toegang te krijgen tot opslagaccounts of deze te exploiteren. Beveiligingswaarschuwingen worden geactiveerd in Azure Security Center wanneer er afwijkingen in activiteit optreden en worden ook via e-mail verzonden naar abonnementsbeheerders, met details over verdachte activiteiten en aanbevelingen over het onderzoeken en oplossen van bedreigingen. Zie [Geavanceerde bedreigingsbeveiliging voor Azure Storage voor](../common/storage-advanced-threat-protection.md)meer informatie. | [Ja](../../security-center/security-center-sql-service-recommendations.md) |
| SAS-tokens (Shared Access Signature) beperken tot alleen HTTPS-verbindingen | Als u HTTPS nodig hebt wanneer een client een SAS-token gebruikt om toegang te krijgen tot wachtrijgegevens, u het risico op afluisteren minimaliseren. Zie [Beperkte toegang tot Azure Storage-bronnen verlenen met behulp van gedeelde toegangshandtekeningen (SAS)](../common/storage-sas-overview.md)voor meer informatie. | - |

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

| Aanbeveling | Opmerkingen | Security Center |
|-|----|--|
| Azure AD (Azure Directory) gebruiken om de toegang tot wachtrijgegevens te autoriseren | Azure AD biedt superieure beveiliging en gebruiksgemak via gedeelde sleutel voor het toestaan van aanvragen voor wachtrijopslag. Zie [Toegang tot Azure-blobs en wachtrijen autoriseren met Azure Active Directory](../common/storage-auth-aad.md)voor meer informatie. | - |
| Houd rekening met de belangrijkste van de minste bevoegdheden bij het toewijzen van machtigingen aan een Azure AD-beveiligingsprincipal via RBAC | Wanneer u een rol toewijst aan een gebruiker, groep of toepassing, verleent u die beveiligingsprincipal alleen de machtigingen die nodig zijn om hun taken uit te voeren. Het beperken van de toegang tot bronnen helpt zowel onbedoeld als kwaadwillig misbruik van uw gegevens te voorkomen. | - |
| Uw accounttoegangssleutels beveiligen met Azure Key Vault | Microsoft raadt aan Azure AD te gebruiken om aanvragen voor Azure Storage te autoriseren. Als u echter de machtiging Gedeelde sleutel moet gebruiken, u uw accountsleutels beveiligen met Azure Key Vault. U de sleutels ophalen uit de sleutelkluis tijdens runtime, in plaats van ze op te slaan met uw toepassing. | - |
| Uw accountsleutels periodiek opnieuw genereren | Als u de accountsleutels periodiek draait, neemt het risico af dat uw gegevens worden blootgesteld aan kwaadwillende actoren. | - |
| Houd rekening met de belangrijkste van de minste bevoegdheden bij het toewijzen van machtigingen aan een SAS | Wanneer u een SAS maakt, geeft u alleen de machtigingen op die de client nodig heeft om de functie uit te voeren. Het beperken van de toegang tot bronnen helpt zowel onbedoeld als kwaadwillig misbruik van uw gegevens te voorkomen. | - |
| Heb een herroepingsplan voor elke SAS die u aan klanten uitgeeft | Als een SAS wordt gecompromitteerd, wilt u die SAS zo snel mogelijk intrekken. Als u een SAS voor gebruikersdelegatie wilt intrekken, trekt u de sleutel voor gebruikersdelegatie in om alle handtekeningen die aan die sleutel zijn gekoppeld, snel ongeldig te maken. Als u een service-SAS wilt intrekken die is gekoppeld aan een opgeslagen toegangsbeleid, u het beleid voor opgeslagen toegang verwijderen, de naam van het beleid wijzigen of de vervaldatum wijzigen in een tijd die in het verleden is. Zie [Beperkte toegang tot Azure Storage-bronnen verlenen met behulp van gedeelde toegangshandtekeningen (SAS)](../common/storage-sas-overview.md)voor meer informatie.  | - |
| Als een service SAS niet is gekoppeld aan een opgeslagen toegangsbeleid, stelt u de vervaldatum in op een uur of minder | Een service SAS die niet is gekoppeld aan een beleid voor opgeslagen toegang kan niet worden ingetrokken. Om deze reden wordt het beperken van de vervaldatum aanbevolen, zodat de SAS een uur of minder geldig is. | - |

## <a name="networking"></a>Netwerken

| Aanbeveling | Opmerkingen | Security Center |
|-|----|--|
| Firewallregels inschakelen | Configureer firewallregels om de toegang tot uw opslagaccount te beperken tot aanvragen die afkomstig zijn van opgegeven IP-adressen of -bereiken, of uit een lijst met subnetten in een Azure Virtual Network (VNet). Zie Azure File Sync proxy [en firewall-instellingen](../files/storage-sync-files-firewall-and-proxy.md)voor meer informatie over het configureren van firewallregels. | - |
| Vertrouwde Microsoft-services toegang geven tot het opslagaccount | Het inschakelen van firewallregels voor uw opslagaccount blokkeert standaard binnenkomende aanvragen voor gegevens, tenzij de aanvragen afkomstig zijn van een service die actief is binnen een Azure Virtual Network (VNet) of van toegestane openbare IP-adressen. Aanvragen die worden geblokkeerd, zijn ook die van andere Azure-services, van de Azure-portal, van logboekregistratie- en metrische services, enzovoort. U aanvragen van andere Azure-services toestaan door een uitzondering toe te voegen om vertrouwde Microsoft-services toegang te geven tot het opslagaccount. Zie [Azure File Sync-proxy- en firewall-instellingen](../files/storage-sync-files-firewall-and-proxy.md)voor meer informatie over het toevoegen van een uitzondering voor vertrouwde Microsoft-services.| - |
| Privéeindpunten gebruiken | Een privéeindpunt wijst een privé-IP-adres van uw Azure Virtual Network (VNet) toe aan het opslagaccount. Het beveiligt al het verkeer tussen uw VNet en de opslagaccount via een privéverbinding. Zie [Privé verbinden met een opslagaccount met Azure Private Endpoint](../../private-link/create-private-endpoint-storage-portal.md)voor meer informatie over privéeindpunten. | - |
| Netwerktoegang beperken tot specifieke netwerken | Het beperken van de toegang tot netwerken die toegang vereisen, vermindert de blootstelling van uw bronnen aan netwerkaanvallen. | [Ja](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>Logboekregistratie/controle

| Aanbeveling | Opmerkingen | Security Center |
|-|----|--|
| Bijhouden hoe aanvragen zijn geautoriseerd | Azure Storage-logboekregistratie inschakelen om bij te houden hoe elke aanvraag voor Azure Storage is geautoriseerd. De logboeken geven aan of een verzoek anoniem is gedaan, met behulp van een OAuth 2.0-token, met behulp van Gedeelde sleutel of met behulp van een gedeelde toegangshandtekening (SAS). Zie [Azure Storage Analytics Analytics logging](../common/storage-analytics-logging.md)voor meer informatie. | - |

## <a name="next-steps"></a>Volgende stappen

- [Documentatie over beveiliging in Azure](https://docs.microsoft.com//azure/security/)
- [Beveiligde ontwikkelingsdocumentatie.](https://docs.microsoft.com/azure/security/develop/)
