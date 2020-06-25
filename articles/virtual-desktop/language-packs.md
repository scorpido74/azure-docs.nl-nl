---
title: Taal pakketten installeren op Windows 10-Vm's in virtueel bureau blad van Windows-Azure
description: Taal pakketten installeren voor Vm's met meerdere sessies van Windows 10 in Windows virtueel bureau blad.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70592d940e3766597475f4a7b90a3902a53406d5
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85361299"
---
# <a name="install-language-packs"></a>Taalpakketten installeren

Wanneer u de implementaties van Windows virtueel bureau blad internationaal hebt ingesteld, is het een goed idee om ervoor te zorgen dat uw implementatie meerdere talen ondersteunt. U kunt taal pakketten installeren op een installatie kopie van een virtuele machine met Windows 10 Enter prise voor meerdere sessies (VM) ter ondersteuning van zoveel talen als uw organisatie behoeften. In dit artikel wordt uitgelegd hoe u taal pakketten installeert en installatie kopieën kunt vastleggen waarmee uw gebruikers hun eigen weergave talen kunnen kiezen.

Meer informatie over het implementeren van een VM in azure vindt u [in een virtuele Windows-machine maken in een beschikbaarheids zone met de Azure Portal](../virtual-machines/windows/create-portal-availability-zone.md).

>[!NOTE]
>Dit artikel is van toepassing op Vm's met meerdere sessies met Windows 10 Enter prise.

## <a name="install-a-language-pack"></a>Een taal pakket installeren

Als u een VM-installatie kopie met taal pakketten wilt maken, moet u eerst taal pakketten installeren op een computer en een installatie kopie hiervan vastleggen.

Taal pakketten installeren:

1. Meld u aan als beheerder.
2. Zorg ervoor dat u alle meest recente Windows-en Windows Store-updates hebt geïnstalleerd.
3. Ga naar **instellingen**  >  **tijd & taal**  >  **regio**.
4. Onder **land of regio**selecteert u het land of de regio van uw voor keur in de vervolg keuzelijst.
    In dit voor beeld gaan we **Frank rijk**selecteren, zoals wordt weer gegeven in de volgende scherm afbeelding:

    > [!div class="mx-imgBorder"]
    > ![Een scherm afbeelding van de regio pagina. De regio die momenteel is geselecteerd, is Frank rijk.](media/region-page-france.png)

5. Selecteer vervolgens **taal**en selecteer vervolgens **een taal toevoegen**. Kies de taal die u wilt installeren in de lijst en selecteer vervolgens **volgende**.
6. Wanneer het venster **taal functies installeren** wordt geopend, schakelt u het selectie vakje taal **pakket installeren in en stelt u in als mijn Windows-weergave taal**.
7. Selecteer **Installeren**.
8. Als u meerdere talen tegelijk wilt toevoegen, selecteert u **een taal toevoegen**en herhaalt u het proces om een taal toe te voegen aan stap 5 en 6. Herhaal dit proces voor elke taal die u wilt installeren. U kunt echter maar één taal per keer instellen als uw weergave taal.

    Laten we een snelle visuele demonstratie uitvoeren. De volgende afbeeldingen laten zien hoe u de Franse en Nederlandse taal pakketten installeert en vervolgens Frans als weergave taal instelt.

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname van de taal pagina aan het begin van het proces. De geselecteerde Windows-weergave taal is Engels.](media/language-page-default.png)

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname van het taal selectie venster. De gebruiker heeft Frans ingevoerd in de zoek balk om de pakketten in de Franse taal te vinden.](media/select-language-french.png)

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname van de pagina taal functies installeren. Frans is geselecteerd als de voorkeurs taal. De geselecteerde opties zijn ' mijn weergave taal instellen ', ' taal pakket installeren ', ' spraak herkenning ' en ' hand schrift '.](media/install-language-features.png)

    Nadat de taal pakketten zijn geïnstalleerd, ziet u dat de namen van uw taal pakketten worden weer gegeven in de lijst met talen.

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname van de taal pagina waarop de nieuwe taal pakketten zijn geïnstalleerd. De Franse en Nederlandse taal pakketten worden vermeld onder voorkeurs talen.](media/language-page-complete.png)

9. Als er een venster wordt weer gegeven waarin u wordt gevraagd om u af te melden bij uw sessie. Meld u af en meld u vervolgens opnieuw aan. De weergave taal moet nu de geselecteerde taal zijn.

10.  Ga naar de klok van **het configuratie scherm**  >  **en**de regio  >  **regio**.

11.  Wanneer het **regio** venster wordt geopend, selecteert u het tabblad **beheer** en selecteert u **instellingen kopiëren**.

12.  Selecteer de selectie vakjes met het label **aanmeldings scherm en systeem accounts** en **nieuwe gebruikers accounts**.

13.  Selecteer **OK**.

14.  Er wordt een venster geopend waarin u de sessie opnieuw kunt starten. Selecteer **nu opnieuw opstarten**.

15.  Nadat u zich hebt aangemeld, gaat u terug naar de klok van **het configuratie scherm**  >  **en de regio**  >  **regio**.

16.  Selecteer het tabblad **beheer** .

17.  Selecteer **systeem land instelling wijzigen**.

18. Selecteer in de vervolg keuzelijst onder **huidige systeem land instelling**de taal van de land instellingen die u wilt gebruiken. Daarna selecteert u **OK**.

19. Selecteer **nu opnieuw opstarten** om de sessie opnieuw te starten.

Gefeliciteerd, u hebt uw taal pakketten geïnstalleerd.

Voordat u doorgaat, moet u controleren of op uw systeem de nieuwste versies van Windows en Windows Store zijn geïnstalleerd.

## <a name="sysprep"></a>Sysprep

Vervolgens moet u uw computer Sysprep om deze voor te bereiden voor het proces voor het vastleggen van de installatie kopie.

U kunt als volgt Sysprep uitvoeren op uw computer:

1. Open PowerShell als administrator.
2. Voer de volgende cmdlet uit om naar de juiste map te gaan:

    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Voer vervolgens de volgende cmdlet uit:

    ```powershell
    .\sysprep.exe
    ```

4. Wanneer het hulp programma voor systeem voorbereiding wordt geopend, schakelt u het selectie vakje **generalize**in, gaat u naar **Opties voor afsluiten** en selecteert u **Afsluiten** in de vervolg keuzelijst.

>[!NOTE]
>Het duurt enkele minuten voordat het syprep-proces is voltooid. Wanneer de VM wordt afgesloten, wordt de verbinding met uw externe sessie verbroken.

### <a name="resolve-sysprep-errors"></a>Sysprep-fouten oplossen

Als er tijdens het Sysprep-proces een fout bericht wordt weer gegeven, kunt u het volgende doen:

1. Open **station C** en ga naar **Windows**  >  **System32 Sysprep**  >  **Panther**en open vervolgens het **Setuperr** -bestand.

   De tekst in het fout bestand geeft aan dat u een specifiek taal pakket moet verwijderen, zoals wordt weer gegeven in de volgende afbeelding. Kopieer de naam van het taal pakket voor de volgende stap.

   > [!div class="mx-imgBorder"]
   > ![Een scherm opname van het Setuperr-bestand. De tekst met de pakket naam wordt in donker blauw gemarkeerd.](media/setuperr-package-name.png)

2. Open een nieuw Power shell-venster en voer de volgende cmdlet uit met de pakket naam die u in stap 2 hebt gekopieerd om het taal pakket te verwijderen:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Controleer of u het pakket hebt verwijderd door de `Remove-AppxPackage` cmdlet opnieuw uit te voeren. Als u het pakket hebt verwijderd, wordt een bericht weer gegeven met de melding dat het pakket dat u probeert te verwijderen niet aanwezig is.

4. Voer de `sysprep.exe` cmdlet opnieuw uit.

## <a name="capture-the-image"></a>De installatie kopie vastleggen

Nu uw systeem klaar is, kunt u een installatie kopie vastleggen zodat andere gebruikers met behulp van Vm's op basis van uw systeem kunnen beginnen zonder het configuratie proces te herhalen.

Een installatie kopie vastleggen:

1. Ga naar de Azure Portal en selecteer de naam van de computer die u hebt geconfigureerd in [een taal pakket installeren](#install-a-language-pack) en [Sysprep](#sysprep).

2. Selecteer **vastleggen**.

3. Voer in het veld **naam** een naam in voor de afbeelding en wijs deze toe aan de resource groep met behulp van de vervolg keuzelijst **resource groep** , zoals weer gegeven in de volgende afbeelding.

   > [!div class="mx-imgBorder"]
   > ![Een scherm opname van het venster installatie kopie maken. De naam die de gebruiker aan deze test installatie kopie heeft gegeven, is "vmwvd-image-FR" en ze hebben deze toegewezen aan de resource groep ' testwvdimagerg '.](media/create-image.png)

4. Selecteer **Maken**.

5. Wacht enkele minuten totdat het vastleggen is voltooid. Wanneer de installatie kopie gereed is, ziet u een bericht in het meldingen centrum zodat u weet dat de afbeelding is vastgelegd.

U kunt nu een VM implementeren met behulp van uw nieuwe installatie kopie. Wanneer u de virtuele machine implementeert, moet u de instructies in [een virtuele Windows-machine maken in een beschikbaarheids zone met de Azure Portal](../virtual-machines/windows/create-portal-availability-zone.md)volgen.

### <a name="how-to-change-display-language-for-standard-users"></a>De weergave taal voor standaard gebruikers wijzigen

Standaard gebruikers kunnen de weergave taal van hun virtuele machines wijzigen.

De weergave taal wijzigen:

1. Ga naar **taal instellingen**. Als u dat niet weet, kunt u de **taal** invoeren in de zoek balk van het menu Start.

2. Selecteer in de vervolg keuzelijst Windows-weergave taal de taal die u wilt gebruiken als uw weergave taal.

3. Meld u af bij uw sessie en meld u vervolgens opnieuw aan. De weergave taal moet nu zijn geselecteerd in stap 2.
