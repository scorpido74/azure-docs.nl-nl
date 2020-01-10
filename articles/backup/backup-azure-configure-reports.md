---
title: Power BI-rapporten configureren
description: Power BI-rapporten configureren voor Azure Backup met behulp van een Recovery Services kluis.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 9b6ef62a924761642ef3217ff8af64ac6847c766
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450108"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-rapporten configureren

In dit artikel worden de stappen beschreven die u moet volgen om rapporten voor Azure Backup te configureren met behulp van een Recovery Services kluis. Ook wordt uitgelegd hoe u met behulp van Power BI toegang krijgt tot rapporten. Nadat u deze stappen hebt voltooid, kunt u rechtstreeks naar Power BI gaan om rapporten weer te geven, aan te passen en te maken.

> [!IMPORTANT]
> vanaf 1 november 2018 ervaren sommige klanten mogelijk problemen bij het laden van gegevens in de Azure Backup-app in Power BI. In de foutmelding staat 'Er zijn extra tekens aangetroffen aan het eind van de JSON-invoer. De uitzondering is opgetreden door de IDataReader-interface.'
Dit probleem treedt op vanwege een wijziging in de indeling waarin gegevens in de opslagaccount worden geladen.
Down load de nieuwste app (versie 1,8) om dit probleem te voor komen.
>
>

## <a name="supported-scenarios"></a>Ondersteunde scenario's

- Azure Backup-rapporten worden ondersteund voor back-ups van virtuele machines van Azure en back-ups van bestanden en mappen naar de Cloud met behulp van de Azure Recovery Services-agent.
- Rapporten voor Azure SQL Database, Azure-bestands shares, Data Protection Manager en Azure Backup-Server worden op dit moment niet ondersteund.
- U kunt rapporten weer geven over kluizen en abonnementen, als hetzelfde opslag account is geconfigureerd voor elk van de kluizen. Het geselecteerde opslag account moet zich in dezelfde regio bevinden als de Recovery Services kluis.
- De frequentie van geplande vernieuwing voor de rapporten is 24 uur in Power BI. U kunt ook een vernieuwing op aanvraag uitvoeren van de rapporten in Power BI. In dit geval worden de meest recente gegevens in het opslag account van de klant gebruikt om rapporten weer te geven.

## <a name="prerequisites"></a>Vereisten

- Maak een [Azure Storage-account](../storage/common/storage-quickstart-create-account.md) om het te configureren voor-rapporten. Dit opslag account wordt gebruikt voor het opslaan van gegevens over rapporten.
- [Maak een Power bi-account](https://powerbi.microsoft.com/landing/signin/) om uw eigen rapporten te bekijken, aan te passen en te maken met behulp van de Power BI portal.
- Registreer de resource provider **micro soft. Insights**als deze nog niet is geregistreerd. Gebruik de abonnementen voor het opslag account en de Recovery Services kluis zodat rapport gegevens naar het opslag account kunnen stromen. Als u deze stap wilt uitvoeren, gaat u naar de Azure Portal, selecteert u **abonnement** > **resource providers**en controleert u of deze provider deze kan registreren.

## <a name="configure-storage-account-for-reports"></a>Opslag account voor rapporten configureren

Volg deze stappen om het opslag account voor een Recovery Services kluis te configureren met behulp van de Azure Portal. Dit is een eenmalige configuratie. Nadat het opslag account is geconfigureerd, kunt u rechtstreeks naar Power BI gaan om de sjabloon-app weer te geven en rapporten te gebruiken.

1. Als er al een Recovery Services kluis is geopend, gaat u naar de volgende stap. Als u geen Recovery Services kluis hebt geopend, selecteert u in de Azure Portal **alle services**.

   - Voer **Recovery Services**in de lijst met resources in.
   - Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Wanneer u **Recovery Services kluizen**ziet, selecteert u deze.
   - De lijst met Recovery Services-kluizen wordt weergegeven. Selecteer in de lijst met Recovery Services-kluizen een kluis.

     Het geselecteerde kluisdashboard wordt geopend.
2. Selecteer in de lijst met items onder de kluis, onder de sectie **bewaking en rapporten** , **back-uprapporten** om het opslag account voor rapporten te configureren.

      ![Back-uprapporten selecteren menu-item stap 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Selecteer op de Blade **back-uprapporten** de koppeling **Diagnostische instellingen** . Met deze koppeling opent u de gebruikers interface voor **Diagnostische instellingen** , die wordt gebruikt om gegevens naar een opslag account van de klant te pushen.

      ![Diagnostische gegevens inschakelen, stap 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Selecteer **Diagnostische gegevens inschakelen** om een gebruikers interface te openen die u wilt gebruiken voor het configureren van een opslag account.

      ![Diagnostische gegevens inschakelen, stap 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Voer in het vak **naam** een naam in voor de instelling. Schakel het selectie vakje **archiveren naar een opslag account** in zodat rapport gegevens in het opslag account kunnen worden geplaatst.

      ![Diagnostische gegevens inschakelen, stap 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Selecteer **opslag account**, selecteer het relevante abonnement en het opslag account in de lijst voor het opslaan van rapport gegevens en selecteer **OK**.

      ![Opslag account selecteren stap 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Schakel onder de sectie **logboek** het selectie vakje **AzureBackupReport** in. Verplaats de schuif regelaar om een Bewaar periode voor deze rapport gegevens te selecteren. Rapportage gegevens in het opslag account worden bewaard voor de periode die is geselecteerd met deze schuif regelaar.

      ![Opslag account opslaan stap 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Controleer alle wijzigingen en selecteer **Opslaan**. Met deze actie zorgt u ervoor dat alle wijzigingen worden opgeslagen en het opslag account nu is geconfigureerd voor het opslaan van rapport gegevens.

9. De tabel **Diagnostische instellingen** bevat nu de nieuwe instelling ingeschakeld voor de kluis. Als dat niet het geval is, vernieuwt u de tabel om de bijgewerkte instelling weer te geven.

      ![Diagnostische instelling weer geven stap 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Nadat u rapporten hebt geconfigureerd door het opslag account op te slaan, *wacht u 24 uur totdat* de eerste gegevens push is voltooid. Importeer de Azure Backup-app in Power BI pas na dat tijdstip. Zie de [sectie Veelgestelde vragen](backup-azure-monitor-alert-faq.md)voor meer informatie.
>
>

## <a name="view-reports-in-power-bi"></a>Rapporten weer geven in Power BI

Nadat u een opslag account voor rapporten hebt geconfigureerd met behulp van een Recovery Services kluis, duurt het ongeveer 24 uur voordat rapport gegevens worden getransporteerd. Na 24 uur na het instellen van een opslag account volgt u deze stappen om rapporten in Power BI weer te geven.
Als u het rapport wilt aanpassen en delen, maakt u een werk ruimte en voert u de volgende stappen uit

1. [Meld](https://powerbi.microsoft.com/landing/signin/) u aan bij Power bi.
2. Navigeer naar **apps > meer apps van micro soft Appsource ophalen**. Volg de stappen in de [Power bi-documentatie om verbinding te maken met een service](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

3. Voer **Azure backup** in op de **Zoek** balk en selecteer **nu ophalen**.

      ![Sjabloon-app ophalen](./media/backup-azure-configure-reports/template-app-get.png)
4. Voer de naam in van het opslag account dat in de vorige stap 5 is geconfigureerd en selecteer **volgende**.

    ![Naam van opslagaccount invoeren](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)
5. Met behulp van de verificatie methode Key voert u de sleutel voor het opslag account voor dit opslag account in. U vindt de toegangs sleutels voor uw opslag account in de Azure Portal. Zie [toegangs sleutels voor opslag accounts beheren](../storage/common/storage-account-keys-manage.md)voor meer informatie.

     ![Opslag account invoeren](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>

6. Selecteer **Aanmelden**. Nadat het aanmelden is voltooid, ziet u een melding over het importeren van gegevens.

    ![Inhouds pakket importeren](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>

    Nadat het importeren is voltooid, ziet u een **geslaagde** melding. Als de hoeveelheid gegevens in het opslag account groot is, kan het wat langer duren om de sjabloon-app te importeren.

    ![Inhouds pakket voor geslaagde import bewerking](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. Nadat de gegevens zijn geïmporteerd, is de app **Azure backup** sjabloon zichtbaar in **apps** in het navigatie deel venster. In de lijst met **Dash boards**, **rapporten**en **gegevens sets**wordt nu Azure backup weer gegeven.

8. Selecteer in **Dash boards**de optie **Azure backup**, die een set met vastgemaakte sleutel rapporten bevat.

      ![Azure Backup dash board](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Als u de volledige set rapporten wilt weer geven, selecteert u een rapport in het dash board.

      ![Status van Azure Backup-taak](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Selecteer elk tabblad in de rapporten om rapporten in dat gebied weer te geven.

      ![Tabbladen Azure Backup rapporten](./media/backup-azure-configure-reports/reports-tab-view.png)

## <a name="troubleshooting-errors"></a>Fouten oplossen

| Foutdetails | Oplossing |
| --- | --- |
| Nadat u het opslag account voor back-uprapporten hebt ingesteld, wordt in het **opslag account** nog steeds **niet geconfigureerd**weer gegeven. | Als u een opslag account hebt geconfigureerd, stromen uw rapport gegevens ondanks dit probleem. Als u dit probleem wilt oplossen, gaat u naar de Azure Portal en selecteert u **alle services** > **diagnostische instellingen** > **Recovery Services kluis** > **instelling bewerken**. Verwijder de eerder geconfigureerde instelling en maak een nieuwe instelling op dezelfde Blade. In dit tijdstip selecteert u in het vak **naam** de optie **service**. Nu wordt het geconfigureerde opslag account weer gegeven. |
|Nadat u de Azure Backup sjabloon-app in Power BI hebt geïmporteerd, wordt het fout bericht ' 404-container is niet gevonden ' weer gegeven. | Zoals eerder vermeld, moet u 24 uur wachten nadat u rapporten hebt geconfigureerd in de Recovery Services kluis om ze correct te bekijken in Power BI. Als u de rapporten vóór 24 uur probeert te openen, wordt dit fout bericht weer gegeven omdat de voltooide gegevens nog niet aanwezig zijn om geldige rapporten weer te geven. |

## <a name="next-steps"></a>Volgende stappen

Nadat u het opslag account hebt geconfigureerd en de app Azure Backup sjabloon hebt geïmporteerd, moeten de volgende stappen worden uitgevoerd om rapporten aan te passen en een rapport gegevens model te gebruiken voor het maken van rapporten. Zie de volgende artikelen voor meer informatie.

- [Een Azure Backup rapport gegevens model gebruiken](backup-azure-reports-data-model.md)
- [Rapporten filteren in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
- [Rapporten maken in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
