---
title: Privé-eindpunten
description: Meer informatie over het proces van het maken van privé-eind punten voor Azure Backup en de scenario's waarbij persoonlijke eind punten worden gebruikt om de beveiliging van uw resources te hand haven.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 2696f3fdbc4e9061afee266ae36ae8d3507026fc
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84231419"
---
# <a name="private-endpoints-for-azure-backup"></a>Privé-eind punten voor Azure Backup

Met Azure Backup kunt u veilig back-ups van uw gegevens maken en uw Recovery Services kluizen herstellen met behulp van [privé-eind punten](https://docs.microsoft.com/azure/private-link/private-endpoint-overview). Privé-eind punten gebruiken een of meer privé-IP-adressen van uw VNet, waardoor de service effectief in uw VNet wordt gezet.

In dit artikel vindt u informatie over het proces van het maken van privé-eind punten voor Azure Backup en de scenario's waarbij persoonlijke eind punten worden gebruikt om de beveiliging van uw resources te hand haven.

## <a name="before-you-start"></a>Voordat u begint

- Privé-eind punten kunnen alleen worden gemaakt voor nieuwe Recovery Services kluizen (waarvoor geen items zijn geregistreerd bij de kluis). Daarom moeten persoonlijke eind punten worden gemaakt voordat u items op de kluis probeert te beveiligen.
- Eén virtueel netwerk kan persoonlijke eind punten voor meerdere Recovery Services kluizen bevatten. Daarnaast kan een Recovery Services kluis persoonlijke eind punten in meerdere virtuele netwerken hebben. Het maximum aantal privé-eind punten dat voor een kluis kan worden gemaakt, is echter 12.
- Zodra een persoonlijk eind punt is gemaakt voor een kluis, wordt de kluis vergrendeld. Het is niet toegankelijk (voor back-ups en herstel bewerkingen) van netwerken van een locatie die een persoonlijk eind punt voor de kluis bevatten. Als alle persoonlijke eind punten voor de kluis worden verwijderd, is de kluis toegankelijk vanuit alle netwerken.
- Hoewel een Recovery Services kluis wordt gebruikt door (beide) Azure Backup en Azure Site Recovery, wordt in dit artikel alleen het gebruik van privé-eind punten voor Azure Backup besproken.
- Azure Active Directory biedt momenteel geen ondersteuning voor persoonlijke eind punten. IP-adressen en FQDN-namen die vereist zijn voor de Azure Active Directory om in een regio te werken, moeten dus uitgaande toegang hebben tot het beveiligde netwerk wanneer ze back-ups maken van data bases in azure-Vm's en-back-ups met behulp van de MARS-agent. U kunt ook NSG Tags en Azure Firewall Tags gebruiken om toegang te verlenen tot Azure AD, zoals van toepassing.
- Virtuele netwerken met netwerk beleidsregels worden niet ondersteund voor privé-eind punten. U moet netwerk beleid uitschakelen voordat u doorgaat.

## <a name="recommended-and-supported-scenarios"></a>Aanbevolen en ondersteunde scenario's

Hoewel persoonlijke eind punten zijn ingeschakeld voor de kluis, worden ze gebruikt voor het maken van back-ups en het herstellen van SQL-en SAP HANA-workloads in een Azure-VM en alleen back-ups van de MARS-agent. U kunt ook de kluis gebruiken voor het maken van back-ups van andere werk belastingen (er zijn echter geen persoonlijke eind punten nodig). Naast het maken van een back-up van SQL-en SAP HANA-workloads en-back-ups met behulp van de MARS-agent worden persoonlijke eind punten ook gebruikt voor het herstellen van bestanden in het geval van back-ups van Azure-VM'S. Zie de volgende tabel voor meer informatie:

| Back-ups van werk belastingen in azure VM (SQL, SAP HANA), back-up maken met MARS agent | Het gebruik van privé-eind punten wordt aanbevolen om back-ups te maken en te herstellen zonder dat u alle IP-adressen/FQDN-namen voor Azure Backup of Azure Storage van uw virtuele netwerken hoeft te vermelden. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Back-up van Azure VM**                                         | Voor VM-back-ups is geen toegang tot Ip's of FQDN-adressen toegestaan. Daarom zijn geen persoonlijke eind punten vereist voor het maken van back-ups en het herstellen van schijven.  <br><br>   Bestands herstel vanuit een kluis met persoonlijke eind punten zou echter worden beperkt tot virtuele netwerken die een persoonlijk eind punt voor de kluis bevatten. <br><br>    Wanneer u ACL'ed unmanaged disks gebruikt, moet u ervoor zorgen dat het opslag account met de schijven toegang krijgt tot **vertrouwde micro soft-Services** als het ACL'ed is. |
| **Azure Files back-up**                                      | Azure Files back-ups worden opgeslagen in het lokale opslag account. Daarom zijn geen persoonlijke eind punten vereist voor back-up en herstel. |

## <a name="creating-and-using-private-endpoints-for-backup"></a>Privé-eind punten maken en gebruiken voor back-up

In deze sectie vindt u informatie over de stappen voor het maken en gebruiken van privé-eind punten voor Azure Backup binnen uw virtuele netwerken.

>[!IMPORTANT]
> Het wordt ten zeerste aangeraden om de stappen uit te voeren in dezelfde volg orde als vermeld in dit document. Als u dit niet doet, kan dit ertoe leiden dat de kluis niet compatibel is met het gebruik van privé-eind punten en dat u het proces opnieuw moet starten met een nieuwe kluis.

>[!NOTE]
> Bepaalde elementen van de Azure Portal ervaring zijn mogelijk momenteel niet beschikbaar. Raadpleeg de alternatieve ervaringen in dergelijke scenario's voor volledige Beschik baarheid in uw regio.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

Zie [deze sectie](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) voor meer informatie over het maken van een kluis met behulp van de Azure Resource Manager-client. Hiermee maakt u een kluis met de beheerde identiteit al ingeschakeld. Meer informatie over Recovery Services kluizen [vindt u hier](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview).

## <a name="enable-managed-identity-for-your-vault"></a>Beheerde identiteit voor uw kluis inschakelen

Met beheerde identiteiten kan de kluis privé-eind punten maken en gebruiken. In deze sectie vindt u informatie over het inschakelen van de beheerde identiteit voor uw kluis.

1. Ga naar uw Recovery Services kluis-> **identiteit**.

    ![Identiteits status wijzigen in aan](./media/private-endpoints/identity-status-on.png)

1. Wijzig de **status** in **on** en klik op **Opslaan**.

1. Er wordt een **object-id** gegenereerd. Dit is de beheerde identiteit van de kluis.

    >[!NOTE]
    >Wanneer de beheerde identiteit is ingeschakeld, mag deze niet worden uitgeschakeld (zelfs tijdelijk). Het uitschakelen van de beheerde identiteit kan leiden tot inconsistent gedrag.

## <a name="dns-changes"></a>DNS-wijzigingen

Voor het gebruik van privé-eind punten zijn Privé-DNS zones vereist om de back-upextensie toe te staan om FQDN-adressen van particuliere koppelingen om te zetten naar privé Ip's Samen zijn er drie privé-DNS-zones vereist. Hoewel er twee van deze zones moeten worden gemaakt, kan de derde worden gekozen om te worden geïntegreerd met het persoonlijke eind punt (tijdens het maken van het persoonlijke eind punt) of afzonderlijk kunnen worden gemaakt.

U kunt ook uw aangepaste DNS-servers gebruiken. Raadpleeg [DNS-wijzigingen voor aangepaste DNS-servers](#dns-changes-for-custom-dns-servers) voor meer informatie over het gebruik van aangepaste DNS-servers.

### <a name="creating-mandatory-dns-zones"></a>Verplichte DNS-zones maken

Er zijn twee verplichte DNS-zones die moeten worden gemaakt:

- `privatelink.blob.core.windows.net`(voor back-up/herstel gegevens)
- `privatelink.queue.core.windows.net`(voor service communicatie)

1. Zoek in de zoek balk **alle services** naar **privé-DNS zone** en selecteer **privé-DNS zone** in de vervolg keuzelijst.

    ![Privé-DNS zone selecteren](./media/private-endpoints/private-dns-zone.png)

1. Klik in het deel venster **privé-DNS zone** op de knop **+ toevoegen** om te beginnen met het maken van een nieuwe zone.

1. Vul in het deel venster **privé-DNS-zone maken** de benodigde gegevens in. Het abonnement moet hetzelfde zijn als waar het persoonlijke eind punt wordt gemaakt.

    De zones moeten de volgende naam hebben:

    - `privatelink.blob.core.windows.net`
    - `privatelink.queue.core.windows.net`

    | **Gebied**                           | **Service** | **Details van het abonnement en de resource groep (RG)**                  |
    | ---------------------------------- | ----------- | ------------------------------------------------------------ |
    | `privatelink.blob.core.windows.net`  | Blob        | **Abonnement**: hetzelfde als het persoonlijke eind punt dat moet worden gemaakt **RG**: ofwel de RG van het VNET of het persoonlijke eind punt |
    | `privatelink.queue.core.windows.net` | Wachtrij       | **RG**: de RG van het VNET of van het persoonlijke eind punt |

    ![Privé-DNS zone maken](./media/private-endpoints/create-private-dns-zone.png)

1. Als u klaar bent, gaat u verder met het controleren en maken van de DNS-zone.

### <a name="optional-dns-zone"></a>Optionele DNS-zone

Klanten kunnen ervoor kiezen om hun persoonlijke eind punten te integreren met privé-DNS-zones voor Azure Backup (besproken in de sectie over het maken van privé-eind punten) voor service communicatie. Als u niet wilt integreren met de privé-DNS-zone, kunt u ervoor kiezen om uw eigen DNS-server te gebruiken of een privé-DNS-zone afzonderlijk te maken. Dit is een aanvulling op de twee verplichte privé-DNS-zones die in de vorige sectie zijn besproken.

Als u een afzonderlijke privé-DNS-zone wilt maken in azure, kunt u hetzelfde doen met behulp van dezelfde stappen die worden gebruikt voor het maken van verplichte DNS-zones. De naamgevings-en abonnements gegevens worden hieronder gedeeld:

| **Gebied**                                                     | **Service** | **Details van het abonnement en de resource groep**                  |
| ------------------------------------------------------------ | ----------- | ------------------------------------------------------------ |
| `privatelink.<geo>.backup.windowsazure.com`  <br><br>   **Opmerking**: *geografische* hier verwijst naar de regio code. Bijvoorbeeld *wcus* en *ne* voor West-Centraal VS en Europa-Noord respectievelijk. | Backup      | **Abonnement**: hetzelfde als waar het persoonlijke eind punt moet worden gemaakt **RG**: elke rg in het abonnement |

Raadpleeg [deze lijst](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) voor regio codes.

Voor URL-naamgevings conventies in nationale regio's:

- [China](https://docs.microsoft.com/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [Duitsland](https://docs.microsoft.com/azure/germany/germany-developer-guide#endpoint-mapping)
- [US Gov](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide)

### <a name="linking-private-dns-zones-with-your-virtual-network"></a>Persoonlijke DNS-zones koppelen aan uw virtuele netwerk

De hierboven gemaakte DNS-zones moeten nu zijn gekoppeld aan het virtuele netwerk waarvan de servers waarvan u een back-up wilt maken, zich bevinden. Dit moet worden gedaan voor alle DNS-zones die u hebt gemaakt.

1. Ga naar de DNS-zone (die u in de vorige stap hebt gemaakt) en navigeer naar **virtuele netwerk koppelingen** op de balk aan de linkerkant. Klik vervolgens op de knop **toevoegen** .
1. Vul de vereiste gegevens in. De velden **abonnement** en **virtueel netwerk** moeten worden ingevuld met de overeenkomende gegevens van het virtuele netwerk waarin uw servers zijn opgenomen. De andere velden moeten blijven staan.

    ![Koppeling van virtueel netwerk toevoegen](./media/private-endpoints/add-virtual-network-link.png)

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Machtigingen verlenen aan de kluis om vereiste persoonlijke eind punten te maken

Als u de vereiste privé-eind punten voor Azure Backup wilt maken, moet de kluis (de beheerde identiteit van de kluis) machtigingen hebben voor de volgende resource groepen:

- De resource groep die het doel-VNet bevat
- De resource groep waar de persoonlijke eind punten moeten worden gemaakt
- De resource groep die de Privé-DNS zones bevat

We raden u aan de rol **Inzender** voor deze drie resource groepen toe te kennen aan de kluis (beheerde identiteit). In de volgende stappen wordt beschreven hoe u dit doet voor een bepaalde resource groep (dit moet worden gedaan voor elk van de drie resource groepen):

1. Ga naar de resource groep en navigeer naar **Access Control (IAM)** op de balk aan de linkerkant.
1. Ga in **Access Control**naar **een roltoewijzing toevoegen**.

    ![Een roltoewijzing toevoegen](./media/private-endpoints/add-role-assignment.png)

1. Kies in het deel venster **roltoewijzing toevoegen** de optie **Inzender** als **rol**en gebruik de **naam** van de kluis als de **Principal**. Selecteer uw kluis en klik op **Opslaan** wanneer u klaar bent.

    ![Rol en Principal kiezen](./media/private-endpoints/choose-role-and-principal.png)

Zie voor het beheren van machtigingen op een meer gedetailleerd niveau [rollen en machtigingen hand matig maken](#create-roles-and-permissions-manually).

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>Privé-eind punten voor Azure Backup maken en goed keuren

### <a name="creating-private-endpoints-for-backup"></a>Privé-eind punten voor back-up maken

In deze sectie wordt het proces voor het maken van een persoonlijk eind punt voor uw kluis beschreven.

1. Zoek in de zoek balk naar en selecteer **privé-koppeling**. Hiermee gaat u naar het **persoonlijke koppelings centrum**.

    ![Zoeken naar persoonlijke koppeling](./media/private-endpoints/search-for-private-link.png)

1. Klik in de linkernavigatiebalk op **persoonlijke eind punten**. Klik in het deel venster **privé-eind punten** op **+ toevoegen** om te beginnen met het maken van een persoonlijk eind punt voor uw kluis.

    ![Persoonlijk eind punt toevoegen in persoonlijk koppelings centrum](./media/private-endpoints/add-private-endpoint.png)

1. Eenmaal in het proces voor het maken van een **persoonlijk eind punt** , moet u details opgeven voor het maken van uw verbinding met een privé-eind punt.

    1. **Basis beginselen**: Vul de basis gegevens in voor uw privé-eind punten. De regio moet hetzelfde zijn als de kluis en de resource.

        ![Basis details invullen](./media/private-endpoints/basic-details.png)

    1. **Resource**: op dit tabblad moet u de Paas-resource vermelden waarvoor u de verbinding wilt maken. Selecteer **micro soft. Recovery Services/kluizen** van het resource type voor uw gewenste abonnement. Als u klaar bent, kiest u de naam van uw Recovery Services kluis als de **resource** en **AzureBackup** als de **subresource**van het doel.

        ![Het tabblad resource invullen](./media/private-endpoints/resource-tab.png)

    1. **Configuratie**: Geef in configuratie het virtuele netwerk en het subnet op waar u het persoonlijke eind punt wilt maken. Dit is het Vnet waar de virtuele machine aanwezig is. U kunt ervoor kiezen om **uw persoonlijke eind punt te integreren** met een privé-DNS-zone. U kunt ook uw aangepaste DNS-server gebruiken of een privé-DNS-zone maken.

        ![Tabblad Configuratie invullen](./media/private-endpoints/configuration-tab.png)

    1. U kunt eventueel **labels** voor uw persoonlijke eind punt toevoegen.

    1. Ga verder met **bekijken + maken** zodra u de gegevens hebt ingevoerd. Wanneer de validatie is voltooid, klikt u op **maken** om het persoonlijke eind punt te maken.

## <a name="approving-private-endpoints"></a>Persoonlijke eind punten goed keuren

Als de gebruiker die het persoonlijke eind punt maakt ook de eigenaar is van de Recovery Services kluis, wordt het persoonlijke eind punt dat hierboven is gemaakt, automatisch goedgekeurd. Anders moet de eigenaar van de kluis het persoonlijke eind punt goed keuren voordat het kan worden gebruikt. In deze sectie wordt de hand matige goed keuring van privé-eind punten in de Azure Portal beschreven.

Zie [hand matige goed keuring van privé-eind punten met behulp van de Azure Resource Manager-client](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) om de Azure Resource Manager-client te gebruiken voor het goed keuren van persoonlijke eind punten.

1. Navigeer in uw Recovery Services-kluis naar **privé-eindpunt verbindingen** op de balk aan de linkerkant.
1. Selecteer de verbinding van het particuliere eind punt dat u wilt goed keuren.
1. Selecteer **goed keuren** in de bovenste balk. U kunt ook **weigeren** of **verwijderen** selecteren als u de eindpunt verbinding wilt afwijzen of verwijderen.

    ![Privé-eind punten goed keuren](./media/private-endpoints/approve-private-endpoints.png)

## <a name="adding-dns-records"></a>DNS-records toevoegen

>[!NOTE]
> Deze stap is niet vereist als u gebruikmaakt van een geïntegreerde DNS-zone. Als u echter uw eigen Azure-Privé-DNS zone hebt gemaakt of een aangepaste privé-DNS-zone gebruikt, controleert u of de vermeldingen zijn aangebracht zoals beschreven in deze sectie.

Wanneer u de optionele privé-DNS-zone en de persoonlijke eind punten voor uw kluis hebt gemaakt, moet u DNS-records toevoegen aan uw DNS-zone. U kunt dit hand matig doen of met een Power shell-script. Dit moet alleen worden uitgevoerd voor de DNS-zone van de back-up. deze voor blobs en wacht rijen worden automatisch bijgewerkt.

### <a name="add-records-manually"></a>Records hand matig toevoegen

Hiervoor moet u vermeldingen voor elke FQDN in uw privé-eind punt in uw Privé-DNS zone maken.

1. Ga naar uw **privé-DNS-zone** en navigeer naar de optie **overzicht** op de balk aan de linkerkant. Klik vervolgens op **+ record set** om records toe te voegen.

    ![Selecteer + Recordset om records toe te voegen](./media/private-endpoints/select-record-set.png)

1. Voeg in het deel venster **recordset toevoegen** dat wordt geopend één vermelding toe voor elke FQDN en een persoonlijk IP-adres als **een type** record. De lijst met FQDN-namen en IP-adressen kan worden verkregen via uw persoonlijke eind punt (onder **overzicht**). Zoals u in het onderstaande voor beeld ziet, wordt de eerste FQDN van het persoonlijke eind punt toegevoegd aan de recordset in de privé-DNS-zone.

    ![Lijst met FQDN-namen en IP-adressen](./media/private-endpoints/list-of-fqdn-and-ip.png)

    ![Recordset toevoegen](./media/private-endpoints/add-record-set.png)

### <a name="add-records-using-powershell-script"></a>Records toevoegen met behulp van Power shell-script

1. Start de **Cloud shell** in het Azure Portal en selecteer **bestand uploaden** in het Power shell-venster.

    ![Bestand uploaden selecteren in Power shell-venster](./media/private-endpoints/upload-file-in-powershell.png)

1. Dit script uploaden: [DnsZoneCreation](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/dnszonerecordcreation.ps1)

1. Ga naar uw basismap (bijvoorbeeld: `cd /home/user` )

1. Voer het volgende script uit:

    ```azurepowershell
    ./dnszonerecordcreation.ps1 -Subscription <SubscriptionId> -VaultPEName <VaultPE Name> -VaultPEResourceGroup <Vault PE RG> -DNSResourceGroup <Private DNS RG> -Privatezone <privatednszone>
    ```

    Dit zijn de para meters:

    - **abonnement**: het abonnement waarin de resources (privé-eind punt van de kluis en de privé-DNS-zone) zich bevinden
    - **vaultPEName**: de naam van het persoonlijke eind punt dat voor de kluis is gemaakt
    - **vaultPEResourceGroup**: resource groep die het persoonlijke eind punt van de kluis bevat
    - **dnsResourceGroup**: een resource groep die de privé-DNS-zones bevat
    - **Privatezone**: naam van de privé-DNS-zone

## <a name="using-private-endpoints-for-backup"></a>Privé-eind punten gebruiken voor back-up

Zodra de privé-eind punten die zijn gemaakt voor de kluis in uw VNet zijn goedgekeurd, kunt u deze gebruiken om uw back-ups te maken en op te slaan.

>[!IMPORTANT]
>Zorg ervoor dat u alle bovenstaande stappen in het document hebt voltooid voordat u verdergaat. Voor samen vatting moet u de stappen in de volgende controle lijst hebben voltooid:
>
>1. Er is een (nieuwe) Recovery Services kluis gemaakt
>1. De kluis is ingeschakeld om een door het systeem toegewezen beheerde identiteit te gebruiken
>1. Er zijn drie Privé-DNS zones gemaakt (twee als u een geïntegreerde DNS-zone gebruikt voor back-up)
>1. Uw Privé-DNS-zones koppelen aan uw Azure-Virtual Network
>1. Relevante machtigingen toegewezen aan de beheerde identiteit van de kluis
>1. Een persoonlijk eind punt voor uw kluis gemaakt
>1. Het persoonlijke eind punt is goedgekeurd (indien niet automatisch goedgekeurd)
>1. De vereiste DNS-records zijn aan uw privé-DNS-zone voor back-up toegevoegd (alleen van toepassing als er geen geïntegreerde privé-DNS-zone wordt gebruikt)

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>Back-ups maken en herstellen van werk belastingen in azure VM (SQL, SAP HANA)

Zodra het persoonlijke eind punt is gemaakt en goedgekeurd, zijn er geen aanvullende wijzigingen vereist aan de client zijde om het persoonlijke eind punt te gebruiken. Alle communicatie-en gegevens overdracht van uw beveiligde netwerk naar de kluis wordt uitgevoerd via het persoonlijke eind punt.
Als u echter persoonlijke eind punten voor de kluis verwijdert nadat er een server (SQL/SAP HANA) is geregistreerd, moet u de container opnieuw registreren bij de kluis. U hoeft de beveiliging niet te stoppen.

### <a name="backup-and-restore-through-mars-agent"></a>Back-ups maken en herstellen via MARS-agent

Wanneer u de MARS-agent gebruikt om een back-up van uw on-premises resources te maken, moet u ervoor zorgen dat uw on-premises netwerk (met uw resources waarvan u een back-up wilt maken) is gekoppeld aan het Azure-VNet dat een persoonlijk eind punt voor de kluis bevat, zodat u het kunt gebruiken. U kunt vervolgens door gaan met de installatie van de MARS-agent en de back-up configureren, zoals hier wordt beschreven. U moet er echter voor zorgen dat alle communicatie voor back-ups alleen via het peered netwerk plaatsvindt.

Als u echter persoonlijke eind punten voor de kluis verwijdert nadat er een MARS-agent is geregistreerd, moet u de container opnieuw registreren bij de kluis. U hoeft de beveiliging niet te stoppen.

## <a name="additional-topics"></a>Extra onderwerpen

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Een Recovery Services kluis maken met behulp van de Azure Resource Manager-client

U kunt de Recovery Services-kluis maken en de beheerde identiteit inschakelen (waardoor de beheerde identiteit is vereist, zoals we later zien) met behulp van de Azure Resource Manager-client. Hieronder vindt u een voor beeld van het volgende:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

Het bovenstaande JSON-bestand moet de volgende inhoud hebben:

JSON aanvragen:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

Reactie JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>De kluis die in dit voor beeld wordt gemaakt via de Azure Resource Manager-client, is al gemaakt met een door het systeem toegewezen beheerde identiteit.

### <a name="managing-permissions-on-resource-groups"></a>Machtigingen voor resource groepen beheren

De beheerde identiteit voor de kluis moet de volgende machtigingen hebben in de resource groep en het virtuele netwerk waar de persoonlijke eind punten worden gemaakt:

- `Microsoft.Network/privateEndpoints/*`Dit is vereist voor het uitvoeren van ruw op privé-eind punten in de resource groep. Deze moet worden toegewezen aan de resource groep.
- `Microsoft.Network/virtualNetworks/subnets/join/action`Dit is vereist in het virtuele netwerk waar het privé-IP-adres aan het persoonlijke eind punt wordt gekoppeld.
- `Microsoft.Network/networkInterfaces/read`Dit is vereist voor de resource groep om de netwerk interface op te halen die voor het persoonlijke eind punt is gemaakt.
- Privé-DNS rol zone bijdrager deze rol bestaat al en kan worden gebruikt om machtigingen op te geven `Microsoft.Network/privateDnsZones/A/*` `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` .

U kunt een van de volgende methoden gebruiken om rollen met de vereiste machtigingen te maken:

#### <a name="create-roles-and-permissions-manually"></a>Rollen en machtigingen hand matig maken

Maak de volgende JSON-bestanden en gebruik de Power shell-opdracht aan het einde van de sectie om rollen te maken:

PrivateEndpointContributorRoleDef. json

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef. json

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef. json

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Een script gebruiken

1. Start de **Cloud shell** in het Azure Portal en selecteer **bestand uploaden** in het Power shell-venster.

    ![Bestand uploaden selecteren in Power shell-venster](./media/private-endpoints/upload-file-in-powershell.png)

1. Upload het volgende script: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Ga naar uw basismap (bijvoorbeeld: `cd /home/user` )

1. Voer het volgende script uit:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Dit zijn de para meters:

    - **abonnement**: * * SubscriptionId met de resource groep waar het persoonlijke eind punt voor de kluis moet worden gemaakt en het subnet waar het persoonlijke eind punt van de kluis wordt gekoppeld

    - **vaultPEResourceGroup**: resource groep waar het persoonlijke eind punt voor de kluis wordt gemaakt

    - **vaultPESubnetResourceGroup**: de resource groep van het subnet waaraan het persoonlijke eind punt wordt gekoppeld

    - **vaultMsiName**: naam van het MSI-bestand van de kluis. Dit is hetzelfde als de **kluisnaam**

1. Voltooi de verificatie en het script zal de context van het opgegeven abonnement nemen. De juiste rollen worden gemaakt als deze ontbreken in de Tenant en rollen worden toegewezen aan het MSI-bestand van de kluis.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Privé-eind punten maken met behulp van Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Automatisch goedgekeurde privé-eind punten

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Hand matige goed keuring van privé-eind punten met behulp van de Azure Resource Manager-client

1. Gebruik **GetVault** om de particuliere endpoint-verbindings-id voor uw privé-eind punt op te halen.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Hiermee wordt de verbindings-ID van het particuliere eind punt geretourneerd. De naam van de verbinding kan als volgt worden opgehaald met het eerste deel van de verbindings-ID:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Haal de **verbindings-id van het particuliere eind punt** (en de naam van het **persoonlijke eind**punt, waar nodig) van het antwoord op en vervang deze in de volgende JSON en Azure Resource Manager URI en probeer de status te wijzigen in goedgekeurd/afgewezen/losgekoppeld, zoals wordt weer gegeven in het voor beeld hieronder:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>DNS-wijzigingen voor aangepaste DNS-servers

#### <a name="create-dns-zones-for-custom-dns-servers"></a>DNS-zones maken voor aangepaste DNS-servers

U moet drie particuliere DNS-zones maken en deze koppelen aan uw virtuele netwerk.

| **Gebied**                                                     | **Service** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | Backup      |
| `privatelink.blob.core.windows.net`                            | Blob        |
| `privatelink.queue.core.windows.net`                           | Wachtrij       |

>[!NOTE]
>In de bovenstaande tekst verwijst *geo* naar de regio code. Bijvoorbeeld *wcus* en *ne* voor West-Centraal VS en Europa-Noord respectievelijk.

Raadpleeg [deze lijst](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) voor regio codes.

#### <a name="adding-dns-records-for-custom-dns-servers"></a>DNS-records toevoegen voor aangepaste DNS-servers

Hiervoor moet u vermeldingen voor elke FQDN in uw privé-eind punt in uw Privé-DNS zone maken.

U moet erop gewezen dat de persoonlijke eind punten die zijn gemaakt voor back-up, Blob en Queue-service, worden gebruikt.

- Het persoonlijke eind punt voor de kluis gebruikt de naam die is opgegeven tijdens het maken van het persoonlijke eind punt
- De persoonlijke eind punten voor Blob-en Queue-services worden voorafgegaan door de naam van hetzelfde voor de kluis.

In de volgende afbeelding ziet u bijvoorbeeld de drie persoonlijke eind punten die zijn gemaakt voor een privé-eind punt verbinding met de naam *pee2epe*:

![Drie privé-eind punten voor een verbinding met een privé-eind punt](./media/private-endpoints/three-private-endpoints.png)

DNS-zone voor de back-upservice ( `privatelink.<geo>.backup.windowsazure.com` ):

1. Navigeer naar uw persoonlijke eind punt voor back-up in het **persoonlijke koppelings centrum**. Op de overzichts pagina vindt u een lijst met de FQDN-en privé Ip's voor uw persoonlijke eind punt.

1. Voeg één vermelding voor elke FQDN en een persoonlijk IP-adres toe als een type record.

    ![Vermelding voor elke FQDN en privé-IP toevoegen](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

DNS-zone voor de Blob service ( `privatelink.blob.core.windows.net` ):

1. Navigeer naar uw persoonlijke eind punt voor Blob in het **persoonlijke koppelings centrum**. Op de overzichts pagina vindt u een lijst met de FQDN-en privé Ip's voor uw persoonlijke eind punt.

1. Voeg een vermelding voor de FQDN-naam en het persoonlijke IP-adres toe als een type record.

    ![Voeg een vermelding voor de FQDN-naam en het persoonlijke IP-adres toe als een type record voor de Blob service](./media/private-endpoints/add-type-a-record-for-blob.png)

DNS-zone voor de Queue-service ( `privatelink.queue.core.windows.net` ):

1. Navigeer naar het persoonlijke eind punt voor de wachtrij in het **persoonlijke koppelings centrum**. Op de overzichts pagina vindt u een lijst met de FQDN-en privé Ip's voor uw persoonlijke eind punt.

1. Voeg een vermelding voor de FQDN-naam en het persoonlijke IP-adres toe als een type record.

    ![Voeg een vermelding voor de FQDN-naam en het persoonlijke IP-adres toe als een type record voor de Queue-service](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

V. Kan ik een persoonlijk eind punt maken voor een bestaande back-upkluis?<br>
A. Nee, privé-eind punten kunnen alleen worden gemaakt voor nieuwe back-upkluizen. De kluis mag dus nooit items bevatten die erop zijn beveiligd. Het is in feite geen pogingen om items te beveiligen voor de kluis voordat persoonlijke eind punten worden gemaakt.

V. Ik heb geprobeerd een item te beveiligen voor mijn kluis, maar dit is mislukt en de kluis bevat nog steeds geen items die ermee zijn beveiligd. Kan ik privé-eind punten voor deze kluis maken?<br>
A. Nee, de kluis mag geen pogingen hebben gehad om in het verleden items te beveiligen.

V. Ik heb een kluis die persoonlijke eind punten gebruikt voor back-up en herstel. Kan ik later privé-eind punten toevoegen of verwijderen voor deze kluis, zelfs als er back-upitems worden beveiligd?<br>
A. Ja. Als u al persoonlijke eind punten voor een kluis en beveiligde back-upitems hebt gemaakt, kunt u later persoonlijke eind punten toevoegen of verwijderen als dat nodig is.

V. Kan het privé-eind punt voor Azure Backup ook worden gebruikt voor Azure Site Recovery?<br>
A. Nee, het privé-eind punt voor back-up kan alleen worden gebruikt voor Azure Backup. U moet een nieuw privé-eind punt voor Azure Site Recovery maken als dit wordt ondersteund door de service.

V. Ik heb een van de stappen in dit artikel gemist en is ingeschakeld om mijn gegevens bron te beveiligen. Kan ik nog steeds persoonlijke eind punten gebruiken?<br>
A. Het is niet mogelijk om de stappen in het artikel te volgen en door te gaan met het beveiligen van items kan ertoe leiden dat de kluis geen privé-eind punten kan gebruiken. Daarom wordt u aangeraden deze controle lijst te raadplegen voordat u doorgaat met het beveiligen van items.

V. Kan ik mijn eigen DNS-server gebruiken in plaats van de privé-DNS-zone van Azure of een geïntegreerde privé-DNS-zone?<br>
A. Ja, u kunt uw eigen DNS-servers gebruiken. Zorg er echter voor dat alle vereiste DNS-records worden toegevoegd, zoals wordt voorgesteld in deze sectie.

V. Moet ik aanvullende stappen uitvoeren op mijn server nadat ik het proces heb gevolgd in dit artikel?<br>
A. Na het uitvoeren van het proces dat in dit artikel wordt beschreven, hoeft u geen extra werk te doen om persoonlijke eind punten te gebruiken voor back-up en herstel.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over alle [beveiligings functies in azure backup](security-overview.md)
