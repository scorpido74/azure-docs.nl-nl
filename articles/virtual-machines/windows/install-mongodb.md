---
title: MongoDB installeren op een Windows VM in Azure
description: Meer informatie over het installeren van MongoDB op een Azure VM met Windows Server 2012 R2 die is gemaakt met het implementatiemodel Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 37c1b58d364e7eadb33803ce7eac1f2b956ec1b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038553"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>MongoDB installeren en configureren op een Windows VM in Azure
[MongoDB](https://www.mongodb.org) is een populaire open-source, high-performance NoSQL database. In dit artikel wordt u door het installeren en configureren van MongoDB op een virtuele machine (VM) van Windows Server 2016 in Azure geloodst. U [MongoDB ook installeren op een Linux-VM in Azure.](../linux/install-mongodb.md)

## <a name="prerequisites"></a>Vereisten
Voordat u MongoDB installeert en configureert, moet u een VM maken en idealiter een gegevensschijf toevoegen. Zie de volgende artikelen om een VM te maken en een gegevensschijf toe te voegen:

* Maak een Windows Server VM met [de Azure-portal](quick-create-portal.md) of [Azure PowerShell](quick-create-powershell.md).
* Koppel een gegevensschijf aan een Windows Server-vm met behulp van [de Azure-portal](attach-managed-disk-portal.md) of [Azure PowerShell](attach-disk-ps.md).

Als u MongoDB wilt installeren en configureren, [meldt u zich aan bij uw Windows Server VM](connect-logon.md) met Extern bureaublad.

## <a name="install-mongodb"></a>MongoDB installeren
> [!IMPORTANT]
> MongoDB-beveiligingsfuncties, zoals verificatie en IP-adresbinding, zijn standaard niet ingeschakeld. Beveiligingsfuncties moeten worden ingeschakeld voordat MongoDB wordt geïmplementeerd in een productieomgeving. Zie [MongoDB-beveiliging en -verificatie](https://www.mongodb.org/display/DOCS/Security+and+Authentication)voor meer informatie.


1. Nadat u verbinding hebt gemaakt met uw virtuele machine met Extern bureaublad, opent u Internet Explorer vanaf de taakbalk.
2. Selecteer **Aanbevolen beveiligings-, privacy- en compatibiliteitsinstellingen gebruiken** wanneer Internet Explorer voor het eerst wordt geopend en klik op **OK**.
3. De verbeterde beveiligingsconfiguratie van Internet Explorer is standaard ingeschakeld. Voeg de MongoDB-website toe aan de lijst met toegestane sites:
   
   * Selecteer het pictogram **Gereedschappen** in de rechterbovenhoek.
   * Selecteer **in internetopties**het tabblad **Beveiliging** en selecteer vervolgens het pictogram **Vertrouwde sites.**
   * Klik op de knop **Sites.** Voeg *\*https:// .mongodb.com* toe aan de lijst met vertrouwde sites en sluit het dialoogvenster.
     
     ![Beveiligingsinstellingen in Internet Explorer configureren](./media/install-mongodb/configure-internet-explorer-security.png)
4. Blader naar de [pagina MongoDB - Downloads](https://www.mongodb.com/downloads) (https://www.mongodb.com/downloads).
5. Selecteer indien nodig de **communityserver-editie** en selecteer vervolgens de nieuwste stabiele versie voor*Windows Server 2008 R2 64-bits en hoger*. Als u het installatieprogramma wilt downloaden, klikt u op **DOWNLOADEN (msi).**
   
    ![MongoDB-installer downloaden](./media/install-mongodb/download-mongodb.png)
   
    Voer het installatieprogramma uit nadat de download is voltooid.
6. Lees en accepteer de licentieovereenkomst. Wanneer u wordt gevraagd, selecteert u De installatie **voltooien.**
7. Indien gewenst u ervoor kiezen om ook Compass te installeren, een grafische interface voor MongoDB.
8. Klik op het laatste scherm op **Installeren**.

## <a name="configure-the-vm-and-mongodb"></a>De VM en MongoDB configureren
1. De padvariabelen worden niet bijgewerkt door de MongoDB-installateur. Zonder de MongoDB-locatie `bin` in uw padvariabele moet u het volledige pad opgeven telkens wanneer u een MongoDB-uitvoerbaar gebruikt. Ga als volgt te werk om de locatie toe te voegen aan uw padvariabele:
   
   * Klik met de rechtermuisknop op het menu **Start** en selecteer **Systeem**.
   * Klik **op Geavanceerde systeeminstellingen**en klik vervolgens op **Omgevingsvariabelen**.
   * Selecteer **Onder Systeemvariabelen**de optie **Pad**en klik op **Bewerken**.
     
     ![PATH-variabelen configureren](./media/install-mongodb/configure-path-variables.png)
     
     Voeg het pad toe aan `bin` de map MongoDB. MongoDB is meestal geïnstalleerd in *C:\Program Files\MongoDB*. Controleer het installatiepad op uw vm. In het volgende voorbeeld wordt de standaard `PATH` locatie voor de installatie van MongoDB aan de variabele toegevoegd:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Zorg ervoor dat u de`;`leidende puntkomma () toevoegt `PATH` om aan te geven dat u een locatie toevoegt aan uw variabele.

2. MongoDB-gegevens maken en mappen registreren op uw gegevensschijf. Selecteer **Opdrachtprompt**in het menu **Start** . De volgende voorbeelden maken de mappen op station F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Start een MongoDB-exemplaar met de volgende opdracht, waarbij het pad wordt aangepast aan uw gegevens en mappen dienovereenkomstig worden aangemeld:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Het kan enkele minuten duren voordat MongoDB de dagboekbestanden toewijst en naar verbindingen begint te luisteren. Alle logboekberichten worden doorgestuurd naar het bestand *F:\MongoLogs\mongolog.log* wanneer `mongod.exe` de server begint en dagboekbestanden toewijst.
   
   > [!NOTE]
   > De opdrachtprompt blijft gericht op deze taak terwijl uw MongoDB-exemplaar wordt uitgevoerd. Laat het opdrachtpromptvenster open om MongoDB te blijven uitvoeren. Of installeer MongoDB als service, zoals beschreven in de volgende stap.

4. Voor een robuustere MongoDB-ervaring installeert u de `mongod.exe` as a-service. Als u een service maakt, hoeft u geen opdrachtprompt te laten uitvoeren wanneer u MongoDB wilt gebruiken. Maak de service als volgt, waarbij het pad aan uw gegevens wordt aangepast en mappen dienovereenkomstig worden aangemeld:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Met de vorige opdracht wordt een service met de naam MongoDB gemaakt, met een beschrijving van "Mongo DB". De volgende parameters zijn ook opgegeven:
   
   * De `--dbpath` optie geeft de locatie van de gegevensmap aan.
   * De `--logpath` optie moet worden gebruikt om een logboekbestand op te geven, omdat de lopende service geen opdrachtvenster heeft om de uitvoer weer te geven.
   * De `--logappend` optie geeft aan dat een herstart van de service ervoor zorgt dat de uitvoer wordt toegevoegd aan het bestaande logboekbestand.
   
   Voer de volgende opdracht uit om de MongoDB-service te starten:
   
    ```
    net start MongoDB
    ```
   
    Zie [Een Windows-service configureren voor MongoDB voor](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service)meer informatie over het maken van de MongoDB-service.

## <a name="test-the-mongodb-instance"></a>Test de MongoDB-instantie
Als MongoDB als één instantie wordt uitgevoerd of als service is geïnstalleerd, u nu beginnen met het maken en gebruiken van uw databases. Als u de beheershell MongoDB wilt starten, opent u een ander opdrachtpromptvenster in het menu **Start** en voert u de volgende opdracht in:

```
mongo  
```

U de databases `db` met de opdracht weergeven. Voeg als volgt enkele gegevens in:

```
db.foo.insert( { a : 1 } )
```

Zoek als volgt naar gegevens:

```
db.foo.find()
```

De uitvoer lijkt op die in het volgende voorbeeld:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Verlaat `mongo` de console als volgt:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Regels voor firewall- en netwerkbeveiligingsgroepen configureren
Nu MongoDB is geïnstalleerd en uitgevoerd, opent u een poort in Windows Firewall, zodat u op afstand verbinding maken met MongoDB. Als u een nieuwe inkomende regel wilt maken om TCP-poort 27017 toe te staan, opent u een administratieve PowerShell-prompt en voert u de volgende opdracht in:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

U de regel ook maken met behulp van de Windows Firewall met het grafische beheerprogramma **Geavanceerde beveiliging.** Maak een nieuwe inkomende regel om TCP-poort 27017 toe te staan.

Maak indien nodig een regel voor netwerkbeveiliging om toegang tot MongoDB toe te staan van buiten het bestaande Azure virtual network subnet. U de regels voor netwerkbeveiligingmaken met behulp van de [Azure-portal](nsg-quickstart-portal.md) of [Azure PowerShell](nsg-quickstart-powershell.md). Net als bij de Windows Firewall-regels, tcp-poort 27017 toestaan om de virtuele netwerkinterface van uw MongoDB VM.

> [!NOTE]
> TCP-poort 27017 is de standaardpoort die door MongoDB wordt gebruikt. U deze poort `--port` wijzigen door `mongod.exe` de parameter te gebruiken wanneer u handmatig of vanuit een service start. Als u de poort wijzigt, moet u de regels voor Windows Firewall en netwerkbeveiliging in de voorgaande stappen bijwerken.


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u MongoDB op uw Windows VM installeren en configureren. U nu toegang krijgen tot MongoDB op uw Windows VM, door de geavanceerde onderwerpen in de [MongoDB-documentatie te](https://docs.mongodb.com/manual/)volgen.

