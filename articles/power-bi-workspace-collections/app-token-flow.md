---
title: Power BI werkruimte verzamelingen verifiëren en autoriseren
description: Verifiëren en autoriseren met Power BI werkruimte verzamelingen.
services: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 8fcd7caffb041c57090d7256361421cb49a9a5fc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427112"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Verifiëren en autoriseren met Power BI werkruimte verzamelingen

Power BI werkruimte verzamelingen gebruiken **sleutels** en **app-tokens** voor verificatie en autorisatie, in plaats van expliciete verificatie door eind gebruikers. In dit model beheert uw toepassing verificatie en autorisatie voor uw eind gebruikers. Als dat nodig is, maakt en verzendt uw app de app-tokens die de service laten weten dat het aangevraagde rapport wordt weer gegeven. Voor dit ontwerp is het niet vereist dat uw app Azure Active Directory gebruikt voor verificatie en autorisatie van gebruikers, maar u kunt wel.

> [!IMPORTANT]
> Power BI Workspace Collections is afgeschaft en is beschikbaar tot juni 2018 of tot de datum die in uw contract wordt aangegeven. Om onderbreking van uw toepassing te voorkomen, wordt u geadviseerd om een migratie naar Power BI Embedded te plannen. Voor meer informatie over het migreren van gegevens naar Power BI Embedded raadpleegt u [How to migrate Power BI Workspace Collections content to Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) (Inhoud van Power BI-werkruimteverzamelingen migreren naar Power BI Embedded).

## <a name="two-ways-to-authenticate"></a>Twee manieren om te verifiëren

**Sleutel** : u kunt sleutels gebruiken voor alle Power bi werkruimte verzamelingen rest API-aanroepen. De sleutels zijn te vinden in de **Microsoft Azure-Portal** door **alle instellingen** te selecteren en vervolgens **toegangs toetsen**te kiezen. Behandel uw sleutel altijd alsof het een wacht woord is. Deze sleutels hebben machtigingen om een REST API aanroep voor een bepaalde werkruimte verzameling uit te voeren.

Als u een sleutel voor een REST-aanroep wilt gebruiken, voegt u de volgende autorisatie-header toe:

    Authorization: AppKey {your key}

**App-token** -app-tokens worden gebruikt voor alle insluitings aanvragen. Ze zijn ontworpen om aan de client zijde te worden uitgevoerd. Het token is beperkt tot één rapport en de best practice om een verval tijd in te stellen.

App-tokens zijn een JWT (JSON Web Token) die is ondertekend met een van de sleutels.

Uw app-token kan de volgende claims bevatten:

| Claim | Beschrijving |    
| --- | --- |
| **ver** |De versie van het app-token. 0.2.0 is de huidige versie. |
| **aud** |De bedoelde ontvanger van het token. Gebruik voor Power BI werkruimte verzamelingen: *https:\//Analysis.Windows.net/powerbi/API*. |
| **iss** |Een teken reeks waarmee de toepassing wordt aangegeven die het token heeft uitgegeven. |
| **type** |Het type app-token dat wordt gemaakt. Huidig het enige type dat wordt ondersteund, is **insluiten**. |
| **wcn** |Naam van de werkruimte verzameling waarvoor het token wordt uitgegeven. |
| **wid** |De werk ruimte-ID waarvoor het token wordt uitgegeven. |
| **Rid** |Rapport-ID waarvoor het token wordt uitgegeven. |
| **gebruikers naam** (optioneel) |Gebruikers naam is een teken reeks die wordt gebruikt met beveiliging op rijniveau om de gebruiker te identificeren bij het Toep assen van regels voor beveiliging op rijniveau. |
| **rollen** (optioneel) |Een teken reeks met de rollen die moeten worden geselecteerd bij het Toep assen van beveiligings regels op rijniveau. Als er meer dan één rol wordt door gegeven, moeten ze als een Sting-matrix worden doorgestuurd. |
| **SCP** (optioneel) |Een teken reeks met de machtigingen bereiken. Als er meer dan één rol wordt door gegeven, moeten ze als een Sting-matrix worden doorgestuurd. |
| **exp** (optioneel) |Hiermee wordt het tijdstip aangegeven waarop het token verloopt. De waarde moet worden door gegeven als UNIX-tijds tempels. |
| **NBF** (optioneel) |Hiermee wordt het tijdstip aangegeven waarop het token geldig wordt. De waarde moet worden door gegeven als UNIX-tijds tempels. |

Een voor beeld van een app-token ziet er als volgt uit:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Bij een gedecodeerde weer gave ziet er ongeveer als volgt uit:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

Er zijn methoden beschikbaar in de Sdk's die het maken van app-tokens eenvoudiger maken. Voor .NET kunt u bijvoorbeeld de klasse [micro soft. PowerBI. Security. PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) bekijken en de [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) -methoden.

Voor de .NET SDK kunt u verwijzen naar [scopes](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Bereiken

Wanneer u insluit tokens gebruikt, wilt u mogelijk het gebruik beperken van de resources waarmee u toegang verleent. Daarom kunt u een token met scoped-machtigingen genereren.

Hieronder vindt u de beschik bare bereiken voor Power BI werkruimte verzamelingen.

|Scope|Beschrijving|
|---|---|
|Dataset. Read|Biedt machtigingen voor het lezen van de opgegeven gegevensset.|
|Dataset.Write|Hiermee wordt toestemming gegeven voor het schrijven naar de opgegeven gegevensset.|
|Dataset. ReadWrite|Biedt machtiging voor het lezen van en schrijven naar de opgegeven gegevensset.|
|Report. Read|Hiermee wordt toestemming gegeven om het opgegeven rapport weer te geven.|
|Report. ReadWrite|Hiermee wordt toestemming gegeven voor het weer geven en bewerken van het opgegeven rapport.|
|Workspace.Report.Create|Biedt machtiging voor het maken van een nieuw rapport in de opgegeven werk ruimte.|
|Werk ruimte. Report. Copy|Hiermee wordt toestemming gegeven voor het klonen van een bestaand rapport in de opgegeven werk ruimte.|

U kunt meerdere bereiken opgeven door gebruik te maken van een spatie tussen de bereiken, zoals hieronder.

```csharp
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Vereiste claims-bereiken**

SCP: {scopesClaim} scopesClaim kan een teken reeks of matrix met teken reeksen zijn, waarbij de toegestane machtigingen voor werkruimte resources (rapport, gegevensset, enzovoort) worden genoteerd.

Een gedecodeerd token, met gedefinieerde bereiken, ziet er ongeveer als volgt uit:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>Bewerkingen en bereiken

|Bewerking|Doelbron|Token machtigingen|
|---|---|---|
|Maak een nieuw rapport op basis van een gegevensset (in het geheugen).|Gegevensset|Dataset. Read|
|Maak een nieuw rapport op basis van een gegevensset en sla het rapport op in het geheugen.|Gegevensset|* Dataset. Read<br>* Werk ruimte. rapport. maken|
|Een bestaand rapport weer geven en verkennen/bewerken (in het geheugen). Report. Read impliceert DataSet. Read. Met Report. Read kunnen bewerkingen niet worden opgeslagen.|Rapport|Report. Read|
|Bewerk een bestaand rapport en sla het op.|Rapport|Report. ReadWrite|
|Een kopie van een rapport opslaan (opslaan als).|Rapport|* Rapport. lezen<br>* Werk ruimte. Report. Copy|

## <a name="heres-how-the-flow-works"></a>De stroom werkt als volgt
1. Kopieer de API-sleutels naar uw toepassing. U kunt de sleutels in **Azure Portal**ophalen.
   
    ![Waar vind ik de API-sleutels in de Azure Portal](media/get-started-sample/azure-portal.png)
1. Token bewering een claim en heeft een verloop tijd.
   
    ![App-token stroom-claim token verklaringen](media/get-started-sample/token-2.png)
1. Token wordt ondertekend met een API-toegangs sleutel.
   
    ![App-token stroom-token wordt ondertekend](media/get-started-sample/token-3.png)
1. Gebruikers aanvragen om een rapport weer te geven.
   
    ![App-token stroom-gebruikers aanvragen om een rapport weer te geven](media/get-started-sample/token-4.png)
1. Het token is gevalideerd met een API-toegangs sleutel.
   
   ![App-token stroom-token is gevalideerd](media/get-started-sample/token-5.png)
1. Power BI werkruimte verzamelingen stuurt een rapport naar de gebruiker.
   
   ![App-token stroom-service rapport naar gebruiker verzenden](media/get-started-sample/token-6.png)

Nadat **Power bi werkruimte verzamelingen** een rapport naar de gebruiker stuurt, kan de gebruiker het rapport bekijken in uw aangepaste app. Als u bijvoorbeeld het voor beeld voor het [analyseren van sales data PBIX](https://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix)hebt geïmporteerd, ziet de voor beeld-web-app er als volgt uit:

![Voor beeld van Inge sloten rapport in toepassing](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Zie ook

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Voor beeld aan de slag met micro soft Power BI Workspace Collections](get-started-sample.md)  
[Algemene scenario's voor micro soft Power BI-werkruimte verzamelingen](scenarios.md)  
[Aan de slag met micro soft Power BI-werkruimte verzamelingen](get-started.md)  
[PowerBI-CSharp Git opslag plaats](https://github.com/Microsoft/PowerBI-CSharp)

Nog vragen? [Probeer de Power BI-community](https://community.powerbi.com/)
