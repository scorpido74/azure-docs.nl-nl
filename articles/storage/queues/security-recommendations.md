---
title: Beveiligings aanbevelingen voor wachtrij opslag
titleSuffix: Azure Storage
description: Meer informatie over beveiligings aanbevelingen voor wachtrij opslag. Als u deze richt lijnen implementeert, kunt u voldoen aan uw beveiligings verplichtingen, zoals beschreven in ons gedeelde verantwoordelijkheids model.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 1315e1f81ee32a544b623b7f3ffad61a7e7275d8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486285"
---
# <a name="security-recommendations-for-queue-storage"></a>Beveiligings aanbevelingen voor wachtrij opslag

Dit artikel bevat beveiligings aanbevelingen voor wachtrij opslag. Als u deze aanbevelingen implementeert, kunt u voldoen aan uw beveiligings verplichtingen, zoals beschreven in ons gedeelde verantwoordelijkheids model. Lees de [gedeelde verantwoordelijkheden voor Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)voor meer informatie over wat micro soft doet aan de verantwoordelijkheden van de service provider.

Enkele van de aanbevelingen in dit artikel kunnen automatisch worden bewaakt door Azure Security Center. Azure Security Center is de eerste verdedigings linie bij het beveiligen van uw resources in Azure. Voor informatie over Azure Security Center raadpleegt u het [Azure Security Center?](../../security-center/security-center-intro.md).

Azure Security Center regel matig de beveiligings status van uw Azure-resources analyseren om mogelijke beveiligings problemen te identificeren. Vervolgens krijgt u aanbevelingen over hoe u deze kunt aanpakken. Zie [beveiligings aanbevelingen in azure Security Center](../../security-center/security-center-recommendations.md)voor meer informatie over Azure Security Center aanbevelingen.

## <a name="data-protection"></a>Databeveiliging

| Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|----|--|
| Het Azure Resource Manager-implementatie model gebruiken | Maak nieuwe opslag accounts met behulp van het Azure Resource Manager-implementatie model voor belang rijke verbeteringen in de beveiliging, waaronder een superieure toegangs beheer (RBAC) en controle, implementatie en governance op basis van Resource Manager, toegang tot beheerde identiteiten, toegang Azure Key Vault voor geheimen en verificatie en autorisatie op basis van Azure AD voor toegang tot Azure Storage gegevens en bronnen. Als dat mogelijk is, migreert u bestaande opslag accounts die gebruikmaken van het klassieke implementatie model om Azure Resource Manager te gebruiken. Zie [Azure Resource Manager Overview](/azure/azure-resource-manager/resource-group-overview)voor meer informatie over Azure Resource Manager. | - |
| Schakel de optie **veilige overdracht vereist** in al uw opslag accounts in | Wanneer u de optie **beveiligde overdracht vereist** inschakelt, moeten alle aanvragen voor het opslag account via beveiligde verbindingen plaatsvinden. Aanvragen die via HTTP worden verzonden, mislukken. Zie [veilige overdracht vereisen in azure Storage](../common/storage-require-secure-transfer.md)voor meer informatie. | [Ja](../../security-center/security-center-sql-service-recommendations.md) |
| Geavanceerde beveiliging tegen bedreigingen inschakelen voor al uw opslag accounts | Advanced Threat Protection voor Azure Storage biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van opslag accounts worden gedetecteerd. Beveiligings waarschuwingen worden in Azure Security Center geactiveerd wanneer afwijkingen in de activiteit optreden en ook via e-mail worden verzonden naar abonnements beheerders, met details over verdachte activiteiten en aanbevelingen voor het onderzoeken en oplossen van bedreigingen. Zie [Advanced Threat Protection voor Azure Storage](../common/storage-advanced-threat-protection.md)voor meer informatie. | [Ja](../../security-center/security-center-sql-service-recommendations.md) |
| Alleen SAS-tokens (Shared Access Signature) beperken tot HTTPS-verbindingen | HTTPS vereisen wanneer een client een SAS-token gebruikt voor toegang tot de wachtrij gegevens helpt het risico op inbreuk te minimaliseren. Zie [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../common/storage-sas-overview.md)voor meer informatie. | - |

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

| Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|----|--|
| Azure Active Directory (Azure AD) gebruiken om toegang tot wachtrij gegevens te autoriseren | Azure AD biedt een superieure beveiliging en gebruiks gemak voor de gedeelde sleutel voor het machtigen van aanvragen voor de wachtrij opslag. Zie [toegang tot Azure-blobs en-wacht rijen toestaan met Azure Active Directory](../common/storage-auth-aad.md)voor meer informatie. | - |
| Houd bij het toewijzen van machtigingen aan een Azure AD-beveiligings-principal via RBAC de belangrijkste bevoegdheid. | Wanneer u een rol aan een gebruiker, groep of toepassing toewijst, moet u die beveiligings-principal alleen de machtigingen verlenen die nodig zijn om hun taken uit te voeren. Het beperken van de toegang tot bronnen voor komt zowel onbedoelde als kwaad aardige misbruik van uw gegevens. | - |
| De toegangs sleutels van uw account beveiligen met Azure Key Vault | Micro soft raadt u aan Azure AD te gebruiken om aanvragen voor Azure Storage te autoriseren. Als u echter gedeelde sleutel autorisatie moet gebruiken, kunt u uw account sleutels beveiligen met Azure Key Vault. U kunt de sleutels uit de sleutel kluis tijdens runtime ophalen in plaats van deze op te slaan in uw toepassing. | - |
| Uw account sleutels periodiek opnieuw genereren | Als u de account sleutels draait, vermindert u het risico dat uw gegevens worden blootgesteld aan kwaad aardige actors. | - |
| Houd bij het toewijzen van machtigingen aan een SAS de hoofd van de minimale bevoegdheid | Wanneer u een SAS maakt, geeft u alleen de machtigingen op die de client nodig heeft om zijn functie uit te voeren. Het beperken van de toegang tot bronnen voor komt zowel onbedoelde als kwaad aardige misbruik van uw gegevens. | - |
| Er is een intrekkings plan aanwezig voor elke SAS die u verleent aan clients | Als een SAS is aangetast, wilt u die SA'S zo snel mogelijk intrekken. Als u een SA'S voor gebruikers overdracht wilt intrekken, trekt u de sleutel voor gebruikers overdracht in om alle hand tekeningen die zijn gekoppeld aan die sleutel, ongeldig te maken. Als u een service-SA'S wilt intrekken die aan een opgeslagen toegangs beleid is gekoppeld, kunt u het opgeslagen toegangs beleid verwijderen, de naam van het beleid wijzigen of de verloop tijd wijzigen in een tijd die in het verleden ligt. Zie [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../common/storage-sas-overview.md)voor meer informatie.  | - |
| Als een service-SAS niet is gekoppeld aan een beleid voor opgeslagen toegang, stelt u de verloop tijd in op één uur of minder | Een service-SA'S die niet aan een opgeslagen toegangs beleid is gekoppeld, kunnen niet worden ingetrokken. Daarom wordt de verloop tijd beperkt zodat de SAS één uur geldig of minder wordt aanbevolen. | - |

## <a name="networking"></a>Networking

| Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|----|--|
| Firewall regels inschakelen | Configureer firewall regels om de toegang tot uw opslag account te beperken tot aanvragen die afkomstig zijn van opgegeven IP-adressen of bereiken of van een lijst met subnetten in een Azure Virtual Network (VNet). Zie [Azure file sync proxy-en Firewall instellingen](../files/storage-sync-files-firewall-and-proxy.md)voor meer informatie over het configureren van firewall regels. | - |
| Vertrouwde micro soft-Services toegang geven tot het opslag account | Door de firewall regels voor uw opslag account in te scha kelen, worden binnenkomende aanvragen voor gegevens standaard geblokkeerd, tenzij de aanvragen afkomstig zijn van een service die binnen een Azure-Virtual Network (VNet) of van toegestane open bare IP-adressen valt. Aanvragen die zijn geblokkeerd omvatten die van andere Azure-services vanuit de Azure-portal van logboekregistratie en metrische gegevens over services, enzovoort. U kunt aanvragen van andere Azure-Services toestaan door een uitzonde ring toe te voegen om vertrouwde micro soft-Services toegang te geven tot het opslag account. Zie [Azure file sync proxy-en Firewall instellingen](../files/storage-sync-files-firewall-and-proxy.md)voor meer informatie over het toevoegen van een uitzonde ring voor vertrouwde micro soft-Services.| - |
| Privé-eind punten gebruiken | Een persoonlijk eind punt wijst een privé-IP-adres van uw Azure Virtual Network (VNet) toe aan het opslag account. Hiermee wordt al het verkeer tussen uw VNet en het opslag account beveiligd via een privé-koppeling. Zie voor meer informatie over privé-eind punten [persoonlijke verbinding maken met een opslag account met behulp van een persoonlijk Azure-eind punt](../../private-link/create-private-endpoint-storage-portal.md). | - |
| Netwerk toegang tot specifieke netwerken beperken | Het beperken van netwerk toegang tot netwerken die als host fungeren voor clients die toegang vereisen, worden de bloot stelling van uw resources aan netwerk aanvallen beperkt. | [Ja](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>Loggen/bewaken

| Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|----|--|
| Bijhouden hoe aanvragen worden geautoriseerd | Schakel Azure Storage logboek registratie in om bij te houden hoe elke aanvraag voor Azure Storage is geautoriseerd. De logboeken geven aan of een aanvraag anoniem is gemaakt door gebruik te maken van een OAuth 2,0-token, met behulp van gedeelde sleutel of door gebruik te maken van een Shared Access Signature (SAS). Zie [Azure Storage Analytics-logboek registratie](../common/storage-analytics-logging.md)voor meer informatie. | - |

## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor Azure-beveiliging](https://docs.microsoft.com//azure/security/)
- [Documentatie voor beveiligde ontwikkel aars](https://docs.microsoft.com/azure/security/develop/).
