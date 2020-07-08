---
title: Back-ups maken van Azure-bestands shares in de Azure Portal
description: Meer informatie over het gebruik van de Azure Portal voor het maken van back-ups van Azure-bestands shares in de Recovery Services kluis
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 76bf8e00dede5f227cb862f9c9474844e349e298
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391147"
---
# <a name="back-up-azure-file-shares"></a>Een back-up maken van Azure-bestandsshares

In dit artikel wordt uitgelegd hoe u de Azure Portal gebruikt voor het maken van back-ups van [Azure-bestands shares](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

In dit artikel leert u het volgende:

* Maak een Recovery Services-kluis.
* Ontdek bestands shares en Configureer back-ups.
* Voer een back-uptaak op aanvraag uit om een herstel punt te maken.

## <a name="prerequisites"></a>Vereisten

* Identificeer of maak een [Recovery Services kluis](#create-a-recovery-services-vault) in dezelfde regio als het opslag account dat als host fungeert voor de bestands share.
* Zorg ervoor dat de bestands share aanwezig is in een van de [ondersteunde typen opslag accounts](azure-file-share-support-matrix.md).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-file-shares-and-configure-backup"></a>Bestands shares detecteren en back-up configureren

1. Open in de [Azure Portal](https://portal.azure.com/)de Recovery Services kluis die u wilt gebruiken voor het configureren van de back-up voor de bestands share.

1. Selecteer in het deel venster **Recovery Services kluis** de **plus back-up** in het menu bovenaan.

   ![Recovery Services-kluis](./media/backup-afs/recovery-services-vault.png)

    1. In het deel venster doel van de **back-up** stelt u in **waar wordt uw workload uitgevoerd?** naar **Azure** door de optie **Azure** te selecteren in de vervolg keuzelijst.

          ![Kies Azure als workload](./media/backup-afs/backup-goal.png)

    2. Selecteer in **waarvan wilt u een back-up maken?** de optie **Azure-bestands share** in de vervolg keuzelijst.

          ![Azure-bestands share selecteren](./media/backup-afs/select-azure-file-share.png)

    3. Selecteer **back-up** om de extensie van de Azure-bestands share in de kluis te registreren.

          ![Selecteer back-up om de Azure-bestands share te koppelen aan de kluis](./media/backup-afs/register-extension.png)

1. Nadat u **back-up**hebt geselecteerd, wordt het deel venster **back-up** geopend. Als u het opslag account wilt selecteren dat als host fungeert voor de bestands share die u wilt beveiligen, klikt u op het tekstvak koppelings tekst onder het veld voor het **opslag account** **selecteren** .

   ![Kies de koppeling selecteren](./media/backup-afs/choose-select-link.png)

1. Het **deel venster opslag account selecteren** wordt aan de rechter kant weer gegeven, met een reeks gedetecteerde ondersteunde opslag accounts. Ze zijn gekoppeld aan deze kluis of bevinden zich in dezelfde regio als de kluis, maar nog niet gekoppeld aan een Recovery Services kluis.

1. Selecteer een account in de lijst met gedetecteerde opslag accounts en selecteer **OK**.

   ![Selecteer een van de gedetecteerde opslag accounts](./media/backup-afs/select-discovered-storage-account.png)

1. De volgende stap is het selecteren van de bestands shares waarvan u een back-up wilt maken. Klik op de knop **toevoegen** in de sectie **Bestands shares naar back-up** .

   ![Selecteer de bestands shares waarvan u een back-up wilt maken](./media/backup-afs/select-file-shares-to-back-up.png)

1. Het deel venster **Bestands shares context selecteren** wordt aan de rechter kant geopend. Azure zoekt naar het opslag account voor bestands shares waarvan een back-up kan worden gemaakt. Als u onlangs uw bestands shares hebt toegevoegd en deze niet in de lijst ziet, moet u enige tijd toestaan dat de bestands shares worden weer gegeven.

1. Selecteer in de lijst **Bestands shares selecteren** een of meer van de bestands shares waarvan u een back-up wilt maken. Selecteer **OK**.

   ![Selecteer de bestands shares](./media/backup-afs/select-file-shares.png)

1. Als u een back-upbeleid voor de bestands share wilt kiezen, hebt u drie opties:

   * Kies het standaard beleid.<br>
   Met deze optie kunt u dagelijks een back-up inschakelen die 30 dagen wordt bewaard. Als u geen bestaand back-upbeleid in de kluis hebt, wordt het deel venster back-up geopend met de standaard beleids instellingen. Als u de standaard instellingen wilt kiezen, kunt u rechtstreeks op **back-up inschakelen**klikken.

   * Een nieuw beleid maken <br>

      1. Als u een nieuw back-upbeleid voor de bestands share wilt maken, klikt u op de koppelings tekst onder de vervolg keuzelijst in het gedeelte **back-upbeleid** .<br>

         ![Nieuw beleid maken](./media/backup-afs/create-new-policy.png)

      1. Het context venster **back-upbeleid** wordt aan de rechter kant geopend. Geef in het tekstvak een beleids naam op en kies de Bewaar periode volgens uw vereiste. Alleen de optie dagelijkse Bewaar periode is standaard ingeschakeld. Als u wekelijks, maandelijks of jaarlijks wilt bewaren, selecteert u het betreffende selectie vakje en geeft u de gewenste Bewaar waarde op.

      1. Klik op OK nadat u de Bewaar waarden en een geldige beleids naam hebt opgegeven.<br>

         ![Beleids naam en bewaar waarden opgeven](./media/backup-afs/policy-name.png)

   * Kies een van de bestaande back-beleidsregels <br>

   Als u een van de bestaande back-beleidsregels voor het configureren van beveiliging wilt kiezen, selecteert u het gewenste beleid in de vervolg keuzelijst **back-upbeleid** .<br>

   ![Bestaand beleid kiezen](./media/backup-afs/choose-existing-policy.png)

1. Klik op **back-up inschakelen** om te beginnen met het beveiligen van de bestands share.

   ![Kies back-up inschakelen](./media/backup-afs/enable-backup.png)

Nadat u een back-upbeleid hebt ingesteld, wordt er op het geplande tijdstip een moment opname van de bestands shares gemaakt. Het herstel punt wordt ook voor de gekozen periode bewaard.

>[!NOTE]
>Azure Backup biedt nu ondersteuning voor beleids regels met een dagelijks/wekelijks/maandelijks/jaarlijks bewaren van back-ups van Azure-bestands share.

## <a name="create-an-on-demand-backup"></a>Een back-up op aanvraag maken

In sommige gevallen wilt u mogelijk een back-upmomentopname of een herstel punt genereren, buiten het tijdstip dat is gepland in het back-upbeleid. Een veelvoorkomende reden voor het genereren van een back-up op aanvraag is direct nadat u het back-upbeleid hebt geconfigureerd. Op basis van de planning in het back-upbeleid, kan het uren of dagen duren totdat er een moment opname wordt gemaakt. Als u uw gegevens wilt beschermen totdat het back-upbeleid actief wordt, start u een back-up op aanvraag. Het maken van een back-up op aanvraag is vaak vereist voordat u geplande wijzigingen aanbrengt aan uw bestands shares.

### <a name="create-a-backup-job-on-demand"></a>Een back-uptaak op aanvraag maken

1. Open de Recovery Services kluis die u hebt gebruikt voor het maken van een back-up van uw bestands share. Selecteer in het deel venster **overzicht** de optie **Back-upitems** onder de sectie **beveiligde items** .

   ![Back-upitems selecteren](./media/backup-afs/backup-items.png)

1. Nadat u **Back-upitems**hebt geselecteerd, wordt er een nieuw deel venster met alle **typen back-upbeheer** weer gegeven naast het deel venster **overzicht** .

   ![Lijst met typen back-upbeheer](./media/backup-afs/backup-management-types.png)

1. Selecteer **Azure Storage (Azure files)** in de lijst **type back-upbeheer** . U ziet een lijst met alle bestands shares en de bijbehorende opslag accounts waarvan een back-up is gemaakt met behulp van deze kluis.

   ![Back-upitems van Azure Storage (Azure Files)](./media/backup-afs/azure-files-backup-items.png)

1. Selecteer de gewenste bestands share uit de lijst met Azure-bestands shares. De details van het **back-upitem** worden weer gegeven. Selecteer in het menu **back-upitem** **Nu back-up maken**. Omdat deze back-uptaak op aanvraag is, is er geen Bewaar beleid gekoppeld aan het herstel punt.

   ![Selecteer nu back-up](./media/backup-afs/backup-now.png)

1. Het deel venster **Nu back-up** wordt geopend. Geef de laatste dag op die u van het herstelpunt wilt bewaren. U kunt een maximale Bewaar periode van tien jaar voor een back-up op aanvraag hebben.

   ![Bewaar datum kiezen](./media/backup-afs/retention-date.png)

1. Selecteer **OK** om te bevestigen dat de back-uptaak op aanvraag wordt uitgevoerd.

1. Controleer de portal meldingen om het volt ooien van de back-uptaak uit te voeren. U kunt de voortgang van de taak in het kluis dashboard bewaken. Selecteer de **back-uptaken**die worden  >  **uitgevoerd**.

>[!NOTE]
>Azure Backup vergrendelt het opslag account wanneer u de beveiliging voor een bestands share configureert in het bijbehorende account. Dit biedt beveiliging tegen onbedoeld verwijderen van een opslag account met back-ups van bestands shares.

## <a name="best-practices"></a>Aanbevolen procedures

* Verwijder geen moment opnamen die zijn gemaakt door Azure Backup. Het verwijderen van momentopnamen kan leiden tot het verlies van herstelpunten en/of herstelfouten.

* Verwijder de vergren deling van het opslag account door Azure Backup niet. Als u de vergren deling verwijdert, is uw opslag account gevoelig voor onbedoeld verwijderen en als dit wordt verwijderd, gaan uw moment opnamen of back-ups verloren.

## <a name="next-steps"></a>Volgende stappen

Leer hoe u het volgende doet:

* [Azure-bestands shares herstellen](restore-afs.md)
* [Back-ups van Azure-bestands shares beheren](manage-afs-backup.md)
