---
title: Zelf studie-back-ups maken van meerdere virtuele Azure-machines
description: In deze zelf studie leert u hoe u een Recovery Services kluis kunt maken, een back-upbeleid kunt definiëren en tegelijkertijd meerdere virtuele machines kunt maken.
ms.date: 01/31/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: f9306f2ef5c4b2a53dcba17cafca9ea13b8dab43
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80245239"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Azure Portal gebruiken voor het maken van een back-up van meerdere virtuele machines

Wanneer u een back-up van gegevens in Azure maakt, kunt u die gegevens opslaan in een Azure-resource, een Recovery Services-kluis genaamd. De Recovery Services-kluisresource is beschikbaar via het menu Instellingen van de meeste Azure-services. Het voor deel van het gebruik van de Recovery Services kluis geïntegreerd in het menu instellingen van de meeste Azure-Services maakt het eenvoudig om een back-up te maken van gegevens. Het is omslachtig om afzonderlijk met elke database of de virtuele machine in uw bedrijf te werken. Wat als u een back-up wilt uitvoeren van de gegevens voor alle virtuele machines op één afdeling of op één locatie? Als u een back-upbeleid maakt en dat beleid toepast op de gewenste virtuele machines, kunt u eenvoudig een back-up maken van meerdere virtuele machines. In deze zelfstudie wordt het volgende uitgelegd:

> [!div class="checklist"]
>
> * Een Recovery Services-kluis maken
> * Een back-upbeleid definiëren
> * Het back-upbeleid toepassen voor de beveiliging van meerdere virtuele machines
> * Een on-demand back-uptaak activeren voor de beveiligde virtuele machines

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

De Recovery Services-kluis bevat de back-upgegevens en het back-upbeleid dat wordt toegepast op de beveiligde virtuele machines. Het maken van back-ups van virtuele machines is een lokaal proces. Het is niet mogelijk van virtuele machines op de ene locatie een back-up in een Recovery Services-kluis op een andere locatie te maken. Daarom moet er voor elke Azure-locatie met virtuele machines waarvoor u een back-up wilt maken, ten minste één Recovery Services-kluis op die locatie bestaan.

1. Selecteer in het menu links **Alle services** en typ *Recovery Services* in de lijst met services. Terwijl u typt, wordt de lijst met resources gefilterd. Wanneer u Recovery Services-kluizen in de lijst ziet, selecteert u deze optie om het menu Recovery Services-kluizen te openen.

    ![Het menu Recovery Services-kluizen openen](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png)

2. Klik in het menu **Recovery Services-kluizen** op **Toevoegen** om het menu Recovery Services-kluizen te openen.

    ![Het menu Kluis openen](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. In het menu Recovery Services-kluis:

    * *myRecoveryServicesVault* in bij **Naam**.
    * De huidige abonnements-id wordt weergegeven in **Abonnement**. Als u meer abonnementen hebt, kunt u een ander abonnement kiezen voor de nieuwe kluis.
    * Selecteer voor **Resourcegroep** de optie **Bestaande gebruiken** en kies *myResourceGroup*. Als *myResourceGroup* niet bestaat, selecteert u **nieuwe maken** en typt u *myResourceGroup*.
    * Kies in de vervolgkeuzelijst **Locatie** de optie *Europa - west*.
    * Klik op **Maken** om de Recovery Services-kluis te maken.

Een Recovery Services-kluis moet zich op dezelfde locatie bevinden als de virtuele machines die worden beveiligd. Als u virtuele machines in meerdere regio's hebt, moet u in elke regio een Recovery Services-kluis maken. In deze zelfstudie maakt u een Recovery Services-kluis in *Europa - west* omdat daar *myVM* (de virtuele machine die is gemaakt met de snelstartgids) is gemaakt.

Het kan enkele minuten duren voordat de Recovery Services-kluis is gemaakt. Controleer de statusmeldingen rechtsboven in de portal. Zodra de kluis is gemaakt, wordt deze weergegeven in de lijst met Recovery Services-kluizen.

Wanneer u een Recovery Services-kluis maakt, heeft de kluis standaard geografisch redundante opslag. Ten behoeve van de gegevenstolerantie worden de gegevens door de geografisch redundante opslag meerdere keren tussen twee Azure-regio's gerepliceerd.

## <a name="set-backup-policy-to-protect-vms"></a>Back-upbeleid voor het beveiligen van virtuele machines instellen

Nadat de Recovery Services-kluis is gemaakt, bestaat de volgende stap uit het configureren van de kluis voor het type gegevens en uit het instellen van het back-upbeleid. Het back-upbeleid is de planning voor hoe vaak en wanneer er herstelpunten worden gemaakt. Het beleid bevat ook de bewaartermijn voor de herstelpunten. Voor deze zelf studie gaan we ervan uit dat uw bedrijf een sport complex is met een hotel, stadion, restaurants en concessies, en dat u de gegevens op de virtuele machines beveiligt. Met de volgende stappen maakt u een back-upbeleid voor de financiële gegevens.

1. Selecteer **myRecoveryServicesVault** in de lijst met Recovery Services-kluizen om het dashboard ervan te openen.

   ![Het menu Scenario openen](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. Klik in het menu van het kluisdashboard op **Back-up** om het menu Back-up te openen.

3. Kies in de vervolgkeuzelijst **Waar wordt uw werkbelasting uitgevoerd?** van het menu Doel van de back-up de optie *Azure*. Kies in de vervolgkeuzelijst **Waarvan wilt u een back-up maken?** de optie *Virtuele machine* en klik op **Back-up**.

    Met deze acties wordt de Recovery Services-kluis voorbereid op interactie met een virtuele machine. Recovery Services-kluizen hebben een standaardbeleid waarmee elke dag een herstelpunt wordt gemaakt en de herstelpunten gedurende 30 dagen worden behouden.

    ![Het menu Scenario openen](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. Als u een nieuw beleid wilt maken in het menu Back-upbeleid, selecteert u in de vervolgkeuzelijst **Back-upbeleid kiezen** de optie *Nieuwe maken*.

    ![Workload selecteren](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. Typ in het menu **Back-upbeleid** voor **Beleidsnaam** de tekst *Financiën*. Voer de volgende wijzigingen in voor het backup-beleid:
   * Stel voor **Back-upfrequentie** de tijdzone in op *Central Time*. Omdat het sportcomplex zich in Texas bevindt, wil de eigenaar de tijd op de lokale tijd instellen. Laat de back-upfrequentie ingesteld op elke dag om 03:30 uur.
   * Stel voor **Dagelijks back-uppunt bewaren** de periode in op 90 dagen.
   * Gebruik voor **Wekelijks back-uppunt bewaren** de *Maandag* als herstelpunt en behoudt deze gedurende 52 weken.
   * Voor **Maandelijks back-uppunt bewaren** gebruikt u het herstelpunt van de eerste zondag van de maand en behoudt u deze gedurende 36 maanden.
   * Schakel de optie **Jaarlijks back-uppunt bewaren** uit. De financieel manager wil gegevens niet langer bewaren dan 36 maanden.
   * Klik op **OK** om het back-upbeleid te maken.

     ![Workload selecteren](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

     Nadat het back-upbeleid is gemaakt, koppelt u het beleid aan de virtuele machines.

6. Selecteer in het dialoog venster **virtuele machines selecteren** de optie *myVM* en klik op **OK** om het back-upbeleid op de virtuele machines te implementeren.

    Alle virtuele machines die zich op dezelfde locatie bevinden en waaraan nog geen back-upbeleid is gekoppeld, worden weergegeven. *myVMH1* en *myVMR1* zijn geselecteerd om aan het beleid *Financiën* te worden gekoppeld.

    ![Workload selecteren](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

    Wanneer de implementatie is voltooid, ontvangt u een melding dat de implementatie is voltooid.

## <a name="initial-backup"></a>Eerste back-up

U hebt back-ups ingeschakeld voor de Recovery Services-kluizen, maar er is geen eerste back-up gemaakt. Het is een aanbevolen procedure voor herstel na noodgevallen om de eerste back-up te activeren, zodat uw gegevens beveiligd zijn.

Ga als volgt te werk om een on-demand back-uptaak uit te voeren:

1. Klik op het kluisdashboard op **3** onder **Back-upitems** om het menu Back-upitems te openen.

    ![Het pictogram Instellingen](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    Het menu **Back-upitems** wordt geopend.

2. Klik in het menu **Back-upitems** op **Virtuele Azure-machine** om de lijst met virtuele machines die aan de kluis zijn gekoppeld te openen.

    ![Het pictogram Instellingen](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    De lijst **Back-upitems** wordt geopend.

    ![Geactiveerde back-uptaak](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. Klik in de lijst **Back-upitems** op het weglatingsteken **... ** om het contextmenu te openen.

4. Selecteer in het contextmenu **Nu back-up maken**.

    ![Contextmenu](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Het menu Nu back-up maken wordt geopend.

5. Voer in het menu Nu back-up maken de laatste dag in waarop het herstelpunt moet worden behouden en klik op **Back-up**.

    ![de laatste dag instellen dat het herstelpunt van Nu back-up maken wordt bewaard](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Implementatiemeldingen laten u weten dat de back-uptaak is geactiveerd en dat u de voortgang van de taak op de pagina Back-uptaken kunt controleren. Afhankelijk van de grootte van de virtuele machine kan het maken van de eerste back-up even duren.

    Wanneer de eerste back-uptaak is voltooid, ziet u de status ervan in het menu Back-uptaak. Door de on-demand back-uptaak is het eerste herstelpunt voor *myVM* gemaakt. Als u een back-up wilt maken van andere virtuele machines, herhaalt u deze stappen voor elke virtuele machine.

    ![Tegel Back-uptaken](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u door wilt gaan met andere zelfstudies, verwijdert u de resources die u in deze zelfstudie hebt gemaakt niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die tijdens deze zelfstudie in Azure Portal zijn gemaakt.

1. Klik op het dashboard **myRecoveryServicesVault** op **3** onder **Back-upitems** om het menu Back-upitems te openen.

    ![Het pictogram Instellingen](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

2. Klik in het menu **Back-upitems** op **Virtuele Azure-machine** om de lijst met virtuele machines die aan de kluis zijn gekoppeld te openen.

    ![Het pictogram Instellingen](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    De lijst **Back-upitems** wordt geopend.

3. Klik in de lijst **Back-upitems** op het weglatingsteken om het contextmenu te openen.

    ![Het pictogram Instellingen](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. Selecteer in het context menu de optie **back-up stoppen** om het menu back-up stoppen te openen.

    ![Het pictogram Instellingen](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. Selecteer in het menu **Back-up stoppen** het bovenste vervolgkeuzemenu en kies **Back-upgegevens verwijderen**.

6. Typ in het dialoogvenster **Typ de naam van het back-upitem** de tekst *myVM*.

7. Zodra het back-upitem is gecontroleerd (er wordt een vinkje weer gegeven), wordt de knop **back-up stoppen** ingeschakeld. Klik op **Back-up stoppen** om het beleid te stoppen en de herstelpunten te verwijderen.

    ![klik op Back-up stoppen om de kluis te verwijderen](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

8. Klik in het menu **myRecoveryServicesVault** op **Verwijderen**.

    ![klik op Back-up stoppen om de kluis te verwijderen](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    Zodra de kluis is verwijderd, keert u terug naar de lijst met Recovery Services-kluizen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure Portal gebruikt voor het volgende:

> [!div class="checklist"]
>
> * Een Recovery Services-kluis maken
> * De kluis instellen voor het beveiligen van virtuele machines
> * Een aangepast back-up- en bewaarbeleid maken
> * Het beleid toepassen voor de beveiliging van meerdere virtuele machines
> * Een on-demand back-up activeren voor virtuele machines

Ga verder met de volgende zelfstudie in Azure om een virtuele machine te herstellen vanaf schijf.

> [!div class="nextstepaction"]
> [Virtuele machines herstellen met CLI](./tutorial-restore-disk.md)
