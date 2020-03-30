---
title: Back-ups beheren met toegangsbeheer op basis van rollen
description: Gebruik Toegangsbeheer op basis van rollen om de toegang tot back-upbeheerbewerkingen in de kluis van Recovery Services te beheren.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: e2e32ac6981635e3b9885119fdf397783ac32cc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273200"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Toegangsbeheer op basis van rollen gebruiken om herstelpunten voor Azure Backup te beheren

Met op rollen gebaseerd toegangsbeheer (RBAC) beschikt u over geavanceerd toegangsbeheer voor Azure. Met op rollen gebaseerd toegangsbeheer kunt u taken scheiden binnen uw team en alleen de mate van toegang verlenen aan gebruikers die nodig is om de taken uit te voeren.

> [!IMPORTANT]
> Rollen die door Azure Backup worden geleverd, zijn beperkt tot acties die kunnen worden uitgevoerd in Azure-portal of via REST API- of Recovery Services-kluis PowerShell- of CLI-cmdlets. Acties die worden uitgevoerd in azure backup Agent Client UI of System Center Data Protection Manager UI of Azure Backup Server UI zijn buiten controle van deze rollen.

Azure Backup biedt drie ingebouwde rollen om back-upbeheerbewerkingen te beheren. Meer informatie over [ingebouwde Azure RBAC-rollen](../role-based-access-control/built-in-roles.md)

* [Back-upbijdrager](../role-based-access-control/built-in-roles.md#backup-contributor) - Deze rol heeft alle machtigingen om back-ups te maken en te beheren, behalve het verwijderen van de kluis van Recovery Services en het geven van toegang aan anderen. Stel je deze rol voor als beheerder van back-upbeheer die elke back-upbeheerbewerking kan uitvoeren.
* [Back-upoperator](../role-based-access-control/built-in-roles.md#backup-operator) - Deze rol heeft machtigingen voor alles wat een bijdrager doet, behalve het verwijderen van back-upbeleid en het beheren van back-upbeleid. Deze rol is gelijk aan bijdrager, behalve dat deze geen destructieve bewerkingen kan uitvoeren, zoals het stoppen van back-ups met het verwijderen van gegevens of het verwijderen van de registratie van on-premises resources.
* [Back-uplezer](../role-based-access-control/built-in-roles.md#backup-reader) - Deze rol heeft machtigingen om alle back-upbeheerbewerkingen te bekijken. Stel je voor dat deze rol een controlerend persoon is.

Als u uw eigen rollen wilt definiëren voor nog meer controle, raadpleegt u hoe u aangepaste rollen in Azure RBAC [bouwen.](../role-based-access-control/custom-roles.md)

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Back-upingebouwde rollen toewijzen aan back-upbeheeracties

In de volgende tabel worden de acties voor back-upbeheer en de bijbehorende minimale RBAC-rol vastgelegd die nodig is om die bewerking uit te voeren.

| Beheeroperatie | Minimale RBAC-rol vereist | Vereiste toepassingsgebied |
| --- | --- | --- |
| Een Recovery Services-kluis maken | Back-upbijdrager | Resourcegroep die de kluis bevat |
| Back-up van Azure VM's inschakelen | Back-upoperator | Resourcegroep die de kluis bevat |
| | Inzender voor virtuele machines | VM-bron |
| On-demand back-up van VM | Back-upoperator | Recovery Services-kluis |
| VM herstellen | Back-upoperator | Recovery Services-kluis |
| | Inzender | Resourcegroep waarin VM wordt geïmplementeerd |
| | Inzender voor virtuele machines | Bron VM die een back-up |
| VM-back-up voor niet-beheerde schijven herstellen | Back-upoperator | Recovery Services-kluis |
| | Inzender voor virtuele machines | Bron VM die een back-up |
| | Inzender voor opslagaccounts | Opslagaccountbron waar schijven worden hersteld |
| Beheerde schijven herstellen vanuit VM-back-up | Back-upoperator | Recovery Services-kluis |
| | Inzender voor virtuele machines | Bron VM die een back-up |
| | Inzender voor opslagaccounts | Tijdelijk opslagaccount geselecteerd als onderdeel van herstellen om gegevens uit de kluis te bewaren voordat ze worden geconverteerd naar beheerde schijven |
| | Inzender | Resourcegroep waaraan beheerde schijf(en) wordt hersteld |
| Afzonderlijke bestanden herstellen vanuit VM-back-up | Back-upoperator | Recovery Services-kluis |
| | Inzender voor virtuele machines | Bron VM die een back-up |
| Back-upbeleid maken voor Azure VM-back-up | Back-upbijdrager | Recovery Services-kluis |
| Back-upbeleid van Azure VM-back-up wijzigen | Back-upbijdrager | Recovery Services-kluis |
| Back-upbeleid van Azure VM-back-up verwijderen | Back-upbijdrager | Recovery Services-kluis |
| Back-ups stoppen (met gegevens bewaren of gegevens verwijderen) op VM-back-up | Back-upbijdrager | Recovery Services-kluis |
| On-premises Windows Server/client/SCDPM of Azure Backup Server registreren | Back-upoperator | Recovery Services-kluis |
| Geregistreerde on-premises Windows Server/client/SCDPM of Azure Backup Server verwijderen | Back-upbijdrager | Recovery Services-kluis |

> [!IMPORTANT]
> Als u VM-inzender opgeeft bij een VM-bronbereik en op Back-up klikt als onderdeel van vm-instellingen, wordt het scherm 'Back-up inschakelen' geopend, ook al is er al een back-up van VM als de oproep om de back-upstatus te verifiëren werkt alleen op abonnementsniveau. Om dit te voorkomen, gaat u naar de kluis en opent u de weergave back-upitem van de vm of geeft u de rol VM-inzender op op abonnementsniveau op.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Minimale functievereisten voor de back-up van azure-bestandsshare

In de volgende tabel worden de acties voor back-upbeheer en de bijbehorende rol vastgelegd die nodig zijn om de bewerking voor azure-bestandsshare uit te voeren.

| Beheeroperatie | Vereiste rol | Resources |
| --- | --- | --- |
| Back-up van Azure File-shares inschakelen | Back-upbijdrager |Recovery Services-kluis |
| |Opslagaccount | Bron van opslaginzender inzender |
| On-demand back-up van VM | Back-upoperator | Recovery Services-kluis |
| Bestandsshare herstellen | Back-upoperator | Recovery Services-kluis |
| | Inzender voor opslagaccounts | Opslagaccountbronnen waar herstelbron en doelbestandsshares aanwezig zijn |
| Afzonderlijke bestanden herstellen | Back-upoperator | Recovery Services-kluis |
| |Inzender voor opslagaccounts|Opslagaccountbronnen waar herstelbron en doelbestandsshares aanwezig zijn |
| Beveiliging stoppen |Back-upbijdrager | Recovery Services-kluis |
| Opslagaccount uitschrijven uit kluis |Back-upbijdrager | Recovery Services-kluis |
| |Inzender voor opslagaccounts | Opslagaccountbron|

## <a name="next-steps"></a>Volgende stappen

* [Op rollen gebaseerd toegangsbeheer:](../role-based-access-control/role-assignments-portal.md)aan de slag met RBAC in de Azure-portal.
* Meer informatie over het beheren van toegang met:
  * [Powershell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure-CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST-API](../role-based-access-control/role-assignments-rest.md)
* [Probleemoplossing voor toegangsbeheer op basis van rollen:](../role-based-access-control/troubleshooting.md)ontvang suggesties voor het oplossen van veelvoorkomende problemen.
