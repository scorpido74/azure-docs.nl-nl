---
title: MongoDB installeren op een Windows-VM in azure
description: Meer informatie over het installeren van MongoDB op een virtuele Azure-machine met Windows Server 2012 R2 die is gemaakt met het Resource Manager-implementatie model.
documentationcenter: ''
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: how-to
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: a5ba7d7fce3f3eabd223956ca8d9cc824fbd0c5f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81869454"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>MongoDB installeren en configureren op een Windows-VM in azure
[MongoDb](https://www.mongodb.org) is een populaire open-source NoSQL-data base met hoge prestaties. Dit artikel helpt u bij het installeren en configureren van MongoDB op een virtuele machine met Windows Server 2016 (VM) in Azure. U kunt [MongoDb ook installeren op een virtuele Linux-machine in azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Vereisten
Voordat u MongoDB installeert en configureert, moet u een virtuele machine maken en een gegevens schijf in het ideale geval toevoegen. Raadpleeg de volgende artikelen om een virtuele machine te maken en een gegevens schijf toe te voegen:

* Maak een Windows Server-VM met behulp van [de Azure Portal](quick-create-portal.md) of [Azure PowerShell](quick-create-powershell.md).
* Een gegevens schijf koppelen aan een virtuele Windows Server-machine met behulp van [de Azure Portal](attach-managed-disk-portal.md) of [Azure PowerShell](attach-disk-ps.md).

Als u MongoDB wilt installeren en configureren, [meldt u zich aan bij de Windows Server-VM](connect-logon.md) met behulp van extern bureaublad.

## <a name="install-mongodb"></a>MongoDB installeren
> [!IMPORTANT]
> MongoDB-beveiligings functies, zoals verificatie en IP-adres binding, zijn standaard niet ingeschakeld. Beveiligings functies moeten worden ingeschakeld voordat u MongoDB implementeert in een productie omgeving. Zie [MongoDb-beveiliging en-verificatie](https://www.mongodb.org/display/DOCS/Security+and+Authentication)voor meer informatie.


1. Nadat u verbinding hebt gemaakt met uw virtuele machine met behulp van Extern bureaublad, opent u Internet Explorer via de taak balk.
2. Selecteer **aanbevolen beveiligings-, privacy-en compatibiliteits instellingen gebruiken** wanneer Internet Explorer voor het eerst wordt geopend en klik op **OK**.
3. Verbeterde beveiliging van Internet Explorer is standaard ingeschakeld. Voeg de MongoDB-website toe aan de lijst met toegestane sites:
   
   * Selecteer het pictogram **extra** in de rechter bovenhoek.
   * Selecteer in **Internet opties**het tabblad **beveiliging** en selecteer vervolgens het pictogram **vertrouwde sites** .
   * Klik op de knop **sites** . Voeg *https:// \* . MongoDb.com* toe aan de lijst met vertrouwde sites en sluit het dialoog venster.
     
     ![Beveiligings instellingen voor Internet Explorer configureren](./media/install-mongodb/configure-internet-explorer-security.png)
4. Blader naar de pagina met [MongoDb-down loads](https://www.mongodb.com/downloads) ( https://www.mongodb.com/downloads) .
5. Als dat nodig is, selecteert u de editie van de **community-server** en selecteert u vervolgens de nieuwste stabiele release voor*Windows Server 2008 R2 64-bits en hoger*. Als u het installatie programma wilt downloaden, klikt u op **downloaden (MSI)**.
   
    ![MongoDB-installatie programma downloaden](./media/install-mongodb/download-mongodb.png)
   
    Voer het installatie programma uit nadat het downloaden is voltooid.
6. Lees en accepteer de gebruiksrecht overeenkomst. Wanneer u hierom wordt gevraagd, selecteert u **volledige** installatie.
7. Desgewenst kunt u ervoor kiezen om ook een kompas, een grafische interface voor MongoDB, te installeren.
8. Klik in het laatste scherm op **installeren**.

## <a name="configure-the-vm-and-mongodb"></a>De VM en MongoDB configureren
1. De padvariabelen worden niet bijgewerkt door het MongoDB-installatie programma. Zonder de `bin` locatie MongoDb in uw padvariabele moet u het volledige pad opgeven telkens wanneer u een MongoDb-uitvoerbaar bestand gebruikt. De locatie toevoegen aan de variabele pad:
   
   * Klik met de rechter muisknop op het menu **Start** en selecteer **systeem**.
   * Klik op **geavanceerde systeem instellingen**en klik vervolgens op **omgevings variabelen**.
   * Selecteer **pad**onder **systeem variabelen**en klik vervolgens op **bewerken**.
     
     ![Padvariabelen configureren](./media/install-mongodb/configure-path-variables.png)
     
     Voeg het pad naar de map MongoDB toe `bin` . MongoDB wordt doorgaans geïnstalleerd in *C:\Program Files\MongoDB*. Controleer het installatiepad op de virtuele machine. In het volgende voor beeld wordt de standaard installatie locatie voor MongoDB toegevoegd aan de `PATH` variabele:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Zorg ervoor dat u de voorloop punt komma ( `;` ) toevoegt om aan te geven dat u een locatie aan uw `PATH` variabele toevoegt.

2. Maak MongoDB-gegevens en-logboek mappen op uw gegevens schijf. Selecteer in het menu **Start** de **opdracht prompt**. De volgende voor beelden maken de mappen op station F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Start een MongoDB-exemplaar met de volgende opdracht, waarbij u het pad naar uw gegevens en logboek directory's dienovereenkomstig kunt aanpassen:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Het kan enkele minuten duren voordat MongoDB de logboek bestanden toe te wijzen en te Luis teren naar verbindingen. Alle logboek berichten worden omgeleid naar het *F:\MongoLogs\mongolog.log* -bestand wanneer de `mongod.exe` server logboek bestanden start en toewijst.
   
   > [!NOTE]
   > De opdracht prompt blijft gericht op deze taak terwijl uw MongoDB-exemplaar wordt uitgevoerd. Laat het opdracht prompt venster geopend om door te gaan met het uitvoeren van MongoDB. Of installeer MongoDB als service, zoals wordt beschreven in de volgende stap.

4. Installeer de as a service voor een krachtigere MongoDB-ervaring `mongod.exe` . Als u een service maakt, hoeft u geen opdracht prompt te laten uitvoeren telkens wanneer u MongoDB wilt gebruiken. Maak de service als volgt, waarbij u het pad naar uw gegevens en logboek directory's dienovereenkomstig kunt aanpassen:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Met de voor gaande opdracht maakt u een service met de naam MongoDB, met een beschrijving van ' Mongo DB '. De volgende para meters zijn ook opgegeven:
   
   * `--dbpath`Met de optie geeft u de locatie van de gegevensdirectory op.
   * De `--logpath` optie moet worden gebruikt om een logboek bestand op te geven, omdat de actieve service geen opdracht venster heeft om uitvoer weer te geven.
   * De `--logappend` optie geeft aan dat het opnieuw opstarten van de service ertoe leidt dat uitvoer wordt toegevoegd aan het bestaande logboek bestand.
   
   Als u de MongoDB-service wilt starten, voert u de volgende opdracht uit:
   
    ```
    net start MongoDB
    ```
   
    Zie [Configure a Windows service for MongoDb](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service)(Engelstalig) voor meer informatie over het maken van de MongoDb-service.

## <a name="test-the-mongodb-instance"></a>Het MongoDB-exemplaar testen
Wanneer MongoDB wordt uitgevoerd als één exemplaar of als een service wordt geïnstalleerd, kunt u nu beginnen met het maken en gebruiken van uw data bases. Als u de MongoDB-beheer shell wilt starten, opent u een ander opdracht prompt venster vanuit het menu **Start** en voert u de volgende opdracht in:

```
mongo  
```

U kunt de data bases weer geven met de `db` opdracht. Voeg de volgende gegevens in:

```
db.foo.insert( { a : 1 } )
```

Zoek gegevens als volgt:

```
db.foo.find()
```

De uitvoer lijkt op die in het volgende voorbeeld:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Sluit de- `mongo` console als volgt af:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Firewall-en netwerk beveiligings groeps regels configureren
Nu MongoDB is geïnstalleerd en wordt uitgevoerd, opent u een poort in Windows Firewall zodat u extern verbinding kunt maken met MongoDB. Als u een nieuwe regel voor binnenkomende verbindingen wilt maken om TCP-poort 27017 toe te staan, opent u een Power shell-prompt voor beheer en voert u de volgende opdracht in:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

U kunt de regel ook maken met behulp van het hulp programma **Windows Firewall met geavanceerde beveiliging voor** grafische beheer. Maak een nieuwe regel voor binnenkomende verbindingen om TCP-poort 27017 toe te staan.

Als dat nodig is, maakt u een regel voor de netwerk beveiligings groep om toegang tot MongoDB toe te staan van buiten het bestaande subnet van het virtuele Azure-netwerk. U kunt de regels voor de netwerk beveiligings groep maken met behulp van de [Azure Portal](nsg-quickstart-portal.md) of [Azure PowerShell](nsg-quickstart-powershell.md). Net als bij de Windows Firewall regels, staat u TCP-poort 27017 toe aan de virtuele netwerk interface van uw MongoDB-VM.

> [!NOTE]
> TCP-poort 27017 is de standaard poort die wordt gebruikt door MongoDB. U kunt deze poort wijzigen door de `--port` para meter te gebruiken wanneer u `mongod.exe` hand matig of vanuit een service begint. Als u de poort wijzigt, moet u ervoor zorgen dat u de Windows Firewall en de regels voor de netwerk beveiligings groep in de voor gaande stappen bijwerkt.


## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u geleerd hoe u MongoDB kunt installeren en configureren op uw Windows-VM. U hebt nu toegang tot MongoDB op uw Windows-VM door de geavanceerde onderwerpen in de [MongoDb-documentatie](https://docs.mongodb.com/manual/)te volgen.

