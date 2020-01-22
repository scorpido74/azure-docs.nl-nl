---
title: Back-ups van Azure-bestandsshares beheren
description: In dit artikel worden algemene taken beschreven voor het beheren en bewaken van de Azure-bestands shares waarvan een back-up wordt gemaakt door de Azure Backup-service.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: a5477d021b6e3600693e183d8707e11592b7cc38
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294550"
---
# <a name="manage-azure-file-share-backups"></a>Back-ups van Azure-bestandsshares beheren

In dit artikel worden algemene taken beschreven voor het beheren en bewaken van de Azure-bestands shares waarvan een back-up wordt gemaakt door de [Azure backup](https://docs.microsoft.com/azure/backup/backup-overview) -service. U leert hoe u de volgende beheer taken uitvoert in de Recovery Services kluis:

* [Taken bewaken](#monitor-jobs)
* [Een nieuw beleid maken](#create-a-new-policy)
* [Beleid wijzigen](#modify-policy)
* [De beveiliging voor een bestandsshare stoppen](#stop-protection-on-a-file-share)
* [De beveiliging voor een bestandsshare hervatten](#resume-protection-on-a-file-share)
* [Back-upgegevens verwijderen](#delete-backup-data)
* [Registratie van opslag account opheffen](#unregister-storage-account)

## <a name="monitor-jobs"></a>Taken controleren

Wanneer u een back-up-of herstel bewerking uitvoert, maakt de back-upservice een taak voor bijhouden. U kunt de voortgang van alle taken bijhouden op de pagina **Back-uptaken**.

De pagina **Back-uptaken** openen:

1. Open de Recovery Services kluis die u hebt gebruikt voor het configureren van back-ups voor uw bestands shares. Klik op de Blade **overzicht** op **back-uptaken** onder het gedeelte **bewaking** .

   ![Bewerkings sectie back-uptaken](./media/manage-afs-backup/backup-jobs.png)

2. Nadat u op OK hebt geklikt, wordt de Blade **back-uptaken** weer gegeven met de status van alle taken. U kunt klikken op de naam van de werk belasting die overeenkomt met de bestands share die u wilt bewaken.

   ![Werkbelasting naam](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Een nieuw beleid maken

U kunt een nieuw beleid maken voor back-ups van Azure-bestands shares in de sectie **back-upbeleid** van de Recovery Services kluis. Alle beleids regels die zijn gemaakt bij het configureren van back-ups voor bestands shares, worden weer gegeven met het beleids type van de Azure-bestands share.

Het bestaande back-upbeleid weergeven:

1. Open de Recovery Services kluis die u hebt gebruikt voor het configureren van de back-up voor de bestands share en klik in het menu Recovery Services kluis op **back-upbeleid** onder sectie beheren. Alle back-upbeleiden die in de kluis zijn geconfigureerd, worden weer gegeven.

   ![Alle back-upbeleid](./media/manage-afs-backup/all-backup-policies.png)

2. Selecteer de optie **Azure-bestands share** in de vervolg keuzelijst rechtsboven om de beleids regels weer te geven die specifiek zijn voor de Azure-bestands share.

   ![Azure-bestands share kiezen](./media/manage-afs-backup/azure-file-share.png)

Een nieuw back-upbeleid maken:

1. Klik op de Blade back-upbeleid op **+ toevoegen** .

   ![Nieuw back-upbeleid](./media/manage-afs-backup/new-backup-policy.png)

2. Selecteer **Azure-bestands share** als het **beleids type** op de Blade **toevoegen** . De Blade back-upbeleid voor Azure-bestands share wordt geopend. Geef de naam van het beleid, de back-upfrequentie en het Bewaar termijn voor de herstel punten op. Klik op **OK** wanneer u het beleid hebt gedefinieerd.

   ![Het back-upbeleid definiëren](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Beleid wijzigen

U kunt een back-upbeleid wijzigen om de back-upfrequentie of het Bewaar bereik te wijzigen.

Een beleid wijzigen:

1. Open de Recovery Services kluis die u hebt gebruikt voor het configureren van de back-up voor de bestands share en klik in het menu Recovery Services kluis op **back-upbeleid** in de sectie beheren. Alle back-upbeleiden die in de kluis zijn geconfigureerd, worden weer gegeven.

   ![Alle back-upbeleid in de kluis](./media/manage-afs-backup/all-backup-policies-modify.png)

2. Als u beleids regels wilt bekijken die specifiek zijn voor een Azure-bestands share, selecteert u **Azure-bestands share** in de vervolg keuzelijst rechtsboven. Klik op het back-upbeleid dat u wilt wijzigen.

   ![De Azure-bestands share die u wilt aanpassen](./media/manage-afs-backup/azure-file-share-modify.png)

3. De Blade **planning** wordt geopend. Bewerk het back-upschema/Bewaar bereik zoals vereist en klik op **Opslaan**. Het bericht ' update wordt uitgevoerd ' wordt weer gegeven op de Blade en wanneer de wijzigingen van het beleid zijn bijgewerkt, wordt het bericht ' het back-upbeleid is bijgewerkt ' weer gegeven.

   ![Het gewijzigde beleid opslaan](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>De beveiliging voor een bestandsshare stoppen

Er zijn twee manieren om het beveiligen van Azure-bestandsshares te stoppen:

* Alle toekomstige back-uptaken stoppen en *alle herstel punten verwijderen*
* Alle toekomstige back-uptaken stoppen, maar *de herstel punten behouden*

Er zijn mogelijk kosten verbonden aan het verlaten van de herstel punten in de opslag, omdat de onderliggende moment opnamen die zijn gemaakt door Azure Backup, behouden blijven. Het voordeel van het bewaren van de herstelpunten is dat u de bestandsshare later kunt herstellen indien gewenst. Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/backup/)voor meer informatie over de kosten voor het verlaten van de herstel punten. Als u alle herstelpunten verwijdert, kunt u de bestandsshare niet terugzetten.

Ga als volgt te werk om de beveiliging van een Azure-bestandsshare te stoppen:

1. Open de Recovery Services kluis die de bestands share herstel punten bevat en klik op **Back-upitems** onder beveiligde items. De lijst met typen back-upitems wordt weergegeven.

   ![Back-upitems](./media/manage-afs-backup/backup-items.png)

2. In de lijst **Type back-upbeheer** selecteert u **Azure Storage (Azure-bestanden)** . De lijst met **Back-upitems voor (Azure Storage (Azure files))** wordt weer gegeven.

   ![Azure Storage selecteren (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. Selecteer in de lijst met **Back-upitems (Azure Storage (Azure files))** het back-upitem waarvoor u de beveiliging wilt stoppen.

4. Selecteer de optie **back-up stoppen** in het menu van de Blade **back-upitem** .

   ![Back-up stoppen selecteren](./media/manage-afs-backup/stop-backup.png)

5. Kies op de Blade **back-up stoppen** de optie **back-upgegevens behouden** of **back-upgegevens verwijderen** en klik op **back-up stoppen**.

    ![Kies back-upgegevens behouden of verwijderen](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>De beveiliging voor een bestandsshare hervatten

Als de optie **back-upgegevens behouden** is gekozen toen de beveiliging van de bestands share werd gestopt, is het mogelijk de beveiliging te hervatten. Als u de optie **Back-upgegevens verwijderen** hebt gekozen, kan de beveiliging voor de bestandsshare niet worden hervat.

De beveiliging van de Azure-bestands share hervatten:

1. Open de Recovery Services kluis die de bestands share herstel punten bevat en klik op **Back-upitems** onder beveiligde items. De lijst met typen back-upitems wordt weergegeven.

   ![Back-upitems voor hervatten](./media/manage-afs-backup/backup-items-resume.png)

2. In de lijst **Type back-upbeheer** selecteert u **Azure Storage (Azure-bestanden)** . De lijst met **Back-upitems voor (Azure Storage (Azure files))** wordt weer gegeven.

   ![Lijst met Azure Storage (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. Selecteer in de lijst met **Back-upitems (Azure Storage (Azure files))** het back-upitem waarvoor u de beveiliging wilt hervatten.

4. Selecteer de optie **back-up hervatten** in het menu van het item van de Blade **back-up** .

   ![Back-up hervatten selecteren](./media/manage-afs-backup/resume-backup.png)

5. De Blade **back-upbeleid** wordt geopend en u kunt een beleid kiezen dat u wilt gebruiken om de back-up te hervatten.

6. Klik op **Opslaan** nadat u het gewenste **back-upbeleid**hebt geselecteerd. Het bericht ' update wordt uitgevoerd ' wordt weer gegeven in de portal en zodra de back-up is hervat, wordt het bericht ' het back-upbeleid voor de beveiligde Azure-bestands share is bijgewerkt ' weer gegeven.

   ![Het back-upbeleid is bijgewerkt](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Back-upgegevens verwijderen

U kunt de back-up van een bestands share tijdens het stoppen van de **back-** uptaak verwijderen of op elk gewenst moment nadat u de beveiliging hebt gestopt. Het kan zelfs handig zijn om een aantal dagen of weken te wachten voordat u de herstelpunten verwijdert. Wanneer u back-upgegevens verwijdert, kunt u geen specifieke herstel punten kiezen die u wilt verwijderen. Als u ervoor kiest om uw back-upgegevens te verwijderen, verwijdert u alle herstel punten die zijn gekoppeld aan de bestands share.

In de volgende procedure wordt ervan uitgegaan dat de beveiliging is gestopt voor de bestands share.

Back-upgegevens voor Azure-bestands share verwijderen:

1. Zodra de back-uptaak is gestopt, zijn de opties **back-up** en **back-upgegevens verwijderen** beschikbaar in het dash board **back-upitem** . Klik op de optie **back-upgegevens verwijderen** in het menu van de Blade **back-upitem** .

   ![Back-upgegevens verwijderen](./media/manage-afs-backup/delete-backup-data.png)

2. De Blade **back-upgegevens verwijderen** wordt geopend. Typ de naam van de bestands share om de verwijdering te bevestigen. Geef eventueel een **reden** op voor het verwijderen of een **Opmerking**. Klik op **verwijderen** als u zeker weet dat u de back-upgegevens wilt verwijderen.

   ![Verwijderen van gegevens bevestigen](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-storage-account"></a>Registratie van opslag account opheffen

Als u uw bestands shares in een bepaald opslag account wilt beveiligen met behulp van een andere Recovery Services-kluis, moet u eerst de [beveiliging stoppen voor alle bestands shares](#stop-protection-on-a-file-share) in dat opslag account. Hef de registratie van het account vervolgens op bij de huidige Recovery Services-kluis die wordt gebruikt voor de beveiliging.

In de volgende procedure wordt ervan uitgegaan dat de beveiliging is gestopt voor alle bestands shares in het opslag account dat u wilt verwijderen.

De registratie van het opslag account ongedaan maken:

1. Open de Recovery Services-kluis waar uw opslag account is geregistreerd.
2. Klik op de optie **back-upinfrastructuur** in het gedeelte **beheren** van de Blade **overzicht** .

   ![Klik op back-upinfrastructuur](./media/manage-afs-backup/backup-infrastructure.png)

3. De Blade **back-upinfrastructuur** wordt geopend. Klik op **opslag accounts** in de sectie **Azure Storage accounts** op deze Blade.

   ![Klik op opslag accounts](./media/manage-afs-backup/storage-accounts.png)

4. Zodra u op **opslag accounts**klikt, wordt er een lijst weer gegeven met opslag accounts die zijn geregistreerd bij de kluis.
5. Klik met de rechter muisknop op het opslag account dat u wilt verwijderen en kies **registratie ongedaan maken**.

   ![Selecteer registratie ongedaan maken](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met back-up/herstel voor Azure-bestands shares oplossen](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files) voor meer informatie
