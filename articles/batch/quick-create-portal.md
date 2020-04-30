---
title: Azure-snelstartgids - Batch-taak uitvoeren - Portal
description: Meer informatie over het gebruik van de Azure Portal voor het maken van een batch-account, een pool van reken knooppunten en een taak waarmee basis taken worden uitgevoerd op de groep.
ms.topic: quickstart
ms.date: 07/03/2018
ms.custom: mvc
ms.openlocfilehash: 4857b9ffb38cf48678f793284afaaf3bbefb7e42
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82114136"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Snelstartgids: Uw eerste Batch-taak uitvoeren in Azure Portal

In deze snelstartgids leert u hoe u Azure Portal gebruikt voor het maken van een Batch-account, een *pool* met computerknooppunten (virtuele machines), en een *Batch-taak* waarmee *basistaken* worden uitgevoerd in de groep. Nadat u deze snelstartgids hebt voltooid, begrijpt u de belangrijkste principes van de Batch-service en bent u er klaar voor om Batch op grotere schaal te gebruiken voor meer realistische workloads.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure 

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-batch-account"></a>Batch-account maken

Volg deze stappen om een Batch-voorbeeldaccount te maken voor testdoeleinden. U hebt een Batch-account nodig om pools en taken te maken. Zoals hier wordt getoond, kunt u een Azure-opslagaccount aan het Batch-account koppelen. Hoewel dit niet is vereist voor deze snelstartgids, is een opslagaccount handig voor het implementeren van toepassingen, en voor het opslaan van invoer- en uitvoergegevens voor de meeste workloads uit de praktijk.


1. Selecteer een**batch service**voor het > **berekenen** > van **een resource maken**. 

   ![Batch in de Marketplace][marketplace_portal]

2. Voer waarden in bij **Accountnaam** en **Resourcegroep**. De accountnaam moet uniek zijn voor de geselecteerde Azure-**locatie**. Gebruik alleen kleine letters of cijfers, en kies een naam van minimaal 3 en maximaal 24 tekens. 

3. Selecteer in **Opslagaccount** een bestaand opslagaccount of maak een nieuw opslagaccount.

4. Laat de overige instellingen op de standaardwaarden staan, en selecteer **Maken** om het account te maken.

   ![Batch-account maken][account_portal]  

Wanneer het bericht **Implementatie is voltooid** wordt weergegeven, gaat u naar het Batch-account in de portal.

## <a name="create-a-pool-of-compute-nodes"></a>Een pool met rekenknooppunten maken

Nu u beschikt over een Batch-account, maakt u een voorbeeldpool met Windows-rekenknooppunten voor testdoeleinden. De pool in dit snelle voorbeeld bestaat uit 2 knooppunten waarop een Windows Server 2012 R2-installatiekopie wordt uitgevoerd vanuit Azure Marketplace.


1. Selecteer in het batch-account de optie **Pools** > **toevoegen**.

2. Voer een **Pool-id** in met de naam *mypool*. 

3. Selecteer bij **Besturingssysteem** de volgende instellingen (u kunt andere opties verkennen).
  
   |Instelling  |Waarde  |
   |---------|---------|
   |**Type installatiekopie**|Marketplace (Linux/Windows)|
   |**Uitgever**     |MicrosoftWindowsServer|
   |**Aanbieding**     |WindowsServer|
   |**SKU**     |2012-R2-Datacenter-smalldisk|

   ![Selecteer een besturingssysteem voor de pool][pool_os] 

4. Schuif naar beneden om de **Knooppuntgrootte** en instellingen voor **Schalen** in te voeren. De voorgestelde knooppuntgrootte in dit snelle voorbeeld biedt een goede balans tussen prestaties en kosten.
  
   |Instelling  |Waarde  |
   |---------|---------|
   |**Prijscategorie voor het knooppunt**     |Standard_A1|
   |**Aan het doel toegewezen knooppunten**     |2|

   ![Een poolgrootte selecteren][pool_size] 

5. Laat de overige instellingen op de standaardwaarden staan en selecteer **OK** om de pool te maken.

De pool wordt onmiddellijk gemaakt in Batch, maar het duurt enkele minuten voordat de rekenknooppunten zijn toegewezen en gestart. Gedurende deze minuten is de **Toewijzingsstatus** van de pool ingesteld op **Grootte wordt gewijzigd**. U kunt doorgaan met het maken van een taak terwijl de grootte van de pool wordt gewijzigd. 

![Pool met de status Grootte wordt gewijzigd][pool_resizing]

Na enkele minuten is de status van de pool **Onveranderlijk** en worden de knooppunten gestart. Selecteer **Knooppunten** om de status van de knooppunten te controleren. Wanneer de status van een knooppunt **Inactief** is, kunt u taken op een knooppunt uitvoeren. 

## <a name="create-a-job"></a>Een taak maken

Nu u beschikt over een pool, kunt u een taak maken om uit te voeren op deze pool. Een Batch-taak is een logische groep met een of meer taken. Een Batch-taak omvat instellingen die gemeenschappelijk zijn voor de taken, zoals prioriteit en de pool waarop taken moeten worden uitgevoerd. De Batch-taak heeft in eerste instantie geen taken. 

1. Selecteer in de weer gave batch-account de optie **taken** > **toevoegen**. 

2. Voer een **taak-id** in met naam *myjob*. Selecteer *mypool* bij **Pool**. Behoud de standaardwaarden voor de overige instellingen en selecteer **OK**.

   ![Een taak maken][job_create]

Nadat de taak is gemaakt, wordt de pagina **Taken** geopend.

## <a name="create-tasks"></a>Taken maken

Maak nu voorbeeldtaken om uit te voeren in de Batch-taak. Doorgaans maakt u meerdere taken die met Batch in de wachtrij worden geplaatst en worden gedistribueerd voor uitvoering op de rekenknooppunten. In dit voorbeeld maakt u twee identieke taken. In elke taak wordt een opdrachtregel uitgevoerd om de variabelen voor de Batch-omgeving op een rekenknooppunt weer te geven. Vervolgens is er een pauze van 90 seconden. 

Wanneer u Batch gebruikt, geeft u uw app of script op de opdrachtregel op. Batch biedt een aantal manieren om apps en scripts te implementeren op rekenknooppunten. 

De eerste taak maken:

1. Selecteer **Toevoegen**.

2. Voer een **Taak-id** in met de naam *mytask*. 

3. Voer `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"` in bij **Opdrachtregel**. Behoud de standaardwaarden voor de overige instellingen en selecteer **OK**.

   ![Een taak maken][task_create]

Nadat u een taak hebt gemaakt, wordt deze in Batch in de wachtrij geplaatst voor uitvoering op de pool. Wanneer er een knooppunt beschikbaar is om de taak uit te voeren, wordt de taak uitgevoerd.

Ga terug naar stap 1 om een tweede taak te maken. Voer een andere **Taak-id** in, maar geef een identieke opdrachtregel op. Als de eerste taak nog steeds wordt uitgevoerd, wordt de tweede taak in Batch gestart op een ander knooppunt in de pool.

## <a name="view-task-output"></a>Taakuitvoer weergeven

De voorgaande taakvoorbeelden zijn in enkele minuten voltooid. Als u de uitvoergegevens van een voltooide taak wilt weergeven, selecteert u **Bestanden op knooppunt** en selecteert u vervolgens het bestand `stdout.txt`. Dit bestand bevat de standaarduitvoer van de taak. De inhoud ziet er ongeveer als volgt uit:

![Taakuitvoer weergeven][task_output]

De inhoud geeft de variabelen voor de Azure Batch-omgeving weer die zijn ingesteld op het knooppunt. Wanneer u uw eigen Batch-taken en taken maakt, kunt u naar deze omgevingsvariabelen verwijzen in opdrachtregels voor taken, en in de apps en scripts die met de opdrachtregels worden uitgevoerd.

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt doorgaan met Batch-zelfstudies en -voorbeelden, gebruikt u het Batch-account en het gekoppelde opslagaccount dat in deze snelstartgids is gemaakt. Voor het Batch-account zelf worden geen kosten in rekening gebracht.

Er worden kosten berekend voor de pool zolang de knooppunten actief zijn, zelfs als er geen taken zijn gepland. Verwijder de pool wanneer u deze niet meer nodig hebt. Selecteer in de accountweergave **Pools** en de naam van de pool. Selecteer vervolgens **Verwijderen**.  Wanneer u de pool verwijdert, wordt ook alle taakuitvoer op de knooppunten verwijderd. 

Verwijder de resourcegroep, het Batch-account en alle gerelateerde resources, wanneer u deze niet meer nodig hebt. Hiervoor selecteert u de resourcegroep voor het Batch-account en selecteert u **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u een Batch-account, een Batch-pool en een Batch-taak gemaakt. Met de Batch-taak zijn voorbeeldtaken uitgevoerd, en u hebt de uitvoer van een van de knooppunten bekeken. Nu u de belangrijkste principes van de Batch-service begrijpt, bent u er klaar voor om Batch op grotere schaal te gebruiken voor meer realistische workloads. Voor meer informatie over Azure Batch gaat u naar de Azure Batch-zelfstudies. 

> [!div class="nextstepaction"]
> [Azure Batch-zelfstudies](./tutorial-parallel-dotnet.md)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png
