---
title: Uri voor gedeelde toegangshandtekeningen voor uw VM-afbeelding in Microsoft Azure | Azure Marketplace
description: Hier wordt uitgelegd hoe u de Uri (Shared Access Signature) URI voor uw VM-afbeelding krijgen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 2fdbc2a11bd963057b465a629757f2be51ae4061
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273848"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Uri voor gedeelde toegangshandtekeningen voor uw VM-afbeelding

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar partnercentrum. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Uri voor gedeelde toegangmet handtekening voor uw VM-afbeelding](https://aka.ms/GetSASURI) om uw gemigreerde aanbiedingen te beheren.

Tijdens het publicatieproces moet u een uniforme resource-id (URI) opgeven voor elke virtuele harde schijf (VHD) die aan uw SKU's is gekoppeld. Microsoft heeft toegang nodig tot deze VHD's tijdens het certificeringsproces. In dit artikel wordt uitgelegd hoe u een SAS-uri (Shared Access Signature) genereert voor elke VHD. U voert deze URI in op het tabblad **SKU's** in de Cloud Partner Portal.

Houd u bij het genereren van SAS-URI's voor uw VHD's aan de volgende vereisten:

- Alleen onbeheerde VHD's worden ondersteund.
- `List`en `Read` machtigingen volstaan. Geef *not* of `Write` `Delete` heb geen toegang.
- De toegangsduur *(vervaldatum)* moet minimaal drie weken zijn vanaf het moment waarop de SAS URI wordt gemaakt.
- Als u wilt beschermen tegen utc-tijdvariaties, stelt u de begindatum in op één dag voor de huidige datum. Als de huidige datum bijvoorbeeld 6 oktober 2014 is, selecteert u 10/5/2014.

## <a name="generate-the-sas-url"></a>De SAS-URL genereren

De SAS-URL kan op twee veelvoorkomende manieren worden gegenereerd met behulp van de volgende hulpprogramma's:

- Microsoft Storage Explorer - Grafisch hulpprogramma beschikbaar voor Windows, macOS en Linux
- Microsoft Azure CLI - Aanbevolen voor niet-Windows-bess en geautomatiseerde of continue integratieomgevingen

### <a name="azure-cli"></a>Azure CLI

Gebruik de volgende stappen om een SAS URI te genereren met Azure CLI.

1. Download en installeer de [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Versies zijn beschikbaar voor Windows, macOS en verschillende distro's van Linux.
2. Maak een PowerShell-bestand (bestandsextensie),`.ps1` kopieer in de volgende code en sla het lokaal op.

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```

3. Bewerk het bestand om de volgende parameterwaarden te leveren.  Datums moeten bijvoorbeeld worden opgegeven in `2016-10-25T00:00:00Z`utc-datumnotatie .
   - `<account-name>`- Uw Azure-opslagaccountnaam
   - `<account-key>`- Uw Azure-opslagaccountsleutel
   - `<vhd-name>`- Uw VHD-naam
   - `<start-date>`- Startdatum voor toestemming voor VHD-toegang. Geef een datum op een dag voor de huidige datum.
   - `<expiry-date>`- Vervaldatum van de toestemming voor VHD-toegang.  Geef een datum op die ten minste drie weken na de huidige datum is.

   In het volgende voorbeeld worden de juiste parameterwaarden weergegeven (op het moment van schrijven).

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```

4. Sla de wijzigingen in dit PowerShell-script op.
5. Voer dit script uit met behulp van beheerdersbevoegdheden om een *SAS-verbindingstekenreeks* voor toegang tot containerniveau te genereren.  U twee basisbenaderingen gebruiken:
   - Voer het script uit vanaf de console.  Als u bijvoorbeeld in Windows schrijft, klikt u op het script en selecteert u **Uitvoeren als beheerder**.
   - Voer het script uit vanuit een PowerShell-scripteditor, zoals de [Windows PowerShell ISE,](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)met behulp van beheerdersbevoegdheden.
     Hieronder wordt een SAS-verbindingstekenreeks gegenereerd binnen deze editor.

     ![SAS URI-generatie in PowerShell ISE](./media/publishvm_032.png)

6. Kopieer de resulterende SAS-verbindingstekenreeks en sla deze op in een tekstbestand op een veilige locatie.  U bewerkt deze tekenreeks om de bijbehorende VHD-locatiegegevens toe te voegen om de uiteindelijke SAS URI te maken.
7. Navigeer in de Azure-portal naar de blob-opslag die de VHD bevat die is gekoppeld aan de nieuw gegenereerde URI.
8. Kopieer de URL-waarde van het eindpunt van de **Blob-service**, zoals hieronder wordt weergegeven.

    ![Eindpunt van blobservice in Azure-portal](./media/publishvm_033.png)

9. Bewerk het tekstbestand met de SAS-verbindingstekenreeks vanaf stap 6.  U bouwt de volledige SAS URI met de volgende indeling:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Als de naam van de VDH bijvoorbeeld is, `TestRGVM2.vhd`dan is de resulterende SAS URI:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Herhaal deze stappen voor elke VHD in de SKU's die u wilt publiceren.

### <a name="microsoft-storage-explorer"></a>Microsoft Storage Explorer

Gebruik de volgende stappen om een SAS URI te genereren met de Microsoft Azure Storage Explorer.

1. De [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) downloaden en installeren.
2. Open de verkenner en klik in de linkermenubalk op het pictogram **Account toevoegen.**  Het dialoogvenster **Verbinding maken met Azure Storage** wordt weergegeven.
3. Selecteer **Een Azure-account toevoegen** en klik op **Aanmelden**.  Ga door met de vereiste stappen om u aan te melden bij uw Azure-account.
4. Navigeer in het **deelvenster** Explorer aan de linkerkant naar uw **opslagaccounts** en vouw dit knooppunt uit.
5. Klik met de rechtermuisknop op uw VHD en selecteer **Toegang tot delen ophalen in** het contextmenu.

    ![SAS-item ontvangen in Azure Explorer](./media/publishvm_034.png)

6. Het dialoogvenster **Gedeelde toegangshandtekening** wordt weergegeven. Voer waarden in voor de volgende velden:
   - **Begintijd** - Begindatum voor toestemming voor VHD-toegang. Geef een datum op die één dag voor de huidige datum is.
   - **Vervaldatum** - Vervaldatum van de toestemming voor VHD-toegang.  Geef een datum op die ten minste drie weken na de huidige datum is.
   - **Machtigingen** - Selecteer `Read` `List` de machtigingen en machtigingen.

     ![Dialoogvenster SAS in Azure Explorer](./media/publishvm_035.png)

7. Klik **op Maken** om de bijbehorende SAS URI voor deze VHD te maken.  In het dialoogvenster worden nu details over deze bewerking weergegeven.
8. Kopieer de **URL-waarde** en sla deze op in een tekstbestand op een veilige locatie.

    ![SAS URI maken in Azure Explorer](./media/publishvm_036.png)

    Deze gegenereerde SAS URL is voor toegang op containerniveau.  Om het specifiek te maken, moet de bijbehorende VHD-naam eraan worden toegevoegd.

9. Bewerk het tekstbestand. Voeg uw VHD-naam in na de `vhds` tekenreeks in de SAS-URL (voeg een voorloopslash toe).  De uiteindelijke SAS URI moet van het formaat zijn:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Als de naam van de VDH bijvoorbeeld is, `TestRGVM2.vhd`dan is de resulterende SAS URI:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Herhaal deze stappen voor elke VHD in de SKU's die u wilt publiceren.

## <a name="verify-the-sas-uri"></a>Controleer de SAS URI

Controleer en verifieer elke gegenereerde SAS URI met behulp van de volgende checklist.  Controleer of:

- De URI is van `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + het formulier:`<sas-connection-string>`
- De URI bevat uw VHD-afbeeldingbestandsnaam, inclusief de bestandsnaamextensie ".vhd".
- Naar het midden van `sp=rl` de URI, verschijnt. Deze tekenreeks `Read` geeft `List` aan dat en toegang is opgegeven.
- Na dat `sr=c` punt, verschijnt ook. Deze tekenreeks geeft aan dat toegang op containerniveau is opgegeven.
- Kopieer en plak de URI in een browser om de bijbehorende blob te downloaden.  (U de bewerking annuleren voordat de download is voltooid.)

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt bij het genereren van een SAS URI, raadpleegt u [algemene URL-problemen met SAS.](./cpp-common-sas-url-issues.md)  Anders slaat u de SAS URI(s) op een veilige locatie op voor later gebruik. Het is vereist om [uw VM-aanbieding](./cpp-publish-offer.md) te publiceren in de Cloud Partner Portal.
