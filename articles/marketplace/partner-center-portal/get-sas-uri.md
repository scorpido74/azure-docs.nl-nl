---
title: Shared Access Signature-URI voor VM-installatie kopieën-Azure Marketplace
description: Genereer een SAS-URI (Shared Access Signature) voor uw virtuele harde schijven (VHD) in azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 07/29/2020
ms.openlocfilehash: 2bc129fc37347bd108ad62409490c5ce31b7728f
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87538928"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>URI voor Shared Access-hand tekening ophalen voor uw VM-installatie kopie

In dit artikel wordt beschreven hoe u een SAS-URL (Shared Access Signature) voor elke virtuele harde schijf (VHD) genereert.

Tijdens het publicatie proces moet u een URI opgeven voor elke VHD die is gekoppeld aan uw plannen (voorheen Sku's genoemd). Tijdens het certificerings proces moet micro soft toegang hebben tot deze Vhd's. U voert deze URI in op het tabblad **plannen** in partner centrum.

Volg de volgende vereisten bij het genereren van SAS-Uri's voor uw Vhd's:

* Alleen onbeheerde Vhd's worden ondersteund.
* Alleen `List` en `Read` machtigingen zijn vereist. Geef geen toegang voor schrijven of verwijderen op.
* De duur voor toegang (verval datum) moet mini maal drie weken zijn vanaf het moment dat de SAS-URI wordt gemaakt.
* Als u wilt beveiligen tegen UTC-tijd wijzigingen, stelt u de begin datum in op één dag voor de huidige datum. Als de huidige datum bijvoorbeeld 6 oktober 2019 is, selecteert u 10/5/2019.

## <a name="generate-the-sas-address"></a>Het SAS-adres genereren

Er zijn twee algemene hulpprogram ma's voor het maken van een SAS-adres (URL):

* **Microsoft Azure Storage Explorer** : grafisch hulp programma dat beschikbaar is in de Azure Portal.
* **Microsoft Azure cli** : aanbevolen voor niet-Windows-besturings systemen en geautomatiseerde of continue integratie omgevingen.

### <a name="use-microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer gebruiken

1. Ga naar uw opslag account in de Azure Portal.
2. Open in het deel venster Verkenner aan de linkerkant het hulp programma **Storage Explorer** (preview).
3. Klik met de rechter muisknop op uw VHD en selecteer vervolgens **Shared Access Signature ophalen**.
4. Het dialoog venster **Shared Access Signature** wordt weer gegeven. Vul de volgende velden in:

    * **Begin tijd** – machtigings begindatum voor toegang tot de virtuele harde schijf. Geef een datum op die één dag voor de huidige datum valt.
    * **Verloop tijd** – verval datum van machtiging voor toegang tot de VHD. Geef een datum op die ten minste drie weken na de huidige datum ligt.
    * **Machtigingen** : Selecteer de machtigingen lezen en lijst.
    * **Container niveau** : Schakel het selectie vakje **GENEReer de URI op container niveau van de Shared Access-hand tekening genereren** in.

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Illustreert het dialoog venster Shared Access Signature":::

5. Selecteer **maken**om de bijbehorende SAS-URI voor deze VHD te maken. Het dialoog venster wordt vernieuwd en Details over deze bewerking worden weer gegeven.
6. Kopieer de **URI** en sla deze op in een tekst bestand op een veilige locatie.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Illustreert het Shared Access Signature vak Details":::
7. Herhaal deze stappen voor elke VHD in de plannen die u wilt publiceren.

### <a name="using-azure-cli"></a>Azure CLI gebruiken

1. Down load en Installeer [Microsoft Azure cli](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Er zijn versies beschikbaar voor Windows, macOS en verschillende distributies van Linux.
2. Maak een Power shell-bestand (. ps1-bestands extensie), kopieer de volgende code en sla deze lokaal op.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Bewerk het bestand om de volgende parameter waarden te gebruiken. Geef datums op in UTC-datum notatie, zoals `2020-04-01T00:00:00Z` .

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

    Als de naam van de VHD bijvoorbeeld is `TestRGVM2.vhd` , wordt de SAS-URI:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Herhaal deze stappen voor elke VHD in de plannen die u wilt publiceren.

## <a name="verify-the-sas-uri"></a>De SAS-URI controleren

Controleer elke gegenereerde SAS-URI door de volgende controle lijst te gebruiken om te controleren of:

* De URI ziet er als volgt uit:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* De URI bevat de bestands naam van de VHD-installatie kopie, inclusief de bestands extensie. VHD.
* `sp=rl`wordt weer gegeven in de buurt van de URI. Deze teken reeks geeft aan dat `Read` en de `List` toegang is opgegeven.
* Wanneer `sr=c` wordt weer gegeven, betekent dit dat toegang op container niveau is opgegeven.
* Kopieer en plak de URI in een browser om de BLOB te testen (u kunt de bewerking annuleren voordat de down load is voltooid).

## <a name="next-step"></a>Volgende stap

Zie [veelvoorkomende SAS-URL-problemen](common-sas-uri-issues.md)als u problemen hebt met het maken van een SAS-URI. Sla anders de SAS-URI (s) op een veilige locatie op voor later gebruik. U hebt deze nodig voor het publiceren van uw VM-aanbieding in het partner centrum.

* [Een Azure virtual machine-aanbieding maken](azure-vm-create-offer.md)
