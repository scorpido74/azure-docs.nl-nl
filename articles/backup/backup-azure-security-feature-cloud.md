---
title: Beveiligings functies voor het beveiligen van Cloud werkbelastingen die gebruikmaken van Azure Backup
description: Meer informatie over het gebruik van beveiligings functies in Azure Backup om back-ups veiliger te maken.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: dacurwin
ms.openlocfilehash: f0e4540f3f5ab3fdbb5953cbf100c5fdc2b2542a
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622008"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Beveiligings functies voor het beveiligen van Cloud werkbelastingen die gebruikmaken van Azure Backup

Zorgen over beveiligingsproblemen, zoals malware, ransomware en inbraak nemen toe. Deze beveiligingsproblemen kunnen kostbaar zijn, zowel met betrekking tot geld als gegevens. Azure Backup biedt nu beveiligings functies die u helpen bij het beveiligen van back-upgegevens, zelfs na het verwijderen. Een dergelijke functie is zacht verwijderen. Met zacht verwijderen, zelfs als een schadelijke actor de back-up van een virtuele machine verwijdert (of als er per ongeluk back-upgegevens worden verwijderd), worden de back-upgegevens 14 extra dagen bewaard, zodat het back-upitem zonder gegevens verlies kan worden hersteld. Deze extra 14 dagen retentie van back-upgegevens in de status ' voorlopig verwijderen ' zijn niet van toepassing op de klant.

> [!NOTE]
> Met zacht verwijderen worden verwijderde back-upgegevens alleen beveiligd. Als een virtuele machine wordt verwijderd zonder een back-up, worden de gegevens niet bewaard met de functie voor voorlopig verwijderen. Alle resources moeten worden beveiligd met Azure Backup om volledige tolerantie te garanderen.
>

## <a name="soft-delete"></a>Voorlopig verwijderen

### <a name="supported-regions"></a>Ondersteunde regio’s

Voorlopig verwijderen wordt momenteel ondersteund in de West-Centraal VS, Azië-oost, Canada-centraal, Canada-oost, Frankrijk-centraal, Frankrijk-zuid, Korea-centraal, Korea-zuid, UK-zuid, UK-west, Australië-oost, Australië-Zuid-Oost, Europa-noord, VS-West, West VS2, VS-midden, Zuid Azië-oost, Noord-Centraal VS, Zuid-Centraal VS, Japan-Oost, Japan-West, India-Zuid, India-midden, India-West, VS-Oost 2, Zwitserland-noord, Zwitserland-west en alle nationale regio's.

### <a name="soft-delete-for-vms"></a>Voorlopig verwijderen voor Vm's

1. Voor het verwijderen van de back-upgegevens van een virtuele machine moet de back-up worden gestopt. Ga in het Azure Portal naar uw Recovery Services-kluis, klik met de rechter muisknop op het back-upitem en kies **back-up stoppen**.

   ![Scherm opname van Azure Portal back-upitems](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. In het volgende venster krijgt u de mogelijkheid om de back-upgegevens te verwijderen of te bewaren. Als u **back-upgegevens verwijderen** kiest en vervolgens **back-up stoppen**, wordt de back-up van de virtuele machine niet definitief verwijderd. In plaats daarvan worden de back-upgegevens 14 dagen in de modus voorlopig verwijderd bewaard. Als er **back-upgegevens verwijderen** wordt gekozen, wordt een waarschuwing voor verwijderen van e-mail verzonden naar de geconfigureerde e-mail-id waarmee de gebruiker 14 dagen op de hoogte blijft van de uitgebreide Bewaar periode voor back-upgegevens. Er wordt ook een e-mail waarschuwing verzonden op de twaalfde dag, waarin wordt gemeld dat er nog twee resterende dagen zijn om de verwijderde gegevens te Resurrect. De verwijdering wordt uitgesteld tot de vijftiende dag, wanneer er permanent wordt verwijderd en er wordt een definitieve e-mail waarschuwing verzonden over het permanent verwijderen van de gegevens.

   ![Scherm opname van Azure Portal, back-upvenster stoppen](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Gedurende die 14 dagen, in de Recovery Services kluis, wordt de voorlopig verwijderde virtuele machine weer gegeven met een rood pictogram ' zacht verwijderen ' ernaast.

   ![Scherm opname van Azure Portal, VM in de status zacht verwijderen](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Als er tijdelijke, verwijderde back-upitems aanwezig zijn in de kluis, kan de kluis op dat moment niet worden verwijderd. Probeer het verwijderen van de kluis nadat de back-upitems definitief zijn verwijderd en er is geen item met de status zacht verwijderd links in de kluis.

4. Als u de voorlopig verwijderde VM wilt herstellen, moet deze eerst worden verwijderd. Als u de verwijdering ongedaan wilt maken, kiest u de voorlopig verwijderde virtuele machine en klikt u vervolgens op **verwijderen**.

   ![Scherm opname van Azure Portal, verwijderen van virtuele machine ongedaan maken](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Er wordt een venster weer gegeven waarin wordt vermeld dat als ongedaan maken is gekozen, alle herstel punten voor de virtuele machine worden verwijderd en beschikbaar zijn voor het uitvoeren van een herstel bewerking. De virtuele machine wordt bewaard in de status ' beveiliging met behoud van gegevens stoppen ' met back-ups onderbroken en back-upgegevens blijven behouden zonder dat er back-upbeleid van kracht is.

   ![Scherm opname van Azure Portal, bevestig de verwijdering van de virtuele machine](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Op dit moment kunt u ook de virtuele machine herstellen door **VM herstellen** uit het gekozen herstel punt te selecteren.  

   ![Scherm opname van Azure Portal, VM-optie herstellen](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > De garbage collector voert alleen verlopen herstel punten uit en reinigt nadat de gebruiker de **back-** upbewerking voor hervatten heeft uitgevoerd.

5. Nadat het verwijderen is voltooid, wordt de status weer gegeven als back-up stoppen met gegevens behouden en vervolgens kunt u **back-up hervatten**kiezen. Met de **back-** upbewerking hervatten wordt het back-upitem teruggezet in de actieve status, gekoppeld aan een back-upbeleid dat is geselecteerd door de gebruiker die de back-up-en bewaar schema's definieert.

   ![Scherm opname van Azure Portal, back-upoptie hervatten](./media/backup-azure-security-feature-cloud/resume-backup.png)

In dit stroom diagram worden de verschillende stappen en statussen van een back-upitem weer gegeven:

![Levens cyclus van een zacht verwijderd back-upitem](./media/backup-azure-security-feature-cloud/lifecycle.png)

Zie het gedeelte Veelgestelde [vragen](backup-azure-security-feature-cloud.md#frequently-asked-questions) hieronder voor meer informatie.

## <a name="disabling-soft-delete"></a>Tijdelijke verwijdering uitschakelen

Voorlopig verwijderen is standaard ingeschakeld op nieuwe kluizen. Als de functie voor het voorlopig verwijderen van de beveiliging is uitgeschakeld, worden de back-upgegevens niet per ongeluk of kwaad aardig verwijderd beschermd. Zonder de functie voor voorlopig verwijderen worden alle verwijderde beveiligde items onmiddellijk verwijderd, zonder dat u de mogelijkheid hebt om te herstellen. Omdat back-upgegevens in de status ' voorlopig verwijderen ' geen kosten in rekening worden gebracht aan de klant, wordt het uitschakelen van deze functie niet aanbevolen. De enige omstandigheid waarbij het uitschakelen van de functie voor het verwijderen van uw beveiligde items naar een nieuwe kluis moet worden aangeraden, is dat de 14 dagen die vereist zijn voor het verwijderen en opnieuw beveiligen van de gegevens (zoals in een test omgeving), niet kunnen worden gewacht.

### <a name="prerequisites-for-disabling-soft-delete"></a>Vereisten voor het uitschakelen van de functie voor voorlopig verwijderen

- Het in-of uitschakelen van voorlopig verwijderen voor kluizen (zonder beveiligde items) kan alleen worden uitgevoerd door de Azure Portal. Dit is van toepassing op:
  - Nieuw gemaakte kluizen die geen beveiligde items bevatten
  - Bestaande kluizen waarvan de beveiligde items zijn verwijderd en verlopen (na de vaste Bewaar periode van 14 dagen)
- Als de functie voor voorlopig verwijderen is uitgeschakeld voor de kluis, kunt u deze opnieuw inschakelen, maar u kunt deze keuze niet terugdraaien en weer uitschakelen als de kluis beveiligde items bevat.
- U kunt geen tijdelijke verwijdering uitschakelen voor kluizen die beveiligde items of items bevatten met de status zacht verwijderd. Als u dit wilt doen, voert u de volgende stappen uit:
  - Stop de beveiliging van verwijderde gegevens voor alle beveiligde items.
  - Wacht tot de 14 dagen na het bewaren van de veiligheid verloopt.
  - Tijdelijke verwijdering uitschakelen.

Als u zacht verwijderen wilt uitschakelen, controleert u of aan de vereisten wordt voldaan en volgt u deze stappen:

1. Ga in het Azure Portal naar uw kluis en ga naar **instellingen** -> **Eigenschappen**.
2. Selecteer in het deel venster Eigenschappen de optie **beveiligings instellingen** -> **Update**.
3. Selecteer in het deel venster beveiligings instellingen onder voorlopig verwijderen de optie **uitschakelen**.

![Tijdelijke verwijdering uitschakelen](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

## <a name="other-security-features"></a>Andere beveiligings functies

### <a name="storage-side-encryption"></a>Versleuteling van opslag side

Azure Storage worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Versleuteling beschermt uw gegevens en helpt u om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Gegevens in Azure Storage worden transparant versleuteld en ontsleuteld met 256-bits AES-versleuteling, een van de krach tigste blok cijfers die beschikbaar zijn en is compatibel met FIPS 140-2. Azure Storage versleuteling lijkt op BitLocker-versleuteling in Windows. Azure Backup versleutelt gegevens automatisch voordat ze worden opgeslagen. Azure Storage worden gegevens ontsleuteld voordat ze worden opgehaald.  

Binnen Azure worden gegevens in transit tussen Azure Storage en de kluis beveiligd door HTTPS. Deze gegevens blijven op het Azure-backbone-netwerk.

Zie [Azure Storage versleuteling voor Data-at-rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)voor meer informatie.

### <a name="vm-encryption"></a>VM-versleuteling

U kunt met behulp van de Azure Backup-Service back-ups maken van virtuele Windows-of Linux Azure-machines (Vm's) met versleutelde schijven. Zie [back-up en herstel van versleutelde virtuele machines met Azure backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)voor instructies.

### <a name="protection-of-azure-backup-recovery-points"></a>Beveiliging van Azure Backup herstel punten

Opslag accounts die worden gebruikt door Recovery Services-kluizen, zijn geïsoleerd en kunnen niet worden geopend door gebruikers voor schadelijke doel einden. De toegang is alleen toegestaan via Azure Backup beheer bewerkingen, zoals herstellen. Deze beheer bewerkingen worden beheerd via op rollen gebaseerde Access Control (RBAC).

Zie voor meer informatie Access Control op [basis van rollen gebruiken om Azure backup herstel punten te beheren](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="soft-delete"></a>Voorlopig verwijderen

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Moet ik de functie voor voorlopig verwijderen inschakelen voor elke kluis?

Nee, het is standaard gebouwd en ingeschakeld voor alle Recovery Services-kluizen.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Kan ik het aantal dagen configureren dat mijn gegevens behouden blijven in de modus voor zacht verwijderen nadat de bewerking is voltooid?

Nee, het is vast tot 14 dagen na het verwijderen van de bewerking.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Moet ik de kosten voor deze extra retentie van 14 dagen betalen?

Nee, deze extra Bewaar periode van 14 dagen is gratis als onderdeel van de functie voor zacht verwijderen.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Kan ik een herstel bewerking uitvoeren wanneer mijn gegevens de status zacht verwijderen hebben?

Nee, u moet de tijdelijke verwijderde resource verwijderen om te herstellen. Met de bewerking voor het ongedaan maken van de verwijdering wordt de resource weer **gestopt met de status beveiliging stoppen met gegevens behouden** , waarin u naar een wille keurig moment kunt herstellen. De garbage collector blijft onderbroken in deze status.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Volgen mijn moment opnamen dezelfde levens cyclus als mijn herstel punten in de kluis?

Ja.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Hoe kan ik de geplande back-ups opnieuw activeren voor een tijdelijke verwijderde resource?

Als u de verwijdering opheffen, gevolgd door de bewerking hervatten wordt de resource opnieuw beveiligd. Met de bewerking hervatten wordt een back-upbeleid gekoppeld om de geplande back-ups te activeren met de geselecteerde Bewaar periode. Daarnaast wordt de garbage collector uitgevoerd zodra de bewerking hervatten is voltooid. Als u een herstel bewerking wilt uitvoeren vanaf een herstel punt dat voorbij de verloop datum ligt, wordt u geadviseerd om het te doen voordat u de hervatting hervat.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Kan ik mijn kluis verwijderen als er sprake is van tijdelijke verwijderde items in de kluis?

Recovery Services kluis kan niet worden verwijderd als er back-upitems in de kluis worden verwijderd. De voorlopig verwijderde items worden permanent verwijderd na 14 dagen na de Verwijder bewerking. U kunt de kluis pas verwijderen nadat alle tijdelijke verwijderde items zijn opgeschoond.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Kan ik de gegevens die ouder zijn dan de 14 dagen na het verwijderen verwijderen?

Nee. U kunt het verwijderen van de voorlopig verwijderde items niet afdwingen. deze worden na 14 dagen automatisch verwijderd. Deze beveiligings functie is ingeschakeld om de back-upgegevens te beschermen tegen onbedoelde of schadelijke verwijderingen.  U moet 14 dagen wachten voordat u een andere bewerking op de virtuele machine uitvoert.  Voor tijdelijke verwijderde items worden er geen kosten in rekening gebracht.  Neem contact op met micro soft ondersteuning als u de virtuele machines die binnen 14 dagen zijn gemarkeerd voor zacht verwijderen, opnieuw moet beveiligen.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Kunnen er tijdelijke Verwijder bewerkingen worden uitgevoerd in Power shell of CLI?

Nee, ondersteuning voor Power shell of CLI is momenteel niet beschikbaar.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Wordt zacht verwijderen ondersteund voor andere werk belastingen in de Cloud, zoals SQL Server in azure-Vm's en SAP HANA in azure-Vm's?

Nee. Momenteel wordt een tijdelijke verwijdering alleen ondersteund voor virtuele machines van Azure.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beveiligings controles voor Azure backup](backup-security-controls.md).
