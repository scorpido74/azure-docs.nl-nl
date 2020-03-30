---
title: Gegevens herstellen vanaf een Azure Backup Server
description: Herstel de gegevens die u hebt beveiligd met een vault van Recovery Services van elke Azure Backup Server die is geregistreerd in die kluis.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 2a89697899fc244848854978de4b25e79ef6f184
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173501"
---
# <a name="recover-data-from-azure-backup-server"></a>Gegevens herstellen vanaf Azure Backup Server

U Azure Backup Server gebruiken om de gegevens waarop u een back-up hebt gemaakt, te herstellen naar een vault van Recovery Services. Het proces hiervoor is geïntegreerd in de Azure Backup Server-beheerconsole en is vergelijkbaar met de herstelwerkstroom voor andere Azure Backup-componenten.

> [!NOTE]
> Dit artikel is van toepassing op [System Center Data Protection Manager 2012 R2 met UR7 of hoger,](https://support.microsoft.com/kb/3065246)gecombineerd met de nieuwste Azure [Backup-agent](https://aka.ms/azurebackup_agent).
>
>

Ga als een overzicht van gegevens uit een Azure Backup Server:

1. Klik op het tabblad **Herstel** van de beheerconsole azure backup server op **'Externe DPM toevoegen'** (linksboven in het scherm).

    ![Externe DPM toevoegen](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Download nieuwe **vaultcredentials** uit de kluis die is gekoppeld aan de **Azure Backup Server** waar de gegevens worden hersteld, kies de Azure Backup Server uit de lijst met Azure Backup Servers die zijn geregistreerd bij de kluis Recovery Services en geef de **coderingswachtwoorddie** is gekoppeld aan de server waarvan de gegevens worden hersteld.

    ![Externe DPM-referenties](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Alleen Azure Backup Servers die aan dezelfde registratiekluis zijn gekoppeld, kunnen elkaars gegevens herstellen.
   >
   >

    Zodra de externe Azure Backup Server is toegevoegd, u de gegevens van de externe server en de lokale Azure Backup Server bekijken op het tabblad **Herstel.**
3. Blader door de beschikbare lijst met productieservers die zijn beveiligd door de externe Azure Backup Server en selecteer de juiste gegevensbron.

    ![Bladeren door externe DPM-server](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Selecteer **de maand en het jaar** in de vervolgkeuzelijst **Herstelpunten,** selecteer de vereiste **hersteldatum** voor het moment waarop het herstelpunt is gemaakt en selecteer de **hersteltijd**.

    Er wordt een lijst met bestanden en mappen weergegeven in het onderste deelvenster, die op elke locatie kan worden doorzocht en hersteld.

    ![Externe Herstelpunten voor DPM-server](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Klik met de rechtermuisknop op het juiste item en klik op **Herstellen**.

    ![Extern DPM-herstel](./media/backup-azure-alternate-dpm-server/recover.png)
6. Bekijk de **selectie voor herstellen**. Controleer of de gegevens en het tijdstip van het herstel van de back-up kopie en de bron van waaruit de back-upkopie is gemaakt. Als de selectie onjuist is, klikt u op **Annuleren** om terug te gaan naar het tabblad Herstel om het juiste herstelpunt te selecteren. Als de selectie juist is, klikt u op **Volgende**.

    ![Extern DPM-hersteloverzicht](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Selecteer **Herstellen naar een alternatieve locatie**. **Blader** naar de juiste locatie voor het herstel.

    ![Alternatieve externe dpm-herstellocatie](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Kies de optie met betrekking tot **het maken van kopiëren,** **Overslaan**of **Overschrijven**.

   * **Maak een kopie** - maakt een kopie van het bestand als er een naambotsing is.
   * **Overslaan** - als er een naam botsing, niet herstellen van het bestand, die het oorspronkelijke bestand verlaat.
   * **Overschrijven** - als er een naam botsing, overschrijft de bestaande kopie van het bestand.

     Kies de juiste optie om de beveiliging te **herstellen.** U de beveiligingsinstellingen toepassen van de doelcomputer waar de gegevens worden hersteld of de beveiligingsinstellingen die van toepassing waren op het product op het moment dat het herstelpunt werd gemaakt.

     Bepaal of een **melding** is verzonden, zodra het herstel is voltooid.

     ![Externe dpm-herstelmeldingen](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. Het **overzichtsscherm** geeft een overzicht van de opties die tot nu toe zijn gekozen. Zodra u op **'Herstellen'** klikt, worden de gegevens hersteld naar de juiste on-premises locatie.

    ![Overzicht van externe DPM-herstelopties](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > De hersteltaak kan worden gecontroleerd op het tabblad **Controle** van de Azure Backup Server.
   >
   >

    ![Monitoring Herstel](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. U op **Extern DPM wissen** klikken op het tabblad **Herstel** van de DPM-server om de weergave van de externe DPM-server te verwijderen.

    ![Externe DPM wissen](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Foutberichten oplossen

| Nee. | Foutbericht | Stappen voor probleemoplossing |
|:---:|:--- |:--- |
| 1. |Deze server is niet geregistreerd bij de kluis die is opgegeven door de vault-referentie. |**Oorzaak:** Deze fout wordt weergegeven wanneer het geselecteerde kluisreferentiebestand niet behoort tot de kluis Recovery Services die is gekoppeld aan Azure Backup Server waarop het herstel wordt geprobeerd. <br> **Oplossing:** Download het kluisreferentiebestand uit de kluis Recovery Services waarop de Azure Backup Server is geregistreerd. |
| 2. |De herstelbare gegevens zijn niet beschikbaar of de geselecteerde server is geen DPM-server. |**Oorzaak:** Er zijn geen andere Azure Backup Servers geregistreerd bij de vault Recovery Services of de servers hebben de metagegevens nog niet geüpload of de geselecteerde server is geen Azure Backup Server (met Windows Server of Windows Client). <br> **Oplossing:** Als er andere Azure Backup Servers zijn geregistreerd bij de kluis Recovery Services, moet u ervoor zorgen dat de nieuwste Azure Backup-agent is geïnstalleerd. <br>Als er andere Azure Backup Servers zijn geregistreerd bij de kluis Recovery Services, wacht u een dag na de installatie om het herstelproces te starten. De nachtelijke taak uploadt de metagegevens voor alle beveiligde back-ups naar de cloud. De gegevens zullen beschikbaar zijn voor herstel. |
| 3. |Er is geen andere DPM-server geregistreerd op deze kluis. |**Oorzaak:** Er zijn geen andere Azure Backup Servers die zijn geregistreerd bij de kluis waarvan het herstel wordt geprobeerd.<br>**Oplossing:** Als er andere Azure Backup Servers zijn geregistreerd bij de kluis Recovery Services, moet u ervoor zorgen dat de nieuwste Azure Backup-agent is geïnstalleerd.<br>Als er andere Azure Backup Servers zijn geregistreerd bij de kluis Recovery Services, wacht u een dag na de installatie om het herstelproces te starten. De nachtelijke taak uploadt de metagegevens voor alle beveiligde back-ups naar de cloud. De gegevens zullen beschikbaar zijn voor herstel. |
| 4. |De meegeleverde coderingswachtwoordzin komt niet overeen met de wachtwoordzin die aan de volgende server is gekoppeld: ** \<servernaam>** |**Oorzaak:** De versleutelingswachtwoordzin die wordt gebruikt bij het versleutelen van de gegevens van de gegevens van de Azure Backup Server die wordt hersteld, komt niet overeen met de meegeleverde coderingswachtwoordzin. De agent kan de gegevens niet ontsleutelen. Vandaar dat het herstel mislukt.<br>**Oplossing:** Geef exact dezelfde coderingswachtwoordzin op die is gekoppeld aan de Azure Backup Server waarvan de gegevens worden hersteld. |

## <a name="next-steps"></a>Volgende stappen

Lees de andere veelgestelde vragen:

* [Veelgestelde vragen](backup-azure-vm-backup-faq.md) over Azure VM-back-ups
* [Veelgestelde vragen](backup-azure-file-folder-backup-faq.md) over de Azure Backup-agent
