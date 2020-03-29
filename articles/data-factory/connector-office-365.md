---
title: Gegevens uit Office 365 kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit Office 365 naar ondersteunde sink-gegevensarchieven met behulp van kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: d97b3caccc92f0fdfeb229d94e30ee6499c26181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912402"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Gegevens uit Office 365 kopiëren naar Azure met Azure Data Factory

Azure Data Factory integreert met [Microsoft Graph data connect,](https://docs.microsoft.com/graph/data-connect-concept-overview)zodat u de rijke organisatiegegevens in uw Office 365-tenant op een schaalbare manier in Azure brengen en analytics-toepassingen bouwen en inzichten extraheren op basis van deze waardevolle gegevenselementen. Integratie met Privileged Access Management biedt beveiligde toegangscontrole voor de waardevolle samengestelde gegevens in Office 365.  Raadpleeg [deze link](https://docs.microsoft.com/graph/data-connect-concept-overview) voor een overzicht over Microsoft Graph data connect en raadpleeg [deze link](https://docs.microsoft.com/graph/data-connect-policies#licensing) voor licentiegegevens.

In dit artikel wordt beschreven hoe u activiteit kopiëren in Azure Data Factory gebruikt om gegevens uit Office 365 te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden
ADF Office 365-connector en Microsoft Graph-gegevensverbinding maken het mogelijk om op schaal verschillende typen gegevenssets uit exchange-e-mailboxen te gebruiken, waaronder adresboekcontactpersonen, agenda-afspraken, e-mailberichten, gebruikersgegevens, postvakinstellingen en ga zo maar door.  Raadpleeg [hier](https://docs.microsoft.com/graph/data-connect-datasets) de volledige lijst met beschikbare gegevenssets.

Vooralsnog u binnen één kopieeractiviteit alleen **gegevens uit Office 365 kopiëren naar [Azure Blob Storage,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)en [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) in JSON-indeling** (typesetOfObjects). Als u Office 365 wilt laden in andere typen gegevensarchieven of in andere indelingen, u de eerste kopieeractiviteit met een volgende kopieeractiviteit ketenen om gegevens verder te laden in een van de [ondersteunde ADF-doelopslag](copy-activity-overview.md#supported-data-stores-and-formats) (verwijzen naar de kolom 'ondersteund als een sink' in de tabel 'Ondersteunde gegevensopslag en -indelingen').

>[!IMPORTANT]
>- Het Azure-abonnement dat de gegevensfabriek en het sink-gegevensarchief bevat, moet onder dezelfde Azure Active Directory-tenant (Azure AD) als de Office 365-tenant staan.
>- Controleer of het runtime-gebied Azure Integration wordt gebruikt voor kopieeractiviteit en dat de bestemming zich in hetzelfde gebied bevindt waar het postvak van de Office 365-tenantgebruikers zich bevindt. Raadpleeg [hier](concepts-integration-runtime.md#integration-runtime-location) hoe de Azure IR-locatie wordt bepaald. Raadpleeg [hier de tabel](https://docs.microsoft.com/graph/data-connect-datasets#regions) voor de lijst met ondersteunde Office-regio's en bijbehorende Azure-regio's.
>- Service principal-verificatie is het enige verificatiemechanisme dat wordt ondersteund voor Azure Blob Storage, Azure Data Lake Storage Gen1 en Azure Data Lake Storage Gen2 als bestemmingsopslag.

## <a name="prerequisites"></a>Vereisten

Als u gegevens uit Office 365 wilt kopiëren naar Azure, moet u de volgende vereistestappen uitvoeren:

- Uw Office 365-tenantbeheerder moet de on-boarding-acties uitvoeren zoals [hier](https://docs.microsoft.com/graph/data-connect-get-started)beschreven.
- Een Azure AD-webtoepassing maken en configureren in Azure Active Directory.  Zie Een [Azure AD-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)voor instructies .
- Noteer de volgende waarden die u gebruikt om de gekoppelde service voor Office 365 te definiëren:
    - Huurder-ID. Zie [Tenant-id opvragen voor](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)instructies.
    - Toepassings-ID en toepassingssleutel.  Zie [Toepassings-id en verificatiesleutel opvragen](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)voor instructies.
- Voeg de gebruikersidentiteit toe die het verzoek tot toegang tot gegevens als eigenaar van de Azure AD-webtoepassing (vanuit de Azure AD-webtoepassing > Instellingen > Eigenaren > Eigenaar toevoegen) gaat maken. 
    - De gebruikersidentiteit moet zich bevinden in de Office 365-organisatie waar u gegevens van krijgt en mag geen gastgebruiker zijn.

## <a name="approving-new-data-access-requests"></a>Nieuwe aanvragen voor toegang tot gegevens goedkeuren

Als dit de eerste keer is dat u gegevens opvraagt voor deze context (een combinatie van welke gegevenstabel wordt geopend, in welk doelaccount de gegevens worden geladen en in welke gebruikersidentiteit de aanvraag voor gegevenstoegang wordt ingediend), ziet u de status van kopieeractiviteit als 'In uitvoering' en alleen wanneer u klikt op [de link Details onder Acties](copy-activity-overview.md#monitoring) ziet u de status 'Verzoek om toestemming'.  Een lid van de groep voor gegevenstoegangsmoet de aanvraag in het Privileged Access Management goedkeuren voordat de gegevensextractie kan worden voortgezet.

Raadpleeg [hier](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) hoe de goedkeurder het verzoek tot toegang tot gegevens kan goedkeuren en raadpleeg [hier](https://docs.microsoft.com/graph/data-connect-pam) een uitleg over de algehele integratie met Privileged Access Management, inclusief het instellen van de groep voor de goedkeuring van gegevenstoegang.

## <a name="policy-validation"></a>Beleidsvalidatie

Als ADF is gemaakt als onderdeel van een beheerde app en Azure-beleidstoewijzingen worden uitgevoerd op resources binnen de beheerbrongroep, controleert ADF voor elke kopieeractiviteit of de beleidstoewijzingen worden afgedwongen. Raadpleeg [hier](https://docs.microsoft.com/graph/data-connect-policies#policies) een lijst met ondersteunde beleidsregels.

## <a name="getting-started"></a>Aan de slag

>[!TIP]
>Zie [Gegevens laden uit office 365](load-office-365-data.md) voor een walkthrough van het gebruik van Office 365-connector.

U een pijplijn maken met de kopieeractiviteit met behulp van een van de volgende hulpprogramma's of SDK's. Selecteer een koppeling om naar een zelfstudie te gaan met stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken. 

- [Azure-portal](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST-API](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager-sjabloon](quickstart-create-data-factory-resource-manager-template.md). 

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor de Office 365-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de gekoppelde office 365-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **Office365** | Ja |
| office365TenantId | Azure-tenant-id waartoe het Office 365-account behoort. | Ja |
| servicePrincipalTenantId | Geef de tenantgegevens op waaronder uw Azure AD-webtoepassing zich bevindt. | Ja |
| servicePrincipalId | Geef de client-id van de toepassing op. | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. | Ja |
| connectVia | De integratieruntijd die moet worden gebruikt om verbinding te maken met het gegevensarchief.  Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. | Nee |

>[!NOTE]
> Het verschil tussen **office365TenantId** en **servicePrincipalTenantId** en de bijbehorende waarde te bieden:
>- Als u een bedrijfsontwikkelaar bent die een toepassing ontwikkelt tegen Office 365-gegevens voor het gebruik van uw eigen organisatie, moet u dezelfde tenant-id leveren voor beide eigenschappen, wat de AAD-tenant-id van uw organisatie is.
>- Als u een ISV-ontwikkelaar bent die een applicatie voor uw klanten ontwikkelt, dan is office365TenantId de AAD-tenant-id van uw klant (applicatie-installatie- en servicePrincipalTenantId) de AAD-tenant-id van uw bedrijf.

**Voorbeeld:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Office 365-gegevensset.

Als u gegevens uit Office 365 wilt kopiëren, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **Office365Table** | Ja |
| tableName | Naam van de gegevensset die u uit Office 365 wilt halen. Raadpleeg [hier](https://docs.microsoft.com/graph/data-connect-datasets#datasets) de lijst met Office 365-gegevenssets die beschikbaar zijn voor extractie. | Ja |

Als u `dateFilterColumn`instellingen `startTime` `endTime`, `userScopeFilterUri` , , en in de gegevensset, wordt het nog steeds ondersteund as-is, terwijl u wordt voorgesteld om het nieuwe model te gebruiken in activiteitbron in de toekomst.

**Voorbeeld**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door office 365-bron.

### <a name="office-365-as-source"></a>Office 365 als bron

Als u gegevens uit Office 365 wilt kopiëren, worden de volgende eigenschappen ondersteund in de **sectie** bron van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van kopieeractiviteit moet zijn ingesteld op: **Office365Source** | Ja |
| toegestaanGroepen | Groepsselectiepredicaat.  Gebruik deze eigenschap om maximaal 10 gebruikersgroepen te selecteren voor wie de gegevens worden opgehaald.  Als er geen groepen zijn opgegeven, worden de gegevens geretourneerd voor de hele organisatie. | Nee |
| userScopeFilterUri | Wanneer `allowedGroups` de eigenschap niet is opgegeven, u een predicaatexpressie gebruiken die op de hele tenant wordt toegepast om de specifieke rijen te filteren die u uit Office 365 wilt halen. De predicaatindeling moet overeenkomen met de queryindeling van `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`Microsoft Graph API's, bijvoorbeeld . | Nee |
| dateFilterKolom | Naam van de filterkolom DateTime. Gebruik deze eigenschap om het tijdsbereik te beperken waarvoor Office 365-gegevens worden geëxtraheerd. | Ja als de gegevensset een of meer Datumtijdkolommen bevat. Raadpleeg [hier](https://docs.microsoft.com/graph/data-connect-filtering#filtering) voor een lijst met gegevenssets waarvoor dit DateTime-filter vereist is. |
| startTime | Datumtijdwaarde starten om op te filteren. | Ja `dateFilterColumn` als dit is opgegeven |
| endTime | Datumdatumtijd om op te filteren. | Ja `dateFilterColumn` als dit is opgegeven |
| uitvoerKolommen | Array van de kolommen te kopiëren om te zinken. | Nee |

**Voorbeeld:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
