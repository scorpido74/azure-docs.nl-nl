---
title: Instellingen voor Vault Diagnostics op schaal configureren
description: Instellingen voor diagnostische instellingen voor Logboekanalyse configureren voor alle kluizen in een bepaald bereik met Azure Policy
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: c92957cab3e1ed745e7031e3c6f32e7ecda550a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584503"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Instellingen voor Vault Diagnostics op schaal configureren

De rapportageoplossing van Azure Backup maakt gebruik van Log Analytics (LA). Om de gegevens van een bepaalde kluis naar LA te sturen, moet er een [diagnostische instelling](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events) voor die kluis worden gemaakt.

Vaak kan het handmatig toevoegen van een diagnostische instelling per kluis een omslachtige taak zijn. Bovendien moet elke nieuwe kluis die is gemaakt ook diagnostische instellingen hebben ingeschakeld om rapporten voor deze kluis te kunnen bekijken. 

Azure Backup biedt een ingebouwd [Azure-beleid](https://docs.microsoft.com/azure/governance/policy/)om het maken van diagnostische instellingen op schaal te vereenvoudigen (met LA als bestemming). Met dit beleid wordt een LA-diagnose-instelling toegevoegd aan alle kluizen in een bepaald abonnement of resourcegroep. In de volgende secties vindt u instructies over het gebruik van dit beleid.

## <a name="supported-scenarios"></a>Ondersteunde scenario 's

* Het beleid kan in één keer worden toegepast op alle vaults van Recovery Services in een bepaald abonnement (of op een resourcegroep binnen het abonnement). De gebruiker die het beleid toegeeft, moet 'Eigenaar' toegang hebben tot het abonnement waaraan het beleid is toegewezen.

* De LA Workspace zoals opgegeven door de gebruiker (naar welke diagnostische gegevens worden verzonden) kan zich in een ander abonnement bevinden dan de kluizen waaraan het beleid is toegewezen. De gebruiker moet 'Reader', 'Contributor' of 'Owner' toegang hebben tot het abonnement waarin de opgegeven LA Workspace bestaat.

* Het bereik van de beheergroep wordt momenteel niet ondersteund.

* Het ingebouwde beleid is momenteel niet beschikbaar in nationale clouds.

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Het ingebouwde beleid toewijzen aan een scope

Als u het beleid voor kluizen in het vereiste bereik wilt toewijzen, voert u de onderstaande stappen uit:

1. Meld u aan bij de Azure-portal en navigeer naar het **beleidsdashboard.**
2. Selecteer **Definities** in het linkermenu om een lijst met alle ingebouwde beleidsregels in Azure Resources te krijgen.
3. Filter de lijst voor **Categorie=Monitoring**. Zoek het beleid met de naam **[Preview]: Diagnostische instellingen voor Vault voor Herstelservices implementeren in de werkruimte Log Analytics voor resourcespecifieke categorieën**.

![Blad beleidsdefinitie](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Klik op de naam van het beleid. U wordt doorgestuurd naar de gedetailleerde definitie voor dit beleid.

![Gedetailleerde beleidsdefinitie](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Klik op de knop **Toewijzen** boven aan het blad. Hiermee wordt u omgeleid naar het **blad Beleid toewijzen.**

6. Klik **onder Basisbeginselen**op de drie puntjes naast het veld **Bereik.** Dit opent een rechter contextblad waar u het abonnement selecteren voor het beleid waarop u moet worden toegepast. U ook optioneel een resourcegroep selecteren, zodat het beleid alleen wordt toegepast voor kluizen in een bepaalde resourcegroep.

![Basisbeginselen van beleidstoewijzing](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. Voer **onder Parameters**de volgende gegevens in:

* **Profielnaam** - De naam die wordt toegewezen aan de diagnostische instellingen die door het beleid zijn gemaakt.
* **Log Analytics Workspace** - De Log Analytics Workspace waaraan de diagnostische instelling moet worden gekoppeld. Diagnostische gegevens van alle kluizen in het bereik van de beleidstoewijzing worden naar de opgegeven LA Workspace gepusht.

* **Naam van uitsluitingstag (optioneel) en uitsluitingstagwaarde (optioneel)** - U ervoor kiezen om kluizen met een bepaalde tagnaam en -waarde uit te sluiten van de beleidstoewijzing. Als u bijvoorbeeld **niet** wilt dat er een diagnostische instelling wordt toegevoegd aan de kluizen met een tag 'isTest' ingesteld op de waarde 'ja', moet u 'isTest' invoeren in het veld **Uitsluitingstagnaam** en 'ja' in het veld Waarde van de **uitsluitingstag.** Als een (of beide) van deze twee velden leeg blijft, wordt het beleid toegepast op alle relevante kluizen, ongeacht de tags die ze bevatten.

![Parameters beleidstoewijzing](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Een hersteltaak maken** - Zodra het beleid is toegewezen aan een scope, worden alle nieuwe kluizen die in dat bereik zijn gemaakt, automatisch la-diagnostische instellingen geconfigureerd (binnen 30 minuten vanaf het moment van creatie van de kluis). Als u een diagnostische instelling wilt toevoegen aan bestaande kluizen in het bereik, u een hersteltaak activeren tijdens beleidstoewijzingstijd. Als u een hersteltaak wilt activeren, schakelt u het selectievakje **Een hersteltaak maken in.** 

![Beleidstoewijzingsherstel](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Navigeer naar het tabblad **Controleren+Maken** en klik op **Maken**.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Onder welke voorwaarden is de saneringstaak van toepassing op een kluis?

De hersteltaak wordt toegepast op kluizen die niet voldoen aan de definitie van het beleid. Een kluis voldoet niet als deze aan een van de volgende voorwaarden voldoet:

* Er is geen diagnose-instelling aanwezig voor de kluis.
* Diagnostische instellingen zijn aanwezig voor de kluis, maar geen van de instellingen heeft **alle** resourcespecifieke gebeurtenissen ingeschakeld met LA als bestemming en **Resourcespecifiek** geselecteerd in de schakelknop. 

Dus zelfs als een gebruiker een kluis heeft met de gebeurtenis AzureBackupReport ingeschakeld in de AzureDiagnostics-modus (die wordt ondersteund door back-uprapporten), is de hersteltaak nog steeds van toepassing op deze kluis, omdat de resourcespecifieke modus de aanbevolen manier is om diagnostische instellingen te maken, [in de toekomst.](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event)

Als een gebruiker een kluis heeft met slechts een subset van de zes resourcespecifieke gebeurtenissen ingeschakeld, is de hersteltaak van toepassing op deze kluis, omdat back-uprapporten alleen werken zoals verwacht als alle zes resourcespecifieke gebeurtenissen zijn ingeschakeld.

> [!NOTE]
>
> Als een kluis een bestaande diagnostische instelling heeft met een **subset van resourcespecifieke** categorieën ingeschakeld, geconfigureerd om gegevens naar een bepaalde LA Workspace te verzenden, bijvoorbeeld 'Workspace X', mislukt de hersteltaak (alleen voor die kluis) als de bestemming LA Workspace die in de beleidstoewijzing wordt **aangeboden, dezelfde** 'Workspace X' is. 
>
>Als de gebeurtenissen die zijn ingeschakeld door twee verschillende diagnostische instellingen op dezelfde resource elkaar in een bepaalde vorm **overlappen,** kunnen de instellingen niet dezelfde LA Workspace hebben als de bestemming. U moet deze fout handmatig oplossen door naar de desbetreffende kluis te navigeren en een diagnostische instelling te configureren met een andere LA Workspace als bestemming.
>
> Houd er rekening mee dat de hersteltaak **niet** mislukt als de bestaande diagnostische instelling als alleen AzureBackupReport is ingeschakeld met Workspace X als bestemming, omdat er in dit geval geen overlapping is tussen de gebeurtenissen die zijn ingeschakeld door de bestaande instelling en de gebeurtenissen die zijn ingeschakeld door de instelling die is gemaakt door de hersteltaak.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het gebruik van back-uprapporten](https://docs.microsoft.com/azure/backup/configure-reports)
* [Meer informatie over Azure Policy](https://docs.microsoft.com/azure/governance/policy/)
* [Azure Policy gebruiken om back-ups automatisch in te schakelen voor alle VM's in een bereik](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)
