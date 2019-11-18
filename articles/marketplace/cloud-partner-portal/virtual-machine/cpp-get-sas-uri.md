---
title: URI voor Shared Access-hand tekening ophalen voor uw op Microsoft Azure gebaseerde VM-installatie kopie | Azure Marketplace
description: Hierin wordt uitgelegd hoe u de SAS-URI (Shared Access Signature) voor uw VM-installatie kopie ophaalt.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: cb6f1772c7c6f9abd268a8cb58550b253f095dbf
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132457"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>URI voor Shared Access-hand tekening ophalen voor uw VM-installatie kopie

Tijdens het publicatie proces moet u een Uniform Resource Identifier (URI) opgeven voor elke virtuele harde schijf (VHD) die aan uw Sku's is gekoppeld. Tijdens het certificeringsproces moet Microsoft toegang hebben tot deze VHD's. In dit artikel wordt uitgelegd hoe u een SAS-URI (Shared Access Signature) kunt genereren voor elke VHD. U voert deze URI in op het tabblad **sku's** in het Cloud Partner-Portal.

Bij het genereren van SAS-Uri's voor uw Vhd's, moet u voldoen aan de volgende vereisten:

- Alleen onbeheerde Vhd's worden ondersteund.
- `List`-en `Read`machtigingen zijn voldoende. Geef *geen* `Write`-of `Delete` toegang op.
- De duur voor toegang (*verval datum*) moet mini maal drie weken zijn vanaf het moment dat de SAS-URI wordt gemaakt.
- Om te beschermen tegen UTC-tijd variaties, stelt u de begin datum in op één dag voor de huidige datum. Als de huidige datum bijvoorbeeld 6 oktober 2014 is, selecteert u 10/5/2014.

## <a name="generate-the-sas-url"></a>De SAS-URL genereren

De SAS-URL kan op twee algemene manieren worden gegenereerd met behulp van de volgende hulpprogram ma's:

-   Micro soft Storage Explorer-grafisch hulp programma beschikbaar voor Windows, macOS en Linux
-   Microsoft Azure CLI: aanbevolen voor niet-Windows OSs-en geautomatiseerde of continue integratie omgevingen


### <a name="azure-cli"></a>Azure-CLI

Gebruik de volgende stappen om een SAS-URI te genereren met Azure CLI.

1. Down load en installeer de [Microsoft Azure cli](https://azure.microsoft.com/documentation/articles/xplat-cli-install/).  Er zijn versies beschikbaar voor Windows, macOS en verschillende distributies van Linux.
2. Maak een Power shell-bestand (`.ps1` bestands extensie), kopieer de volgende code en sla deze lokaal op.

   ``` powershell
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```

3. Bewerk het bestand om de volgende parameter waarden op te geven.  Datums moeten worden weer gegeven in de notatie voor UTC-datum/tijd, bijvoorbeeld `2016-10-25T00:00:00Z`.
   - `<account-name>`-uw Azure Storage-account naam
   - `<account-key>`-uw Azure Storage-account sleutel
   - `<vhd-name>`-de naam van uw VHD
   - Start datum van `<start-date>` machtiging voor toegang tot de virtuele harde schijf. Geef een datum op van één dag voor de huidige datum.
   - verval datum van `<expiry-date>` machtiging voor toegang tot de VHD.  Geef een datum op die ten minste drie weken na de huidige datum valt.

   In het volgende voor beeld worden de juiste parameter waarden weer gegeven (op het moment van schrijven).

   ``` powershell
       az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```

4. Sla de wijzigingen in dit Power shell-script op.
5. Voer dit script uit met beheerders bevoegdheden om een SAS- *Connection String* te genereren voor toegang op container niveau.  U kunt twee basis benaderingen gebruiken:
   - Voer het script uit vanaf de-console.  Schrijf bijvoorbeeld in Windows op het script en selecteer **als administrator uitvoeren**.
   - Voer het script uit vanuit een Power shell-script editor, zoals de [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise), met behulp van Administrator bevoegdheden.
     Hieronder ziet u een SAS-connection string die in deze editor wordt gegenereerd.

     ![Generatie van SAS-URI in Power shell ISE](./media/publishvm_032.png)

6. Kopieer de resulterende SAS-connection string en sla deze op in een tekst bestand op een veilige locatie.  U gaat deze teken reeks bewerken om de bijbehorende VHD-locatie gegevens aan toe te voegen om de definitieve SAS-URI te maken.
7. Navigeer in de Azure Portal naar de Blob-opslag met de VHD die is gekoppeld aan de zojuist gegenereerde URI.
8. Kopieer de URL-waarde van het **BLOB service-eind punt**, zoals hieronder wordt weer gegeven.

    ![Blob service-eind punt in Azure Portal](./media/publishvm_033.png)

9. Bewerk het tekst bestand met de SAS-connection string uit stap 6.  U maakt de volledige SAS-URI met de volgende indeling:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Als de naam van de VDH bijvoorbeeld is `TestRGVM2.vhd`, zou de resulterende SAS-URI er als volgt uitziet:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Herhaal deze stappen voor elke VHD in de Sku's die u wilt publiceren.


### <a name="microsoft-storage-explorer"></a>Micro soft Storage Explorer

Gebruik de volgende stappen om een SAS-URI te genereren met de Microsoft Azure Storage Explorer.

1. De [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) downloaden en installeren.
2. Open de Verkenner en klik in de linker menu balk op het pictogram **account toevoegen** .  Het dialoog venster **verbinding maken met Azure Storage** wordt weer gegeven.
3. Selecteer **Een Azure-account toevoegen** en klik op **Aanmelden**.  Ga door met de vereiste stappen om u aan te melden bij uw Azure-account.
4. Ga in het linkerdeel venster van de **Verkenner** naar uw **opslag accounts** en vouw dit knoop punt uit.
5. Klik met de rechter muisknop op uw VHD en selecteer **hand tekening voor gedeelde toegang ophalen** in het context menu.

    ![SAS-item in azure Verkenner ophalen](./media/publishvm_034.png)

6. Het dialoog venster **Shared Access Signature** wordt weer gegeven. Voer waarden in voor de volgende velden:
   - **Start tijd** -start datum van machtiging voor toegang tot de virtuele harde schijf. Geef een datum op die één dag voor de huidige datum valt.
   - **Verloop tijd** -verval datum van machtiging voor toegang tot de VHD.  Geef een datum op die ten minste drie weken na de huidige datum valt.
   - **Machtigingen** : selecteer de `Read` en `List` machtigingen.

     ![Het dialoog venster SAS in azure Explorer](./media/publishvm_035.png)

7. Klik op **maken** om de gekoppelde SAS-URI voor deze VHD te maken.  In het dialoog venster worden nu Details over deze bewerking weer gegeven.
8. Kopieer de **URL** -waarde en sla deze op in een tekst bestand op een veilige locatie.

    ![SAS URI maken in azure Verkenner](./media/publishvm_036.png)

    Deze gegenereerde SAS-URL is voor toegang op container niveau.  Als u deze specifiek wilt maken, moet u de naam van de bijbehorende VHD toevoegen.

9. Bewerk het tekst bestand. Voeg uw VHD-naam toe na de `vhds` teken reeks in de SAS-URL (Voeg een voorloop back slash toe).  De uiteindelijke SAS-URI moet de volgende indeling hebben:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Als de naam van de VDH bijvoorbeeld is `TestRGVM2.vhd`, zou de resulterende SAS-URI er als volgt uitziet:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Herhaal deze stappen voor elke VHD in de Sku's die u wilt publiceren.


## <a name="verify-the-sas-uri"></a>De SAS-URI controleren

Controleer en controleer elke gegenereerde SAS-URI met behulp van de volgende controle lijst.  Controleer het volgende:
- De URI heeft de volgende vorm: `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
- De URI bevat de bestands naam van de VHD-installatie kopie, inclusief de bestands extensie. VHD.
- `sp=rl` wordt weer gegeven in de richting van het midden van de URI. Deze teken reeks geeft aan dat `Read` en `List` toegang is opgegeven.
- Na dat punt wordt `sr=c` ook weer gegeven. Deze teken reeks geeft aan dat toegang op container niveau is opgegeven.
- Kopieer en plak de URI in een browser om te beginnen met het downloaden van de gekoppelde blob.  (U kunt de bewerking annuleren voordat de down load is voltooid.)


## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt met het genereren van een SAS-URI, raadpleeg dan [veelvoorkomende problemen met SAS-url's](./cpp-common-sas-url-issues.md).  Sla anders de SAS-URI (s) op een veilige locatie op voor later gebruik. Het is vereist om [uw VM-aanbieding te publiceren](./cpp-publish-offer.md) in de Cloud Partner-Portal.
