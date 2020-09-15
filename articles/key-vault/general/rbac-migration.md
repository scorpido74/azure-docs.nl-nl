---
title: Migreren naar op rollen gebaseerd toegangs beheer van Azure | Microsoft Docs
description: Meer informatie over het migreren van beleid voor kluis toegang naar Azure-rollen.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: e06a7a759c712b47f3a725a3c49a660226da6a09
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064149"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-preview-permission-model"></a>Migreren van beleid voor kluis toegang naar een machtigings model op basis van op rollen gebaseerd toegangs beheer (preview-versie)

Het beleid model van kluis toegang is een bestaand autorisatie systeem dat is ingebouwd in Key Vault om toegang te bieden tot sleutels, geheimen en certificaten. U kunt de toegang beheren door afzonderlijke machtigingen toe te wijzen aan de beveiligingsprincipal (gebruiker, groep, Service-Principal, beheerde identiteit) op Key Vault bereik. 

Toegangs beheer op basis van rollen (Azure RBAC) van Azure is een autorisatie systeem dat is gebaseerd op [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) dat een nauw keurig toegangs beheer van Azure-resources biedt. Azure RBAC voor Key Vault sleutels, geheimen en certificaten toegangs beheer is momenteel beschikbaar als open bare preview. Met Azure RBAC beheert u de toegang tot resources door rollen toewijzingen te maken, die uit drie elementen bestaan: beveiligingsprincipal, roldefinitie (vooraf gedefinieerde set met machtigingen) en bereik (groep resources of afzonderlijke resources). Zie voor meer informatie [Azure Role-based Access Control (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview).

Voordat u migreert naar Azure RBAC, is het belang rijk dat u de voor delen en beperkingen begrijpt.

Azure RBAC-belangrijkste voor delen ten opzichte van het beleid voor kluis toegang:
- Biedt Unified Access Control model voor Azure-resources-dezelfde API voor Azure-Services
- Gecentraliseerd toegangs beheer voor beheerders: alle Azure-resources beheren in één weer gave
- Geïntegreerd met [privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) voor toegangs beheer op basis van tijd
- Weiger toewijzingen: de mogelijkheid om beveiligings-principal uit te sluiten van een bepaald bereik. Zie voor meer informatie [begrijpen Azure deny Assignments](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)

Nadelen van Azure RBAC:
- Latentie voor roltoewijzingen: het kan enkele minuten duren voordat de roltoewijzing wordt toegepast. Het beleid voor kluis toegang wordt direct toegewezen.
- Beperkt aantal roltoewijzingen-2000 rollen toewijzingen per abonnement versus 1024 toegangs beleid per Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Toegangs beleid voor Azure-rollen toewijzen

Azure RBAC heeft verschillende ingebouwde rollen van Azure die u kunt toewijzen aan gebruikers, groepen, service-principals en beheerde identiteiten. Als de ingebouwde rollen niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen [aangepaste Azure-rollen](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) maken.

Key Vault ingebouwde rollen voor sleutels, certificaten en geheimen toegangs beheer:
- Key Vault beheerder (preview-versie)
- Key Vault lezer (preview-versie)
- Key Vault-certificerings medewerker (preview-versie)
- Key Vault crypto-functionaris (preview-versie)
- Key Vault crypto grafie-gebruiker (preview-versie)
- Key Vaulte geheimen-auditeur (preview-versie)
- Key Vault geheimen gebruiker (preview-versie)

Zie [ingebouwde rollen van Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) voor meer informatie over bestaande ingebouwde rollen

Een kluis toegangs beleid kan worden toegewezen met afzonderlijk geselecteerde machtigingen of met vooraf gedefinieerde machtigings sjablonen.

Vooraf gedefinieerde machtigings sjablonen voor toegangs beleid:
- Sleutel, geheim, certificaat beheer
- Sleutel &-geheim beheer
- Certificaat beheer van geheime &
- Sleutel beheer
- Geheim beheer
- Certificaatbeheer
- SQL Server-connector
- Azure Data Lake Storage of Azure Storage
- Azure Backup
- Exchange Online-klant sleutel
- Share point online-klant sleutel
- Azure Information BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Beleids sjablonen voor Azure-rollen toewijzen
| Access-beleids sjabloon | Operations | Azure-rol |
| --- | --- | --- |
| Sleutel, geheim, certificaat beheer | Sleutels: alle bewerkingen <br>Certificaten: alle bewerkingen<br>Geheimen: alle bewerkingen | Key Vault beheerder (preview-versie) |
| Sleutel &-geheim beheer | Sleutels: alle bewerkingen <br>Geheimen: alle bewerkingen| Key Vault crypto-functionaris (preview-versie)<br> Key Vaulte geheimen-auditeur (preview-versie)|
| Certificaat beheer van geheime & | Certificaten: alle bewerkingen <br>Geheimen: alle bewerkingen| Key Vaulte-certificerings instanties (preview-versie)<br> Key Vaulte geheimen-auditeur (preview-versie)|
| Sleutel beheer | Sleutels: alle bewerkingen| Key Vault crypto-functionaris (preview-versie)|
| Geheim beheer | Geheimen: alle bewerkingen| Key Vaulte geheimen-auditeur (preview-versie)|
| Certificaatbeheer | Certificaten: alle bewerkingen | Key Vaulte-certificerings instanties (preview-versie)|
| SQL Server-connector | Sleutels: ophalen, lijst, terugloop sleutel, uitpakken sleutel | Versleuteling van crypto-service Key Vault (preview-versie)|
| Azure Data Lake Storage of Azure Storage | Sleutels: ophalen, lijst, uitpakken sleutel | N.v.t.<br> Aangepaste rol vereist|
| Azure Backup | Sleutels: Get, List, backup<br> Certificaat: ophalen, lijst, back-up | N.v.t.<br> Aangepaste rol vereist|
| Exchange Online-klant sleutel | Sleutels: ophalen, lijst, terugloop sleutel, uitpakken sleutel | Versleuteling van crypto-service Key Vault (preview-versie)|
| Exchange Online-klant sleutel | Sleutels: ophalen, lijst, terugloop sleutel, uitpakken sleutel | Versleuteling van crypto-service Key Vault (preview-versie)|
| Azure Information BYOK | Sleutels: ophalen, ontsleutelen, ondertekenen | N.v.t.<br>Aangepaste rol vereist|


## <a name="assignment-scopes-mapping"></a>Toewijzing van toewijzings bereiken  

Met Azure RBAC voor Key Vault kunt u rollen toewijzen in de volgende bereiken:
- Beheergroep
- Abonnement
- Resourcegroep
- Key Vault resource
- Afzonderlijke sleutel, geheim en certificaat

Het machtigings model kluis toegangs beleid is beperkt tot het toewijzen van beleid op Key Vault resource niveau, dat 

Over het algemeen is het best practice een sleutel kluis per toepassing te hebben en de toegang te beheren op het niveau van de sleutel kluis. Er zijn scenario's voor het beheren van toegang met andere scopes kan het toegangs beheer vereenvoudigen.

- * * Infra structuur, beveiligings beheerders en Opera tors: groep van sleutel kluizen beheren op het niveau van de beheer groep, het abonnement of de resource groep met het beleid voor kluis toegang vereist het onderhouden van beleids regels voor elke sleutel kluis. Met Azure RBAC kunt u één roltoewijzing maken bij de beheer groep, het abonnement of de resource groep. Deze toewijzing is van toepassing op alle nieuwe sleutel kluizen die onder hetzelfde bereik worden gemaakt. In dit scenario is het raadzaam om Privileged Identity Management te gebruiken met Just-in-time-toegang via permanente toegang.
 
- * * Toepassingen: er zijn scenario's waarin toepassingen geheim moeten delen met andere toepassingen. Met behulp van kluis toegang is er een afzonderlijke sleutel kluis gemaakt om te voor komen dat u toegang krijgt tot alle geheimen. Met Azure RBAC kunt u in plaats daarvan een rol toewijzen met een bereik voor afzonderlijke geheimen door gebruik te maken van een enkele sleutel kluis.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Beleid voor kluis toegang tot Azure RBAC-migratie stappen
Er zijn veel verschillen tussen het machtigings model voor het toegangs beleid van Azure RBAC en kluis. Om te voor komen dat er storingen optreden tijdens de migratie, wordt u aangeraden om de volgende stappen uit te voeren.
 
1. **Rollen identificeren en toewijzen**: Bepaal welke ingebouwde rollen zijn gebaseerd op de bovenstaande toewijzings tabel en maak aangepaste rollen wanneer dat nodig is. Wijs rollen toe aan bereiken op basis van de richt lijnen voor het toewijzen van scopes. Zie [toegang bieden tot Key Vault met een op rollen gebaseerd toegangs beheer (preview) van Azure](rbac-guide.md) voor meer informatie over het toewijzen van rollen aan de sleutel kluis
1. Functie **toewijzing valideren**: roltoewijzingen kunnen enkele minuten in beslag nemen in azure RBAC. Voor instructies voor het controleren van roltoewijzingen raadpleegt u [rollen toewijzingen in het bereik](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) weer geven
1. **Bewaking en waarschuwingen voor de sleutel kluis configureren**: het is belang rijk om logboek registratie en installatie waarschuwingen in te scha kelen voor uitzonde ringen voor toegang geweigerd. Zie [controle en waarschuwingen voor Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/alert) voor meer informatie.
1. **Stel het machtigings model op basis van op rollen gebaseerd toegangs beheer in op Key Vault**: wanneer het Azure RBAC-machtigings model wordt ingeschakeld, worden alle bestaande beleids regels voor toegang ongeldig. Als er een fout optreedt, kan het machtigings model weer worden omgezet met alle bestaande beleids regels voor toegang die ongewijzigd blijven.

> [!NOTE]
> Wanneer Azure RBAC-machtigings model is ingeschakeld, mislukken alle scripts die het bijwerken van het toegangs beleid proberen uit te voeren. Het is belang rijk dat u deze scripts bijwerkt voor het gebruik van Azure RBAC.

## <a name="troubleshooting"></a>Problemen oplossen
-  De roltoewijzing werkt niet na enkele minuten: er zijn situaties waarin roltoewijzingen langer kunnen duren. Het is belang rijk om de logica voor opnieuw proberen te schrijven in code om deze gevallen te behandelen.
- Roltoewijzingen zijn verdwenen toen Key Vault werd verwijderd (zacht verwijderd) en hersteld. het is momenteel een beperking van de functie voor het voorlopig verwijderen van alle Azure-Services. Het is vereist om alle roltoewijzingen na het herstel opnieuw te maken.    

## <a name="learn-more"></a>Meer informatie

- [Overzicht van Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Zelf studie voor aangepaste rollen](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-cli)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
