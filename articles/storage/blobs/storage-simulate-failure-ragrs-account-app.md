---
title: Zelfstudie - Een fout simuleren bij het lezen van gegevens uit het primaire gebied
titleSuffix: Azure Storage
description: Een fout simuleren bij het lezen van gegevens uit het primaire gebied wanneer georedundante opslag (RA-GRS) voor lezen is ingeschakeld voor het opslagaccount.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.openlocfilehash: 3693ae8c9c425c35ab1a4ebf88b6cd3dfb58f92c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061295"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Zelfstudie: Een fout simuleren bij het lezen van gegevens uit het primaire gebied

Deze zelfstudie is deel twee van een serie. Hierin leert u over de voordelen van [read-access geo-redundante opslag](../common/storage-redundancy.md) (RA-GRS) door een fout te simuleren.

Als u een fout wilt simuleren, u [statische routering](#simulate-a-failure-with-an-invalid-static-route) of [fiddler](#simulate-a-failure-with-fiddler)gebruiken. Met beide methoden u fouten simuleren voor aanvragen voor het primaire eindpunt van uw [geo-redundante leestoegang](../common/storage-redundancy.md) (RA-GRS)-opslagaccount, waardoor de toepassing in plaats daarvan vanaf het secundaire eindpunt wordt gelezen.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

In deel twee van de serie leert u het volgende:

> [!div class="checklist"]
> * De toepassing uitvoeren en onderbreken
> * Een fout simuleren met [een ongeldige statische route](#simulate-a-failure-with-an-invalid-static-route) of [fiddler](#simulate-a-failure-with-fiddler)
> * Herstel van het primaire eindpunt simuleren

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u de vorige zelfstudie voltooien: [Uw toepassingsgegevens zeer beschikbaar maken met Azure-opslag.][previous-tutorial]

Als u een fout met statische routering wilt simuleren, gebruikt u een opdrachtprompt met verhoogde bevoegdheid.

Als u een storing wilt simuleren met Fiddler, downloadt en [installeert u Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Een fout simuleren met een ongeldige statische route

U kunt een ongeldige statische route maken voor alle aanvragen naar het primaire eindpunt van uw [geografisch redundante](../common/storage-redundancy.md) opslagaccount met leestoegang. In deze zelfstudie wordt de lokale host gebruikt als de gateway voor routeringsaanvragen voor de opslagaccount. Het gebruik van de lokale host als de gateway zorgt ervoor dat alle aanvragen naar het primaire eindpunt van uw opslagaccount in een lusvorm terugkeren naar de host, wat vervolgens tot een fout leidt. Voer de volgende stappen uit om een fout en herstel van het primaire eindpunt met een ongeldige statische route te simuleren.

### <a name="start-and-pause-the-application"></a>De toepassing starten en onderbreken

Gebruik de instructies in de [vorige zelfstudie][previous-tutorial] om het voorbeeld te starten en het testbestand te downloaden, om te bevestigen dat het afkomstig is van primaire opslag. Afhankelijk van uw doelplatform u het voorbeeld vervolgens handmatig onderbreken of bij een prompt wachten.

### <a name="simulate-failure"></a>Fout simuleren

Terwijl de toepassing is onderbroken, opent u een opdrachtprompt op Windows als beheerder of voert u terminal uit als root op Linux.

Informatie over het primaire eindpuntdomein van het opslagaccount door de volgende `STORAGEACCOUNTNAME` opdracht in te voeren op een opdrachtprompt of -terminal, vervangen door de naam van uw opslagaccount.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Kopieer het IP-adres van uw opslagaccount naar een teksteditor voor later gebruik.

Als u het IP-adres van uw lokale host wilt ophalen, typt u `ipconfig` in de Windows-opdrachtprompt of `ifconfig` in de Linux-terminal.

Als u een statische route voor een doelhost wilt toevoegen, typt u `<destination_ip>` de volgende opdracht `<gateway_ip>` op een Windows-opdrachtprompt of Linux-terminal, vervangen door uw IP-adres voor uw opslagaccount en door uw lokale host-IP-adres.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

In het venster met het lopende voorbeeld hervat u de toepassing of drukt u op de juiste toets om het voorbeeldbestand te downloaden en te bevestigen dat het afkomstig is van secundaire opslag. U het voorbeeld vervolgens opnieuw onderbreken of bij de prompt wachten.

### <a name="simulate-primary-endpoint-restoration"></a>Herstel van het primaire eindpunt simuleren

Als u wilt simuleren dat het primaire eindpunt opnieuw functioneel wordt, verwijdert u de ongeldige statische route uit de routeringstabel. Hiermee worden alle aanvragen naar het primaire eindpunt via de standaardgateway gerouteerd. Typ de volgende opdracht op een Windows-opdrachtprompt of Linux-terminal.

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route delete <destination_ip>
```

U de toepassing vervolgens hervatten of op de juiste toets drukken om het voorbeeldbestand opnieuw te downloaden, dit keer om te bevestigen dat het opnieuw afkomstig is van primaire opslag.

## <a name="simulate-a-failure-with-fiddler"></a>Een fout simuleren met Fiddler

Als u een fout wilt simuleren met Fiddler, voert u een mislukte reactie voor aanvragen in op het primaire eindpunt van uw geografisch redundante opslagaccount met leestoegang.

In de volgende gedeelten wordt uitgelegd hoe u een fout simuleert en het primaire eindpunt herstelt met Fiddler.

### <a name="launch-fiddler"></a>Start Fiddler

Open Fiddler en selecteer **Rules** en **Customize Rules**.

![Fiddler-regels aanpassen](media/storage-simulate-failure-ragrs-account-app/figure1.png)

De Fiddler ScriptEditor wordt gestart en u ziet het bestand **SampleRules.js**. Dit bestand wordt gebruikt om Fiddler aan te passen.

Plak het volgende codevoorbeeld in `OnBeforeResponse` `STORAGEACCOUNTNAME` de functie en vervang de naam van uw opslagaccount. Afhankelijk van het voorbeeld moet u `HelloWorld` mogelijk ook worden vervangen door de naam `sampleFile`van het testbestand (of een voorvoegsel zoals) dat wordt gedownload. De nieuwe code wordt becommentarieerd om ervoor te zorgen dat deze niet onmiddellijk wordt uitgevoerd.

Selecteer als u klaar bent **File** en **Save** om uw wijzigingen op te slaan. Laat het ScriptEditor-venster open voor gebruik in de volgende stappen.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![De aangepaste regel plakken](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="start-and-pause-the-application"></a>De toepassing starten en onderbreken

Gebruik de instructies in de [vorige zelfstudie][previous-tutorial] om het voorbeeld te starten en het testbestand te downloaden, om te bevestigen dat het afkomstig is van primaire opslag. Afhankelijk van uw doelplatform u het voorbeeld vervolgens handmatig onderbreken of bij een prompt wachten.

### <a name="simulate-failure"></a>Fout simuleren

Terwijl de toepassing is onderbroken, schakelt u terug naar Fiddler `OnBeforeResponse` en geeft u geen commentaar op de aangepaste regel die u in de functie hebt opgeslagen. Zorg ervoor dat u **Bestand** en **Opslaan selecteert** om de wijzigingen op te slaan, zodat de regel van kracht wordt. Deze code zoekt naar aanvragen voor het RA-GRS-opslagaccount en als het pad de `503 - Service Unavailable`naam van het voorbeeldbestand bevat, retourneert u een antwoordcode van .

In het venster met het lopende voorbeeld hervat u de toepassing of drukt u op de juiste toets om het voorbeeldbestand te downloaden en te bevestigen dat het afkomstig is van secundaire opslag. U het voorbeeld vervolgens opnieuw onderbreken of bij de prompt wachten.

### <a name="simulate-primary-endpoint-restoration"></a>Herstel van het primaire eindpunt simuleren

Verwijder of reageer in Fiddler opnieuw op de aangepaste regel. Selecteer **Bestand** en **Opslaan** om ervoor te zorgen dat de regel niet langer van kracht is.

In het venster met het lopende voorbeeld hervat u de toepassing of drukt u op de juiste toets om het voorbeeldbestand te downloaden en te bevestigen dat het opnieuw afkomstig is van primaire opslag. U het monster vervolgens verlaten.

## <a name="next-steps"></a>Volgende stappen

In deel twee van de serie hebt u geleerd over het simuleren van een fout om geografisch redundante opslag met leestoegang te testen.

Lees het volgende artikel voor meer informatie over de werking van RA-GRS-opslag en de bijbehorende risico's:

> [!div class="nextstepaction"]
> [HA-apps met RA-GRS ontwerpen](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
