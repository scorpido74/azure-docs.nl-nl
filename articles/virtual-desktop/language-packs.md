---
title: Taalpakketten installeren op Windows 10 VM's in Windows Virtual Desktop - Azure
description: Taalpakketten installeren voor Windows 10-vm's met meerdere sessies in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c40df9d821e069e2cd5ff0c42d5841f6b9041c96
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80634033"
---
# <a name="install-language-packs"></a>Taalpakketten installeren

Wanneer u Windows Virtual Desktop-implementaties internationaal instelt, is het een goed idee om ervoor te zorgen dat uw implementatie meerdere talen ondersteunt. U taalpakketten installeren op een VM-afbeelding (Virtual Machine) (Windows 10 Enterprise) om zoveel talen te ondersteunen als uw organisatie nodig heeft. In dit artikel vindt u hoe u taalpakketten installeert en afbeeldingen vastleggen waarmee uw gebruikers hun eigen weergavetalen kunnen kiezen.

Meer informatie over het implementeren van een VM in Azure bij [Het maken van een virtuele Windows-machine in een beschikbaarheidszone met de Azure-portal](../virtual-machines/windows/create-portal-availability-zone.md).

>[!NOTE]
>Dit artikel is van toepassing op vm's met meerdere sessies van Windows 10 Enterprise.

## <a name="install-a-language-pack"></a>Een taalpakket installeren

Als u een VM-afbeelding met taalpakketten wilt maken, moet u eerst taalpakketten op een machine installeren en er een afbeelding van maken.

Taalpakketten installeren:

1. Meld u aan als beheerder.
2. Zorg ervoor dat u alle nieuwste Windows- en Windows Store-updates hebt geïnstalleerd.
3. Ga naar **Instellingentijd** > **& Taalgebied** > **Region**.
4. Selecteer **onder Land of regio**het land of de regio van uw voorkeur in het vervolgkeuzemenu.
    In dit voorbeeld gaan we **Frankrijk**selecteren, zoals in de volgende schermafbeelding wordt weergegeven:

    ![Een screenshot van de regiopagina. De regio die momenteel wordt geselecteerd is Frankrijk.](media/region-page-france.png)

5. Selecteer daarna **Taal**en selecteer **Vervolgens Een taal toevoegen**. Kies de taal die u wilt installeren in de lijst en selecteer **Volgende**.
6. Wanneer het venster **Taalfuncties installeren** wordt geopend, schakelt u het selectievakje selectievakje **Taalpakket installeren in en stel u in als mijn Windows-weergavetaal**.
7. Selecteer **Installeren**.
8. Als u meerdere talen tegelijk wilt toevoegen, selecteert u **Een taal toevoegen**en herhaalt u het proces om een taal toe te voegen in stap 5 en 6. Herhaal dit proces voor elke taal die u wilt installeren. U echter slechts één taal tegelijk instellen als weergavetaal.

    Laten we een snelle visuele demonstratie doormaken. Op de volgende afbeeldingen is te zien hoe u de Franse en Nederlandse taalpakketten installeert en vervolgens Frans als weergavetaal.

    ![Een screenshot van de pagina Taal aan het begin van het proces. De geselecteerde Windows-weergavetaal is Engels.](media/language-page-default.png)

    ![Een schermafbeelding van het venster taalselectie. De gebruiker heeft "Frans" ingevoerd in de zoekbalk om de Franse taalpakketten te vinden.](media/select-language-french.png)

    ![Een schermafbeelding van de pagina Taalfuncties installeren. Frans is geselecteerd als de voorkeurstaal. De geselecteerde opties zijn 'Mijn weergavetaal instellen', 'Taalpakket installeren', 'Spraakherkenning' en 'Handschrift'.](media/install-language-features.png)

    Nadat uw taalpakketten zijn geïnstalleerd, ziet u de namen van uw taalpakketten in de lijst met talen.

    ![Een screenshot van de taalpagina met de nieuwe taalpakketten geïnstalleerd. De Franse en Nederlandse taalpakketten staan onder 'voorkeurstalen'.](media/language-page-complete.png)

9. Als er een venster wordt weergegeven waarin wordt gevraagd u af te melden bij uw sessie. Meld je af en meld je opnieuw aan. Uw weergavetaal moet nu de taal zijn die u hebt geselecteerd.

10.  Ga naar De**klok en regio gebied** >  **van het Configuratiescherm** > .**Region**

11.  Wanneer het **venster Regio** wordt geopend, selecteert u het tabblad **Beheer** en selecteert u Vervolgens **Instellingen kopiëren**.

12.  Schakel de selectievakjes met het label **Welkomstscherm en systeemaccounts** en **Nieuwe gebruikersaccounts in.**

13.  Selecteer **OK**.

14.  Er wordt een venster geopend en u verteld dat u uw sessie opnieuw moet starten. Selecteer **Nu opnieuw starten**.

15.  Nadat u zich weer hebt aangemeld, gaat u terug naar De**Klok en regio van** >  **het Configuratiescherm** > **.**

16.  Selecteer het tabblad **Beheer.**

17.  Selecteer **Systeemlandinstellingen wijzigen**.

18. Selecteer in de vervolgkeuzelijst onder **Huidige systeemlandine**de landtaal die u wilt gebruiken. Selecteer daarna **OK**.

19. Selecteer **Nu opnieuw starten** om de sessie opnieuw te starten.

Gefeliciteerd, je hebt je taalpakketten geïnstalleerd!

Voordat u verdergaat, controleert u of op uw systeem de nieuwste versies van Windows en Windows Store zijn geïnstalleerd.

## <a name="sysprep"></a>Sysprep

Vervolgens moet u uw machine sysprep om het voor te bereiden op het beeld vastleggen proces.

Ga als het gaat om een sysprep van uw machine:

1. Open PowerShell als administrator.
2. Voer de volgende cmdlet uit om naar de juiste map te gaan:
   
    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Voer vervolgens de volgende cmdlet uit:
    
    ```powershell
    .\sysprep.exe
    ```

4. Wanneer het venster Systeemvoorbereidingsgereedschap wordt geopend, schakelt u het selectievakje Met het label **Generaliseren**in, ga dan naar **Opties afsluiten** en selecteert **u Afsluiten** in het vervolgkeuzemenu.

>[!NOTE]
>Het syprep-proces duurt een paar minuten. Als de VM wordt afgesloten, wordt de verbinding verbroken met uw externe sessie.

### <a name="resolve-sysprep-errors"></a>Sysprep-fouten oplossen

Als u een foutbericht ziet tijdens het sysprep-proces, moet u het nieuws als volgen:

1. Open **Drive C** en ga naar **Windows** > **System32 Sysprep** > **Panther,** open vervolgens het **installatiebestand.**

   De tekst in het foutbestand zal u vertellen dat u een specifiek taalpakket moet verwijderen, zoals in de volgende afbeelding wordt weergegeven. Kopieer de naam van het taalpakket voor de volgende stap.

   ![Een screenshot van het installatiebestand. De tekst met de pakketnaam is donkerblauw gemarkeerd.](media/setuperr-package-name.png)

2. Open een nieuw PowerShell-venster en voer de volgende cmdlet uit met de pakketnaam die u in stap 2 hebt gekopieerd om het taalpakket te verwijderen:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Controleer of u het pakket hebt verwijderd `Remove-AppxPackage` door de cmdlet opnieuw uit te voeren. Als u het pakket hebt verwijderd, ziet u een bericht waarin staat dat het pakket dat u probeert te verwijderen er niet is.

4. Voer `sysprep.exe` de cmdlet opnieuw uit.

## <a name="capture-the-image"></a>De afbeelding vastleggen

Nu uw systeem klaar is, u een afbeelding vastleggen, zodat andere gebruikers vm's kunnen gaan gebruiken op basis van uw systeem zonder het configuratieproces te hoeven herhalen.

Ga als een mogelijk maken van een afbeelding:

1. Ga naar de Azure-portal en selecteer de naam van de machine die u hebt geconfigureerd in [Een taalpakket](#install-a-language-pack) en [sysprep](#sysprep)installeren.

2. Selecteer **Vastleggen**.

3. Voer een naam voor uw afbeelding in het veld **Naam** in en wijs deze toe aan de resourcegroep met het vervolgkeuzemenu **Resourcegroep,** zoals in de volgende afbeelding wordt weergegeven.

   ![Een schermafbeelding van het afbeeldingsvenster maken. De naam die de gebruiker heeft gegeven aan deze test afbeelding is "vmwvd-image-fr," en ze hebben toegewezen aan de "testwvdimagerg" resource groep.](media/create-image.png)

4. Selecteer **Maken**.

5. Wacht een paar minuten tot het opnameproces is voltooid. Wanneer de afbeelding klaar is, ziet u een bericht in het Meldingencentrum waarin wordt vermeld dat de afbeelding is vastgelegd.

U nu een VM implementeren met uw nieuwe afbeelding. Wanneer u de VM implementeert, moet u de instructies volgen in [Een virtuele Windows-machine maken in een beschikbaarheidszone met de Azure-portal.](../virtual-machines/windows/create-portal-availability-zone.md)

### <a name="how-to-change-display-language-for-standard-users"></a>Weergavetaal wijzigen voor standaardgebruikers

Standaardgebruikers kunnen de weergavetaal op hun VM's wijzigen.

Ga als een andere versie van de weergavetaal:

1. Ga naar **Taalinstellingen**. Als u niet weet waar dat is, u **Taal** invoeren in de zoekbalk in het menu Start.

2. Selecteer in de vervolgkeuzelijst Windows-weergavetaal de taal die u als weergavetaal wilt gebruiken.

3. Meld u af van uw sessie en meld u vervolgens weer aan. De weergavetaal moet nu degene zijn die u in stap 2 hebt geselecteerd.
