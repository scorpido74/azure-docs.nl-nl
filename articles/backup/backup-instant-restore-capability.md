---
title: Azure Instant Restore-mogelijkheden
description: Azure Instant Restore Capability en Veelgestelde vragen voor VM-back-upstack, Resource Manager-implementatiemodel
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 7cf437c6b20ea6b688e8e93e401cf71ef0260888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705425"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Profiteer van verbeterde back-up- en herstelprestaties met azure backup instant restore-mogelijkheden

> [!NOTE]
> Op basis van feedback van gebruikers hernoemen we **VM backup stack V2** naar **Instant Restore** om verwarring met Azure Stack-functionaliteit te verminderen.
> Alle Azure-back-upgebruikers zijn nu geüpgraded naar **Instant Restore.**

Het nieuwe model voor Instant Restore biedt de volgende functieverbeteringen:

* Mogelijkheid om snapshots te gebruiken die zijn gemaakt als onderdeel van een back-uptaak die beschikbaar is voor herstel zonder te wachten tot de gegevensoverdracht naar de kluis is voltooid. Hiermee wordt de wachttijd verkort voor momentopnamen naar de kluis moeten worden gekopieerd voordat het herstel wordt geactiveerd.
* Vermindert back-up- en hersteltijden door momentopnamen standaard twee dagen te bewaren. Deze standaardmomentopnamewaarde is configureerbaar voor elke waarde tussen 1 tot 5 dagen.
* Ondersteunt schijfformaten tot 32 TB. Het wijzigen van het formaat van schijven wordt niet aanbevolen door Azure Backup.
* Ondersteunt standaard SSD-schijven, samen met standaard HDD-schijven en Premium SSD-schijven.
* Mogelijkheid om een onbeheerde VM's originele opslagaccounts (per schijf) te gebruiken bij het herstellen. Deze mogelijkheid bestaat zelfs wanneer de VM schijven heeft die zijn verdeeld over opslagaccounts. Het versnelt herstelbewerkingen voor een breed scala aan VM-configuraties.
* Voor back-ups van VM's die premium opslag gebruiken, raden we u aan *om 50%* vrije ruimte toe te wijzen aan de totale toegewezen opslagruimte, die **alleen** nodig is voor de eerste back-up. De 50% vrije ruimte is geen vereiste voor back-ups nadat de eerste back-up is voltooid.

## <a name="whats-new-in-this-feature"></a>Nieuwe functies in deze functie

Momenteel bestaat de back-uptaak uit twee fasen:

1. Een VM-momentopname maken.
2. Een VM-momentopname overbrengen naar de kluis Azure Recovery Services.

Een herstelpunt wordt pas als gemaakt beschouwd nadat fase 1 en 2 zijn voltooid. Als onderdeel van deze upgrade wordt een herstelpunt gemaakt zodra de momentopname is voltooid en dit herstelpunt van momentopnametype kan worden gebruikt om een herstel uit te voeren met dezelfde herstelstroom. U dit herstelpunt in de Azure-portal identificeren met behulp van 'momentopname' als herstelpunttype en nadat de momentopname naar de kluis is overgebracht, wordt het type herstelpunt gewijzigd in 'momentopname en kluis'.

![Back-uptaak in vm-back-upstack Resource Manager-implementatiemodel - opslag en kluis](./media/backup-azure-vms/instant-rp-flow.png)

Standaard worden momentopnamen twee dagen bewaard. Deze functie maakt herstel bewerking van deze snapshots daar door het kappen van de hersteltijden. Het vermindert de tijd die nodig is om gegevens uit de kluis te transformeren en te kopiëren.

## <a name="feature-considerations"></a>Functieoverwegingen

* Momentopnamen worden samen met de schijven opgeslagen om het maken van herstelpunten te stimuleren en herstelbewerkingen te versnellen. Als gevolg hiervan ziet u opslagkosten die overeenkomen met momentopnamen die tijdens deze periode zijn gemaakt.
* Incrementele momentopnamen worden opgeslagen als paginablobs. Alle gebruikers die onbeheerde schijven gebruiken, worden in rekening gebracht voor de momentopnamen die zijn opgeslagen in hun lokale opslagaccount. Aangezien de herstelpuntverzamelingen die worden gebruikt door Managed VM-back-ups blobmomentopnamen op het onderliggende opslagniveau gebruiken, ziet u voor beheerde schijven kosten die overeenkomen met blobmomentopnameprijzen en deze incrementeel zijn.
* Voor premium opslagaccounts tellen de momentopnamen die zijn gemaakt voor directe herstelpunten mee voor de 10-TB-limiet van toegewezen ruimte.
* U krijgt de mogelijkheid om de momentopnameretentie te configureren op basis van de herstelbehoeften. Afhankelijk van de vereiste u de momentopnameretentie instellen op een minimum van één dag in het back-upbeleidsblad zoals hieronder uitgelegd. Dit helpt u kosten te besparen voor het bewaren van momentopnamen als u niet vaak herstelt.
* Het is een een directionele upgrade, eenmaal opgewaardeerd naar Instant restore, kun je niet meer teruggaan.

>[!NOTE]
>Met deze instant herstelupgrade wordt de duur van het bewaren van momentopnamen van alle klanten **(nieuw en bestaand beide inbegrepen)** ingesteld op een standaardwaarde van twee dagen. U de duur echter instellen op een waarde tussen 1 tot 5 dagen.

## <a name="cost-impact"></a>Kostenimpact

De incrementele momentopnamen worden opgeslagen in het opslagaccount van de VM, dat wordt gebruikt voor direct herstel. Incrementele momentopname betekent dat de ruimte die wordt ingenomen door een momentopname gelijk is aan de ruimte die wordt ingenomen door pagina's die zijn geschreven nadat de momentopname is gemaakt. Facturering is nog steeds voor de gebruikte ruimte per GB die wordt ingenomen door de momentopname en de prijs per GB is hetzelfde als vermeld op de [prijspagina](https://azure.microsoft.com/pricing/details/managed-disks/). Voor VM's die onbeheerde schijven gebruiken, zijn de momentopnamen te zien in het menu voor het VHD-bestand van elke schijf. Voor beheerde schijven worden momentopnamen opgeslagen in een bron voor herstelpuntverzameling in een aangewezen resourcegroep en zijn de momentopnamen zelf niet direct zichtbaar.

>[!NOTE]
> Momentopnameretentie is vastgesteld op 5 dagen voor wekelijks beleid.

## <a name="configure-snapshot-retention"></a>Momentopnameretentie configureren

### <a name="using-azure-portal"></a>Azure Portal gebruiken

In de Azure-portal ziet u een veld dat is toegevoegd in het **vm-back-upbeleid** onder de sectie **Direct herstel.** U de bewaarduur van momentopnamen wijzigen van het **VM Backup Policy-blad** voor alle VM's die zijn gekoppeld aan het specifieke back-upbeleid.

![Direct herstellen van de mogelijkheid](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>PowerShell gebruiken

>[!NOTE]
> Vanaf Az PowerShell versie 1.6.0 u de bewaarperiode voor momentopnamen direct herstellen in beleid bijwerken met PowerShell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

De standaardmomentopnameretentie voor elk beleid is ingesteld op twee dagen. De gebruiker kan de waarde wijzigen in een minimum van 1 en maximaal vijf dagen. Voor wekelijks beleid is het bewaren van momentopnames vastgesteld op vijf dagen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Wat zijn de kostenimplicaties van Instant restore?

Momentopnamen worden samen met de schijven opgeslagen om het maken van herstelpunten te versnellen en bewerkingen te herstellen. Als gevolg hiervan ziet u opslagkosten die overeenkomen met de momentopnameretentie die is geselecteerd als onderdeel van het VM-back-upbeleid.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Bezetten de momentopnamen die zijn gemaakt voor direct herstelpunt in Premium Storage-accounts de momentopnamelimiet van 10 TB?

Ja, voor premium opslagaccounts bezetten de momentopnamen die zijn gemaakt voor direct herstelpunt 10 TB toegewezen momentopnameruimte.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Hoe werkt het bewaren van momentopnamen gedurende de periode van vijf dagen?

Elke dag wordt een nieuwe momentopname gemaakt, waarna er vijf afzonderlijke incrementele momentopnamen zijn. De grootte van de momentopname is afhankelijk van de gegevensverloop, die in de meeste gevallen rond de 2%-7% liggen.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Is een momentopname met een directe herstelmomentopname een incrementele momentopname of volledige momentopname?

Momentopnamen die worden gemaakt als onderdeel van de mogelijkheid voor direct herstellen, zijn incrementele momentopnamen.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Hoe kan ik de geschatte kostenstijging berekenen als gevolg van de functie direct herstellen?

Het hangt af van de churn van de VM. In een stabiele toestand u ervan uitgaan dat de kostenstijging = momentopnameperiode dagelijkse verloop per VM-opslagkosten per GB is.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Als het hersteltype voor een herstelpunt 'Momentopname en kluis' is en ik een herstelbewerking uitvoert, welk hersteltype wordt gebruikt?

Als het hersteltype "momentopname en kluis" is, wordt het herstel automatisch uitgevoerd vanaf de lokale momentopname, die veel sneller zal zijn in vergelijking met het herstel vanuit de kluis.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Wat gebeurt er als ik de bewaarperiode van herstelpunt (Tier 2) minder selecteer dan de bewaarperiode van momentopname (Tier1) ?

Het nieuwe model staat het verwijderen van het herstelpunt (Tier2) niet toe, tenzij de momentopname (Tier1) wordt verwijderd. We raden u aan de bewaarperiode (Tier2) te plannen die groter is dan de bewaarperiode voor momentopnamen.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Waarom bestaat mijn momentopname zelfs na de ingestelde bewaarperiode in back-upbeleid?

Als het herstelpunt een momentopname heeft en dat is de nieuwste BESCHIKBARE RP, blijft deze behouden tot het moment dat er een volgende succesvolle back-up is. Dit is volgens de ontworpen "garbage collection" (GC) beleid van vandaag dat mandaten ten minste een laatste RP altijd aanwezig zijn in het geval alle back-ups verder op mislukken als gevolg van een probleem in de VM. In normale scenario's worden RP's in maximaal 24 uur na het verstrijken ervan opgeschoond.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Ik heb geen Instant Restore-functionaliteit nodig. Kan het uitgeschakeld worden?

Instant restore-functie is voor iedereen ingeschakeld en kan niet worden uitgeschakeld. U de momentopnameretentie tot een minimum van één dag beperken.

>[!NOTE]
> **Azure Backup ondersteunt nu selectieve schijfback-up en herstel met behulp van de Azure Virtual Machine-back-upoplossing.**
>
>Tegenwoordig ondersteunt Azure Backup een back-up van alle schijven (besturingssysteem en gegevens) in een VM samen met behulp van de back-upoplossing voor virtuele machines. Met de functionaliteit van de uitgesloten schijf krijgt u een optie om een of enkele back-ups te maken van de vele gegevensschijven in een vm. Dit biedt een efficiënte en kosteneffectieve oplossing voor uw back-up- en herstelbehoeften. Elk herstelpunt bevat gegevens van de schijven die zijn opgenomen in de back-upbewerking, waarmee u verder een subset van schijven laten herstellen vanaf het gegeven herstelpunt tijdens de herstelbewerking. Dit geldt voor het herstellen van zowel de momentopname als de kluis.
>
>**Als u zich wilt aanmelden voor de preview, schrijft u ons opAskAzureBackupTeam@microsoft.com**
