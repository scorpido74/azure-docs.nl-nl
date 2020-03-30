---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 729e757c69887bbdce324e2d8383c970995dc94a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903674"
---
## <a name="sign-in-to-azure"></a>Aanmelden bij Azure 

Meld u aan bij Azure Portal op https://portal.azure.com.

> [!NOTE]
> Als u zich hebt geregistreerd om gedeelde galerieën te gebruiken `Microsoft.Compute` tijdens het voorbeeld, moet u de provider mogelijk opnieuw registreren. Open [Cloud Shell](https://shell.azure.com/bash) en typ:`az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Een afbeeldingsgalerie maken

Een afbeeldingsgalerie is de primaire bron die wordt gebruikt voor het inschakelen van het delen van afbeeldingen. Toegestane tekens voor galerienaam zijn hoofdletters, cijfers, stippen en perioden. De naam van de galerie mag geen streepjes bevatten.  Galerijnamen moeten uniek zijn binnen uw abonnement. 

In het volgende voorbeeld wordt een galerie met de naam *myGallery* gemaakt in de *brongroep myGalleryRG.*

1. Selecteer in de linkerbovenhoek van Azure Portal **Een resource maken**.
1. Gebruik het type **Gedeelde afbeeldingsgalerie** in het zoekvak en selecteer **Gedeelde afbeeldingsgalerie** in de resultaten.
1. Klik op de pagina **Gedeelde afbeeldingsgalerie** op **Maken**.
1. Selecteer het juiste abonnement.
1. Selecteer **in de groep Resource**de optie Nieuw **maken** en typ *myGalleryRG* voor de naam.
1. Typ **in Naam** *myGallery* voor de naam van de galerie.
1. Laat de standaardinstelling voor **Regio .**
1. U een korte beschrijving van de galerie typen, zoals *Mijn afbeeldingsgalerie om te testen.* en klik vervolgens op **Controleren + maken**.
1. Nadat de validatie is doorgegeven, selecteert **u Maken**.
1. Wanneer de implementatie is voltooid, selecteert u **Ga naar resource**.
   
## <a name="create-an-image-definition"></a>Een afbeeldingsdefinitie maken 

Afbeeldingsdefinities maken een logische groepering voor afbeeldingen. Ze worden gebruikt om informatie over de afbeeldingsversies die erin zijn gemaakt, te beheren. Afbeeldingsdefinitienamen kunnen bestaan uit hoofdletters of kleine letters, cijfers, stippen, streepjes en perioden. Zie [Afbeeldingsdefinities](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)voor meer informatie over de waarden die u voor een afbeeldingsdefinitie opgeven.

Maak de definitie van galerijafbeelding in uw galerie. In dit voorbeeld wordt de galerijafbeelding *myImageDefinition*genoemd.

1. Selecteer boven aan de pagina **een nieuwe afbeeldingsdefinitie toevoegen** op de pagina voor uw nieuwe afbeeldingsgalerie. 
1. Typ *myImageDefinition*voor **de naam van de afbeeldingsdefinitie**.
1. Selecteer **voor het besturingssysteem**de juiste optie op basis van uw bron-VM.
1. Selecteer **voor VM-generatie**de optie op basis van uw bron-VM. In de meeste gevallen zal dit *Gen 1*zijn. Zie [Ondersteuning voor generatie 2 VM's voor](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)meer informatie .
1. Selecteer de optie op basis van de bron-VM voor de status van het **besturingssysteem.** Zie [Algemeen en gespecialiseerd](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images)voor meer informatie.
1. Typ *myPublisher*voor **Publisher**. 
1. Typ mijn *aanbieding*voor **aanbieding.**
1. Typ *mySKU*voor **SKU**.
1. Wanneer u klaar bent, selecteert u **Controleren + maken**.
1. Nadat de afbeeldingsdefinitie is gevalideerd, selecteert u **Maken**.
1. Wanneer de implementatie is voltooid, selecteert u **Ga naar resource**.


## <a name="create-an-image-version"></a>Een afbeeldingsversie maken

Maak een afbeeldingsversie op basis van een beheerde afbeelding. In dit voorbeeld is de afbeeldingsversie *1.0.0* en wordt deze gerepliceerd naar zowel *datacenters in West Central US* als South Central *US.* Houd er bij het kiezen van doelgebieden voor replicatie rekening mee dat u ook het *brongebied* als doel voor replicatie moet opnemen.

Toegestane tekens voor de afbeeldingsversie zijn getallen en perioden. Getallen moeten zich binnen het bereik van een 32-bits geheel getal bevinden. Formaat: *MajorVersion*. *MinorVersion*. *Patch*.

De stappen voor het maken van een afbeeldingsversie zijn iets anders, afhankelijk van of de bron een algemene afbeelding of een momentopname van een gespecialiseerde virtuele machine is. 

### <a name="option-generalized"></a>Optie: Gegeneraliseerd

1. Selecteer **versie toevoegen** boven aan de pagina op de pagina voor de pagina voor uw afbeeldingsdefinitie.
1. Selecteer **in Regio**het gebied waar uw beheerde afbeelding is opgeslagen. Afbeeldingsversies moeten worden gemaakt in dezelfde regio als de beheerde afbeelding waaruit ze zijn gemaakt.
1. Typ *1.0.0*voor **Naam**. De naam van de afbeeldingsversie moet *groot zijn.* *mineur*. *patchformaat* met gehele getallen. 
1. Selecteer **in de bronafbeelding**de bronbeheerde afbeelding in de vervolgkeuzelijst.
1. Laat in **Uitsluiten van uiterlijk**de standaardwaarde van *Nee*achter .
1. Selecteer **voor einddatum**een datum uit de agenda die in de toekomst een paar maanden bedraagt.
1. Laat **in Replicatie**het aantal **standaardreplica's als 1.** U moet repliceren naar het brongebied, dus laat de eerste replica als standaard en kies vervolgens een tweede replicaregio die *Oost-VS is.*
1. Wanneer u klaar bent, selecteert u **Controleren + maken**. Azure valideert de configuratie.
1. Wanneer de afbeeldingsversie de validatie doorstaat, selecteert u **Maken**.
1. Wanneer de implementatie is voltooid, selecteert u **Ga naar resource**.

Het kan even duren voordat de afbeelding is gerepliceerd naar alle doelgebieden.

### <a name="option-specialized"></a>Optie: Specialized

1. Selecteer **versie toevoegen** boven aan de pagina op de pagina voor de pagina voor uw afbeeldingsdefinitie.
1. Selecteer **in Regio**het gebied waar uw momentopname is opgeslagen. Afbeeldingsversies moeten worden gemaakt in dezelfde regio als de bron waaruit ze zijn gemaakt.
1. Typ *1.0.0*voor **Naam**. De naam van de afbeeldingsversie moet *groot zijn.* *mineur*. *patchformaat* met gehele getallen. 
1. Selecteer in **de momentopname VAN de OS-schijf**de momentopname van de bron-VM in de vervolgkeuzelijst. Als uw bron-VM een gegevensschijf had die u wilt opnemen, selecteert u het juiste **LUN-nummer** in de vervolgkeuzelijst en selecteert u vervolgens de momentopname van de gegevensschijf voor **momentopname gegevensschijf**. 
1. Laat in **Uitsluiten van uiterlijk**de standaardwaarde van *Nee*achter .
1. Selecteer **voor einddatum**een datum uit de agenda die in de toekomst een paar maanden bedraagt.
1. Laat **in Replicatie**het aantal **standaardreplica's als 1.** U moet repliceren naar het brongebied, dus laat de eerste replica als standaard en kies vervolgens een tweede replicaregio die *Oost-VS is.*
1. Wanneer u klaar bent, selecteert u **Controleren + maken**. Azure valideert de configuratie.
1. Wanneer de afbeeldingsversie de validatie doorstaat, selecteert u **Maken**.
1. Wanneer de implementatie is voltooid, selecteert u **Ga naar resource**.

## <a name="share-the-gallery"></a>De galerie delen

We raden u aan de toegang op het niveau van de afbeeldingsgalerie te delen. De volgende wandelingen u door het delen van de galerij die u zojuist hebt gemaakt.

1. Open de [Azure Portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen**in het menu aan de linkerkant . 
1. Selecteer **myGalleryRG**in de lijst met brongroepen . Het blad voor uw resourcegroep wordt geopend.
1. Selecteer in het menu links van de **myGalleryRG-pagina** **het toegangscontrolebesturingselement (IAM).** 
1. Selecteer **Onder Een roltoewijzing toevoegen**de optie **Toevoegen**. Het deelvenster **Een roltoewijzing toevoegen** wordt geopend. 
1. Selecteer **Reader**onder **Rol**.
1. Onder **toegang toewijzen aan**, laat de standaard van Azure **AD-gebruiker, groep of serviceprincipal**.
1. Typ **onder Selecteren**het e-mailadres van de persoon die u wilt uitnodigen.
1. Als de gebruiker zich buiten uw organisatie bevindt, ziet u het bericht **deze gebruiker ontvangt een e-mail waarmee hij of zij met Microsoft kan samenwerken.** Selecteer de gebruiker met het e-mailadres en klik op **Opslaan**.

Als de gebruiker zich buiten uw organisatie bevindt, ontvangt hij of zij een e-mailuitnodiging om lid te worden van de organisatie. De gebruiker moet de uitnodiging accepteren, dan kunnen ze de galerij en alle afbeeldingsdefinities en -versies in hun lijst met bronnen zien.

