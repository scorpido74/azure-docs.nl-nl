---
title: Zelf studie-een fout simuleren bij het lezen van gegevens uit de primaire regio
titleSuffix: Azure Storage
description: Een fout simuleren bij het lezen van gegevens uit de primaire regio wanneer het opslag account is geconfigureerd voor geo-zone-redundante opslag met lees toegang (RA-GZRS). Nadat de fout is opgetreden, leest u de gegevens uit de secundaire regio.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.openlocfilehash: f7a792eea28c6a6d05c4f295241291fdf2449467
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82859037"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Zelf studie: een fout simuleren bij het lezen van gegevens uit de primaire regio

Deze zelfstudie is deel twee van een serie. Hierin leert u wat de voor delen zijn van [geografisch redundante opslag met lees toegang](../common/storage-redundancy.md) (RA-GZRS) door een fout te simuleren.

Als u een fout wilt simuleren, kunt u een [statische route ring](#simulate-a-failure-with-an-invalid-static-route) of [Fiddler](#simulate-a-failure-with-fiddler)gebruiken. Met beide methoden kunt u storingen simuleren voor aanvragen naar het primaire eind punt van uw opslag account met [geografisch redundante Lees toegang](../common/storage-redundancy.md) (RA-GZRS), waardoor de toepassing in plaats daarvan uit het secundaire eind punt kan lezen.

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

In deel twee van de serie leert u het volgende:

> [!div class="checklist"]
> * De toepassing uitvoeren en onderbreken
> * Een fout simuleren met [een ongeldige statische route](#simulate-a-failure-with-an-invalid-static-route) of [Fiddler](#simulate-a-failure-with-fiddler)
> * Herstel van het primaire eindpunt simuleren

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet u de vorige zelf studie volt ooien: [uw toepassings gegevens Maxi maal beschikbaar maken met Azure Storage][previous-tutorial].

Als u een fout met statische route ring wilt simuleren, gebruikt u een opdracht prompt met verhoogde bevoegdheden.

Als u een fout wilt simuleren met behulp van Fiddler, downloadt en [installeert u Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Een fout simuleren met een ongeldige statische route

U kunt een ongeldige statische route maken voor alle aanvragen naar het primaire eind punt van uw opslag account met [geografisch redundant met lees toegang](../common/storage-redundancy.md) (RA-GZRS). In deze zelfstudie wordt de lokale host gebruikt als de gateway voor routeringsaanvragen voor de opslagaccount. Het gebruik van de lokale host als de gateway zorgt ervoor dat alle aanvragen naar het primaire eindpunt van uw opslagaccount in een lusvorm terugkeren naar de host, wat vervolgens tot een fout leidt. Voer de volgende stappen uit om een fout en herstel van het primaire eindpunt met een ongeldige statische route te simuleren.

### <a name="start-and-pause-the-application"></a>De toepassing starten en onderbreken

Volg de instructies in de [vorige zelf studie][previous-tutorial] om het voor beeld te starten en het test bestand te downloaden en te bevestigen dat het afkomstig is van de primaire opslag. Afhankelijk van uw doel platform kunt u het voor beeld hand matig onderbreken of wachten op een prompt.

### <a name="simulate-failure"></a>Fout simuleren

Wanneer de toepassing is onderbroken, opent u een opdracht prompt in Windows als beheerder of voert u de terminal uit als root op Linux.

Haal informatie op over het primaire eindpunt domein van het opslag account door de volgende opdracht in te voeren in een opdracht `STORAGEACCOUNTNAME` prompt of Terminal, waarbij u vervangt door de naam van uw opslag account.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Kopieer het IP-adres van uw opslagaccount naar een teksteditor voor later gebruik.

Als u het IP-adres van uw lokale host wilt ophalen, typt u `ipconfig` in de Windows-opdrachtprompt of `ifconfig` in de Linux-terminal.

Als u een statische route voor een doelhost wilt toevoegen, typt u de volgende opdracht in een Windows-opdracht prompt of Linux `<destination_ip>` -Terminal, vervangen door het IP `<gateway_ip>` -adres van uw opslag account en met het IP-adres van uw lokale host.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

In het venster met het uitgevoerde voor beeld hervat u de toepassing of drukt u op de juiste sleutel om het voorbeeld bestand te downloaden en te bevestigen dat het afkomstig is van de secundaire opslag. Vervolgens kunt u het voor beeld opnieuw onderbreken of wachten op de prompt.

### <a name="simulate-primary-endpoint-restoration"></a>Herstel van het primaire eindpunt simuleren

Als u wilt simuleren dat het primaire eind punt weer werkt, verwijdert u de ongeldige statische route uit de routerings tabel. Hiermee worden alle aanvragen naar het primaire eindpunt via de standaardgateway gerouteerd. Typ de volgende opdracht in een Windows-opdracht prompt of Linux-Terminal.

#### <a name="linux"></a>Linux

```bash
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```console
route delete <destination_ip>
```

U kunt de toepassing vervolgens hervatten of op de juiste toets drukken om het voorbeeld bestand opnieuw te downloaden, maar deze keer dat het opnieuw afkomstig is van de primaire opslag.

## <a name="simulate-a-failure-with-fiddler"></a>Een fout simuleren met Fiddler

Als u fouten wilt simuleren met Fiddler, injecteert u een mislukte reactie op aanvragen naar het primaire eind punt van uw RA-GZRS-opslag account.

In de volgende gedeelten wordt uitgelegd hoe u een fout simuleert en het primaire eindpunt herstelt met Fiddler.

### <a name="launch-fiddler"></a>Start Fiddler

Open Fiddler en selecteer **Rules** en **Customize Rules**.

![Fiddler-regels aanpassen](media/simulate-primary-region-failure/figure1.png)

De Fiddler ScriptEditor wordt gestart en u ziet het bestand **SampleRules.js**. Dit bestand wordt gebruikt om Fiddler aan te passen.

Plak het volgende code voorbeeld in de `OnBeforeResponse` functie, waarbij `STORAGEACCOUNTNAME` u vervangt door de naam van uw opslag account. Afhankelijk van het voor beeld moet u mogelijk ook vervangen `HelloWorld` door de naam van het test bestand (of een voor voegsel zoals `sampleFile`) dat wordt gedownload. De nieuwe code wordt op opmerkingen uitgevoerd om ervoor te zorgen dat deze niet onmiddellijk wordt uitgevoerd.

Selecteer als u klaar bent **File** en **Save** om uw wijzigingen op te slaan. Sluit het venster ScriptEditor open voor gebruik in de volgende stappen.

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

![De aangepaste regel plakken](media/simulate-primary-region-failure/figure2.png)

### <a name="start-and-pause-the-application"></a>De toepassing starten en onderbreken

Volg de instructies in de [vorige zelf studie][previous-tutorial] om het voor beeld te starten en het test bestand te downloaden en te bevestigen dat het afkomstig is van de primaire opslag. Afhankelijk van uw doel platform kunt u het voor beeld hand matig onderbreken of wachten op een prompt.

### <a name="simulate-failure"></a>Fout simuleren

Wanneer de toepassing is onderbroken, keert u terug naar Fiddler en geeft u een opmerking op voor de aangepaste `OnBeforeResponse` regel die u hebt opgeslagen in de functie. Zorg ervoor dat u **bestand** en **Opslaan** selecteert om uw wijzigingen op te slaan, zodat de regel van kracht wordt. Met deze code wordt gezocht naar aanvragen voor het RA-GZRS-opslag account. als het pad de naam van het voorbeeld bestand bevat, retourneert een antwoord `503 - Service Unavailable`code van.

In het venster met het uitgevoerde voor beeld hervat u de toepassing of drukt u op de juiste sleutel om het voorbeeld bestand te downloaden en te bevestigen dat het afkomstig is van de secundaire opslag. Vervolgens kunt u het voor beeld opnieuw onderbreken of wachten op de prompt.

### <a name="simulate-primary-endpoint-restoration"></a>Herstel van het primaire eindpunt simuleren

In Fiddler kunt u de aangepaste regel opnieuw verwijderen of opmerkingen toevoegen. Selecteer **bestand** en **Opslaan** om ervoor te zorgen dat de regel niet meer van kracht is.

In het venster met het uitgevoerde voor beeld hervat u de toepassing of drukt u op de juiste sleutel om het voorbeeld bestand te downloaden en te bevestigen dat het afkomstig is van de primaire opslag. Daarna kunt u het voor beeld afsluiten.

## <a name="next-steps"></a>Volgende stappen

In deel twee van de serie hebt u geleerd over het simuleren van een fout om geografisch redundante opslag met leestoegang te testen.

Lees het volgende artikel voor meer informatie over hoe RA-GZRS-opslag werkt, evenals de bijbehorende Risico's:

> [!div class="nextstepaction"]
> [HA-apps ontwerpen met RA-GZRS](../common/geo-redundant-design.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
