---
title: Een SAS-URI voor uw VM-installatie kopie ophalen-Azure Marketplace
description: Genereer een SAS-URI (Shared Access Signature) voor uw virtuele harde schijven (VHD) in azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a84f287c6303e093d68dd462ccc5cecc34b463cd
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144495"
---
# <a name="get-a-sas-uri-for-your-vm-image"></a>Een SAS-URI voor uw VM-installatie kopie ophalen

Tijdens het publicatie proces moet u een SAS-URI (Shared Access Signature) opgeven voor elke VHD die is gekoppeld aan uw plannen (voorheen Sku's genoemd). Tijdens het certificerings proces moet micro soft toegang hebben tot deze Vhd's. U voert deze URI in op het tabblad **plannen** in partner centrum.

Voor het genereren van SAS-Uri's voor uw Vhd's gelden de volgende vereisten:

- Ze bieden alleen ondersteuning voor onbeheerde Vhd's.
- Alleen lijst-en lees machtigingen zijn vereist. Geef geen toegang voor schrijven of verwijderen op.
- De duur voor toegang (verval datum) moet mini maal drie weken zijn vanaf het moment dat de SAS-URI wordt gemaakt.
- Als u wilt beveiligen tegen UTC-tijd wijzigingen, stelt u de begin datum in op één dag voor de huidige datum. Als de huidige datum bijvoorbeeld 16 juni 2020 is, selecteert u 6/15/2020.

## <a name="generate-the-sas-address"></a>Het SAS-adres genereren

Er zijn twee algemene hulpprogram ma's voor het maken van een SAS-adres (URL):

1. **Micro soft Storage Explorer** -grafisch hulp programma dat beschikbaar is voor Windows, MacOS en Linux.
2. **Microsoft Azure cli** : aanbevolen voor niet-Windows-besturings systemen en geautomatiseerde of continue integratie omgevingen.

### <a name="using-tool-1-microsoft-storage-explorer"></a>Hulp programma 1: micro soft Storage Explorer

1. Down load en Installeer [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Open de Verkenner en selecteer **account toevoegen**in het menu links.
3. Selecteer in het dialoog venster **verbinding maken met Azure Storage** **een Azure-account toevoegen** en meld u aan bij uw Azure-account.
4. Vouw in het deel venster links in de Verkenner het knoop punt **opslag accounts** uit.
5. Klik met de rechter muisknop op uw VHD en selecteer **hand tekening voor share toegang ophalen**.
6. Voer in het dialoog venster **Shared Access Signature** de volgende velden in:

    1. Begin tijd – machtigings begindatum voor toegang tot de virtuele harde schijf. Geef een datum op die één dag voor de huidige datum valt.
    2. Verloop tijd – verval datum van machtiging voor toegang tot de VHD. Geef een datum op die ten minste drie weken na de huidige datum valt.
    3. Machtigingen: Selecteer de machtigingen lezen en lijst.
    4. Container niveau: Schakel het selectie vakje Genereer de URI op container niveau van de Shared Access-hand tekening genereren in.

    ![Het dialoog venster Shared Access Signature.](media/vm/create-sas-uri-storage-explorer.png)

7. Selecteer **maken**om de bijbehorende SAS-URI voor deze VHD te maken. Het dialoog venster wordt vernieuwd en Details over deze bewerking worden weer gegeven.

8. Kopieer de URI en sla deze op in een tekst bestand op een veilige locatie.

    ![De URI wordt gekopieerd.](media/vm/create-sas-uri-shared-access-signature-details.png)

    Deze gegenereerde SAS-URI is voor toegang op container niveau. Als u deze specifiek wilt maken, bewerkt u het tekst bestand om de naam van de VHD toe te voegen.

9. Plaats de naam van de VHD achter de vhd's-teken reeks in de SAS-URI (neem een slash op). De uiteindelijke SAS-URI moet er als volgt uitzien:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

1. Herhaal deze stappen voor elke VHD in de plannen die u wilt publiceren.

### <a name="using-tool-2-azure-cli"></a>Hulp programma 2: Azure CLI gebruiken

1. Down load en Installeer [Microsoft Azure LC](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)I. Er zijn versies beschikbaar voor Windows, macOS en verschillende distributies van Linux.
2. Maak een Power shell-bestand (. ps1-bestands extensie), kopieer de volgende code en sla deze lokaal op.

    ```JSON
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <vhd-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Bewerk het bestand om de volgende parameter waarden te gebruiken. Geef datums op in UTC-datum notatie, zoals 2020-04-01T00:00:00Z.

    - account naam: de naam van uw Azure Storage-account.
    - account sleutel: uw Azure Storage-account sleutel.
    - VHD-naam: de naam van uw VHD.
    - Start datum: de start datum van machtigingen voor toegang tot de VHD. Geef een datum op van één dag voor de huidige datum.
    - verloop datum: de verval datum van de machtiging voor de toegang tot de virtuele harde schijf. Geef een datum op die ten minste drie weken na de huidige datum valt.

    Hier volgt een voor beeld van de juiste parameter waarden (op het moment van schrijven):

    `az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name vhds -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’`

1. Sla de wijzigingen op.
2. Gebruik een van de volgende methoden om dit script uit te voeren met beheerders bevoegdheden voor het maken van een SAS-connection string voor toegang op container niveau:

    - Voer het script uit vanaf de-console. Klik in Windows met de rechter muisknop op het script en selecteer **als administrator uitvoeren**.
    - Voer het script uit vanuit een Power shell-script editor, zoals [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). In dit scherm wordt het maken van een SAS-connection string in deze editor weer gegeven:

    [![een SAS-connection string maken in de Power shell-editor](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Kopieer de SAS-connection string en sla deze op in een tekst bestand op een veilige locatie. Bewerk deze teken reeks om de VHD-locatie gegevens toe te voegen om de definitieve SAS-URI te maken.
7. Ga in het Azure Portal naar de Blob-opslag met de VHD die is gekoppeld aan de nieuwe URI.
8. Kopieer de URL van het thebBlob-service-eind punt:

    ![De URL van het eind punt van de BLOB-service wordt gekopieerd.](media/vm/create-sas-uri-blob-endpoint.png)

9. Bewerk het tekst bestand met de SAS-connection string uit stap 6. Maak de volledige SAS-URI met de volgende indeling:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>De SAS-URI controleren

Controleer de SAS-URI voordat u deze publiceert in het partner centrum om te voor komen dat er problemen zijn met de SAS-URI na verzen ding van de aanvraag. Dit proces is optioneel, maar wordt aanbevolen.

- De URI bevat de bestands naam van de VHD-installatie kopie, inclusief de bestandsnaam extensie `.vhd` .
- `Sp=rl` wordt weer gegeven in de buurt van de URI. In deze teken reeks wordt de lees-en lijst toegang weer gegeven.
- Wanneer `sr=c` wordt weer gegeven, betekent dit dat toegang op container niveau is opgegeven.
- Kopieer en plak de URI in een browser om de BLOB te testen (u kunt de bewerking annuleren voordat de down load is voltooid).

## <a name="next-step"></a>Volgende stap

- Lees [een Azure-aanbieding voor virtuele machines maken](azure-vm-create-offer.md).
