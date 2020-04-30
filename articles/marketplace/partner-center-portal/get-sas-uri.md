---
title: URI voor Shared Access-hand tekening ophalen voor uw VM-installatie kopie | Azure Marketplace
description: In dit artikel wordt uitgelegd hoe u de SAS-URI (Shared Access Signature) voor elke virtuele harde schijf (VHD) kunt ophalen.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: c226d35647e4a5a2b1d583dd6328bfb73dae2a1c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732644"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>URI voor Shared Access-hand tekening ophalen voor uw VM-installatie kopie

> [!IMPORTANT]
> We verplaatsen het beheer van uw Azure Virtual Machine-aanbiedingen van Cloud Partner-portal naar het partner centrum. Totdat uw aanbiedingen zijn gemigreerd, volgt u de instructies in de URI van de [gedeelde toegangs handtekening ophalen voor uw VM-installatie kopie](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) voor Cloud Partner-Portal voor het beheren van uw aanbiedingen.

In dit artikel wordt beschreven hoe u een SAS-URL (Shared Access Signature) voor elke virtuele harde schijf (VHD) genereert.

Tijdens het publicatie proces moet u een URI opgeven voor elke VHD die aan uw plannen is gekoppeld. Deze plannen werden voorheen Sku's of Stock Keeping Units genoemd. Tijdens het certificerings proces moet micro soft toegang hebben tot deze Vhd's. U voert deze URI in op het tabblad **plannen** in partner centrum.

Volg de volgende vereisten bij het genereren van SAS-Uri's voor uw Vhd's:

* Alleen onbeheerde Vhd's worden ondersteund.
* Alleen `List` en `Read` machtigingen zijn vereist. Geef geen toegang voor schrijven of verwijderen op.
* De duur voor toegang (verval datum) moet mini maal drie weken zijn vanaf het moment dat de SAS-URI wordt gemaakt.
* Als u wilt beveiligen tegen UTC-tijd wijzigingen, stelt u de begin datum in op één dag voor de huidige datum. Als de huidige datum bijvoorbeeld 6 oktober 2019 is, selecteert u 10/5/2019.

## <a name="generate-the-sas-address"></a>Het SAS-adres genereren

Er zijn twee algemene hulpprogram ma's voor het maken van een SAS-adres (URL):

* **Micro soft Storage Explorer** -grafisch hulp programma dat beschikbaar is voor Windows, MacOS en Linux.
* **Microsoft Azure cli** : aanbevolen voor niet-Windows-besturings systemen en geautomatiseerde of continue integratie omgevingen.

### <a name="use-microsoft-storage-explorer"></a>Micro soft Storage Explorer gebruiken

1. Down load en Installeer [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Open de Verkenner en selecteer **account toevoegen**in het menu links. Het dialoog venster **verbinding maken met Azure Storage** wordt weer gegeven.
3. Selecteer **een Azure-account toevoegen** en **Meld u aan**. Voer de vereiste stappen uit om u aan te melden bij uw Azure-account.
4. Ga in het linkerdeel**venster naar** uw **opslag accounts** en vouw dit knoop punt uit.
5. Klik met de rechter muisknop op uw VHD en selecteer vervolgens **hand tekening voor share toegang ophalen**.
6. Het dialoog venster **Shared Access Signature** wordt weer gegeven. Vul de volgende velden in:

    * **Begin tijd** – machtigings begindatum voor toegang tot de virtuele harde schijf. Geef een datum op die één dag voor de huidige datum valt.
    * **Verloop tijd** – verval datum van machtiging voor toegang tot de VHD. Geef een datum op die ten minste drie weken na de huidige datum ligt.
    * **Machtigingen** : Selecteer de machtigingen lezen en lijst.
    * **Container niveau** : Schakel het selectie vakje **GENEReer de URI op container niveau van de Shared Access-hand tekening genereren** in.

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Illustreert het dialoog venster Shared Access Signature":::

7. Selecteer **maken**om de bijbehorende SAS-URI voor deze VHD te maken. Het dialoog venster wordt vernieuwd en Details over deze bewerking worden weer gegeven.
8. Kopieer de **URI** en sla deze op in een tekst bestand op een veilige locatie.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Illustreert het Shared Access Signature vak Details":::

    Deze gegenereerde SAS-URI is voor toegang op container niveau. Als u deze specifiek wilt maken, bewerkt u het tekst bestand om de naam van de virtuele harde schijf toe te voegen (volgende stap).

9. Plaats de naam van de VHD achter de vhd's-teken reeks in de SAS-URI (neem een slash op). De uiteindelijke SAS-URI moet er als volgt uitzien:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Als de naam van de VDH bijvoorbeeld is, zou `TestRGVM2.vhd`de resulterende SAS-URI er als volgt uitziet:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Herhaal deze stappen voor elke VHD in de plannen die u wilt publiceren.

### <a name="using-azure-cli"></a>Azure CLI gebruiken

1. Down load en Installeer [Microsoft Azure cli](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Er zijn versies beschikbaar voor Windows, macOS en verschillende distributies van Linux.
2. Maak een Power shell-bestand (. ps1-bestands extensie), kopieer de volgende code en sla deze lokaal op.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Bewerk het bestand om de volgende parameter waarden te gebruiken. Geef datums op in UTC-datum notatie, `2020-04-01T00:00:00Z`zoals.

    * `<account-name>`: Uw Azure Storage-account naam
    * `<account-key>`: Uw Azure Storage-account sleutel
    * `<vhd-name>`: Uw VHD-naam
    * `<start-date>`-Begin datum machtiging voor toegang tot de VHD. Geef een datum op van één dag voor de huidige datum.
    * `<expiry-date>`– Verval datum van toestemming voor toegang tot de VHD. Geef een datum op die ten minste drie weken na de huidige datum valt.

    In dit voor beeld worden de juiste parameter waarden weer gegeven (op het moment van schrijven):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Sla de wijzigingen op.
5. Gebruik een van de volgende methoden om dit script uit te voeren met beheerders bevoegdheden voor het maken van een **SAS-Connection String** voor toegang op container niveau:

    * Voer het script uit vanaf de-console. Klik in Windows met de rechter muisknop op het script en selecteer **als administrator uitvoeren**.
    * Voer het script uit vanuit een Power shell-script editor, zoals [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). In dit scherm wordt het maken van een SAS-connection string in deze editor weer gegeven:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Illustreert het maken van een SAS-connection string met Windows PowerShell ISE":::

6. Kopieer de SAS-connection string en sla deze op in een tekst bestand op een veilige locatie. Bewerk deze teken reeks om de VHD-locatie gegevens toe te voegen om de definitieve SAS-URI te maken.
7. Ga in het Azure Portal naar de Blob-opslag met de VHD die is gekoppeld aan de nieuwe URI.
8. Kopieer de URL van het **BLOB service-eind punt**, zoals wordt weer gegeven in de volgende scherm afbeelding

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Illustreert het Blob service-eind punt":::

9. Bewerk het tekst bestand met de SAS-connection string uit stap 6. Maak de volledige SAS-URI met de volgende indeling:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Als de naam van de VHD bijvoorbeeld is `TestRGVM2.vhd`, wordt de SAS-URI:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Herhaal deze stappen voor elke VHD in de Sku's die u wilt publiceren.

## <a name="verify-the-sas-uri"></a>De SAS-URI controleren

Controleer elke gegenereerde SAS-URI door de volgende controle lijst te gebruiken om te controleren of:

* De URI ziet er als volgt uit:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* De URI bevat de bestands naam van de VHD-installatie kopie, inclusief de bestands extensie. VHD.
* `sp=rl`wordt weer gegeven in de buurt van de URI. Deze teken reeks geeft `Read` aan `List` dat en de toegang is opgegeven.
* Wanneer `sr=c` wordt weer gegeven, betekent dit dat toegang op container niveau is opgegeven.
* Kopieer en plak de URI in een browser om de BLOB te testen (u kunt de bewerking annuleren voordat de down load is voltooid).

## <a name="next-step"></a>Volgende stap

Zie [veelvoorkomende SAS-URL-problemen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues)als u problemen hebt met het maken van een SAS-URI. Sla anders de SAS-URI (s) op een veilige locatie op voor later gebruik. U hebt deze nodig voor het publiceren van uw VM-aanbieding in het partner centrum.

* [Een Azure Virtual Machine-aanbieding maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
