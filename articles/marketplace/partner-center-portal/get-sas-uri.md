---
title: Uri voor gedeelde toegangshandtekeningen voor uw VM-afbeelding | Azure Marketplace
description: In dit artikel wordt uitgelegd hoe u de uri voor gedeelde toegangshandtekeningen (SAS) voor elke virtuele harde schijf (VHD) krijgen.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: 50bda733b0bb8b0c98eb69a15ab3000ad278031c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265705"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Uri voor gedeelde toegangshandtekeningen voor uw VM-afbeelding

> [!IMPORTANT]
> We verplaatsen het beheer van uw Azure Virtual Machine-aanbiedingen van Cloud Partner Portal naar Partner Center. Volg de instructies in Uri voor [gedeelde toegang voor uw VM-afbeelding](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) voor Cloud Partner Portal om uw aanbiedingen te beheren totdat uw aanbiedingen zijn gemigreerd totdat uw aanbiedingen zijn gemigreerd.

In dit artikel wordt beschreven hoe u een uniforme resource-id (Social Access Signature) (Shared Access Signature) (URI) genereert voor elke virtuele harde schijf (VHD).

Tijdens het publicatieproces moet u een URI opgeven voor elke VHD die aan uw plannen is gekoppeld. Deze plannen werden eerder aangeduid als SKU's, of voorraad houden eenheden. Microsoft heeft toegang nodig tot deze VHD's tijdens het certificeringsproces. U voert deze URI in op het tabblad **Plannen** in Partnercentrum.

Volg bij het genereren van SAS-URI's voor uw VHD's de volgende vereisten:

* Alleen onbeheerde VHD's worden ondersteund.
* Alleen `List` `Read` en machtigingen zijn vereist. Geef geen toegang voor schrijven of verwijderen.
* De toegangsduur (vervaldatum) moet minimaal drie weken zijn vanaf het moment waarop de SAS URI wordt gemaakt.
* Als u wilt beschermen tegen wijzigingen in de UTC-tijd, stelt u de begindatum in op één dag voor de huidige datum. Als de huidige datum bijvoorbeeld 6 oktober 2019 is, selecteert u 10/5/2019.

## <a name="generate-the-sas-address"></a>Het SAS-adres genereren

Er zijn twee veelgebruikte tools die worden gebruikt om een SAS-adres (URL) te maken:

* **Microsoft Storage Explorer** – Grafisch hulpprogramma beschikbaar voor Windows, macOS en Linux.
* **Microsoft Azure CLI** – Aanbevolen voor niet-Windows-besturingssystemen en geautomatiseerde of continue integratieomgevingen.

### <a name="use-microsoft-storage-explorer"></a>Microsoft Storage Explorer gebruiken

1. Microsoft [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)downloaden en installeren .
2. Open de verkenner en selecteer in het linkermenu **Account toevoegen**. Het dialoogvenster **Verbinding maken met Azure Storage** wordt weergegeven.
3. Selecteer **Een Azure-account toevoegen** en meld u vervolgens **aan**. Voer de vereiste stappen uit om u aan te melden bij uw Azure-account.
4. Ga in het deelvenster Links-**Explorer** naar uw **opslagaccounts** en vouw dit knooppunt uit.
5. Klik met de rechtermuisknop op uw VHD en selecteer **Vervolgens De handtekening van Toegang tot delen oplaten**.
6. Het dialoogvenster **Gedeelde toegangshandtekening** wordt weergegeven. Vul de volgende velden in:

    * **Begintijd** – Begindatum voor VHD-toegang. Geef een datum op die één dag voor de huidige datum is.
    * **Vervaldatum** – Vervaldatum van de machtigingen voor VHD-toegang. Geef een datum op die ten minste drie weken na de huidige datum is.
    * **Machtigingen** : selecteer de machtigingen Voorlezen en Lijst.
    * **Containerniveau** : schakel het selectievakje **URI voor gedeelde toegangshandtekening op containerniveau in.**

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Illustreert het dialoogvenster Handtekening voor gedeelde toegang":::

7. Als u de bijbehorende SAS URI voor deze VHD wilt maken, selecteert u **Maken**. Het dialoogvenster vernieuwt en toont details over deze bewerking.
8. Kopieer de **URI** en sla deze op een tekstbestand op een veilige locatie op.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Hiermee wordt het vak Gegevens van de handtekening van gedeelde toegang geïllustreerd":::

    Deze gegenereerde SAS URI is voor toegang op containerniveau. Als u het specifiek wilt maken, bewerkt u het tekstbestand om de VHD-naam (volgende stap) toe te voegen.

9. Voeg uw VHD-naam in na de vhds-tekenreeks in de SAS URI (voeg een voorwaartse slash toe). De laatste SAS URI moet er als volgt uitzien:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Als de naam van de VDH bijvoorbeeld is, `TestRGVM2.vhd`dan is de resulterende SAS URI:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Herhaal deze stappen voor elke VHD in de plannen die u publiceert.

### <a name="using-azure-cli"></a>Azure CLI gebruiken

1. Microsoft [Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)downloaden en installeren . Versies zijn beschikbaar voor Windows, macOS en verschillende distro's van Linux.
2. Maak een PowerShell-bestand (.ps1-bestandsextensie), kopieer in de volgende code en sla het lokaal op.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Bewerk het bestand om de volgende parameterwaarden te gebruiken. Datums in utc-datumnotatie `2020-04-01T00:00:00Z`opgeven, zoals .

    * `<account-name>`– Uw Azure-opslagaccountnaam
    * `<account-key>`– Uw Azure-opslagaccountsleutel
    * `<vhd-name>`– Uw VHD-naam
    * `<start-date>`– Begindatum van toestemming voor VHD-toegang. Geef een datum op een dag voor de huidige datum.
    * `<expiry-date>`– Vervaldatum van de toestemming voor VHD-toegang. Geef een datum op ten minste drie weken na de huidige datum.

    In dit voorbeeld worden de juiste parameterwaarden weergegeven (op het moment van schrijven):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Sla de wijzigingen op.
5. Voer dit script met behulp van een van de volgende methoden uit met beheerdersbevoegdheden om een **SAS-verbindingstekenreeks** voor toegang op containerniveau te maken:

    * Voer het script uit vanaf de console. Klik in Windows met de rechtermuisknop op het script en selecteer **Uitvoeren als beheerder**.
    * Voer het script uit vanuit een PowerShell-scripteditor zoals [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). In dit scherm wordt de creatie van een SAS-verbindingstekenreeks in deze editor weergegeven:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Illustreert het maken van een SAS-verbindingstekenreeks met Windows PowerShell ISE":::

6. Kopieer de SAS-verbindingstekenreeks en sla deze op een beveiligde locatie op in een tekstbestand. Bewerk deze tekenreeks om de VHD-locatiegegevens toe te voegen om de uiteindelijke SAS URI te maken.
7. Ga in de Azure-portal naar de blob-opslag die de VHD bevat die is gekoppeld aan de nieuwe URI.
8. De URL van het eindpunt van de **Kblob-service kopiëren,** zoals in de volgende schermafbeelding wordt weergegeven

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Hiermee wordt het eindpunt van de Blob-service geïllustreerd":::

9. Bewerk het tekstbestand met de SAS-verbindingstekenreeks vanaf stap 6. Maak de volledige SAS URI met deze indeling:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Als de naam van de VHD bijvoorbeeld is, `TestRGVM2.vhd`is de SAS URI:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Herhaal deze stappen voor elke VHD in de SKU's die u wilt publiceren.

## <a name="verify-the-sas-uri"></a>Controleer de SAS URI

Controleer elk gemaakte SAS URI met behulp van de volgende checklist om te controleren of:

* De URI ziet er als volgt uit:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* De URI bevat uw VHD-afbeeldingsbestandsnaam, inclusief de bestandsnaamextensie ".vhd".
* `sp=rl`verschijnt in de buurt van het midden van uw URI. Deze tekenreeks `Read` geeft `List` aan dat en toegang is opgegeven.
* Wanneer `sr=c` wordt weergegeven, betekent dit dat toegang op containerniveau is opgegeven.
* Kopieer en plak de URI in een browser om de blob te testen (u de bewerking annuleren voordat de download is voltooid).

## <a name="next-step"></a>Volgende stap

Zie [Algemene URL-problemen met SAS](https://aka.ms/AzureSAS_URI_FAQ)als u problemen hebt met het maken van een SAS URI. Anders slaat u de SAS URI(s) op een veilige locatie op voor later gebruik. Je hebt het nodig om je VM-aanbieding in partnercentrum te publiceren.

* [Een Azure Virtual Machine-aanbieding maken](https://aka.ms/Create_AzureVMoffer)
