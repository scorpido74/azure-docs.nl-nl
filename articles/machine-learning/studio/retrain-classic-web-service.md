---
title: Een klassieke webservice opnieuw trainen
titleSuffix: ML Studio (classic) - Azure
description: Meer informatie over het opnieuw trainen van een model en het bijwerken van een klassieke webservice om het pas getrainde model in Azure Machine Learning Studio (klassiek) te gebruiken.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: peterclu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: eac7674ae4a88621a803c70bd55a88e65b2cb7e9
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838697"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Een klassieke studio-webservice (klassiek) opnieuw trainen en implementeren

Het opnieuw trainen van machine learning modellen is een manier om ervoor te zorgen dat ze nauw keurig en op basis van de meest relevante gegevens beschikbaar blijven. In dit artikel wordt uitgelegd hoe u een klassieke studio-webservice (Classic) opnieuw traint. Raadpleeg voor meer informatie over het opnieuw trainen van een nieuwe studio-webservice (klassiek) [dit artikel.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al een experiment met een opnieuw trainen en een voorspellend experiment hebt. Deze stappen worden beschreven in [een machine learning model opnieuw trainen en implementeren.](/azure/machine-learning/studio/retrain-machine-learning-model) In plaats van uw machine learning model te implementeren als een nieuwe webservice, implementeert u echter uw voorspellende experiment als een klassieke webservice.
     
## <a name="add-a-new-endpoint"></a>Een nieuw eind punt toevoegen

De voorspellende webservice die u hebt geïmplementeerd, bevat een standaard Score-eind punt dat is gesynchroniseerd met het oorspronkelijke model trainingen en Score experimenten dat is getraind. Als u de webservice wilt bijwerken naar met een nieuw getraind model, moet u een nieuw Score-eind punt maken.

Er zijn twee manieren waarop u een nieuw eind punt kunt toevoegen aan een webservice:

* Programmatisch
* De Azure Web Services-portal gebruiken

> [!NOTE]
> Zorg ervoor dat u het eind punt toevoegt aan de voorspellende webservice, niet de Web-webservice training. Als u zowel een training als een voorspellende webservice correct hebt geïmplementeerd, ziet u twee afzonderlijke webservices. De voorspellende webservice moet eindigen op ' [Predictive exp.] '.
>

### <a name="programmatically-add-an-endpoint"></a>Programmatisch een eind punt toevoegen

U kunt Score-eind punten toevoegen met behulp van de voorbeeld code die in deze [github-opslag plaats](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint)is opgenomen.

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>De Azure Web Services-portal gebruiken om een eind punt toe te voegen

1. Klik in Machine Learning Studio (klassiek) in de linker navigatie kolom op Web Services.
1. Klik onder aan het dash board van de webservice op **voor beeld van eind punten beheren**.
1. Klik op **Toevoegen**.
1. Typ een naam en beschrijving voor het nieuwe eind punt. Selecteer het logboek registratie niveau en of voorbeeld gegevens zijn ingeschakeld. Zie [logboek registratie inschakelen voor machine learning-webservices](web-services-logging.md)voor meer informatie over logboek registratie.

## <a name="update-the-added-endpoints-trained-model"></a>Het getrainde model van het toegevoegde eind punt bijwerken

### <a name="retrieve-patch-url"></a>De PATCH-URL ophalen

### <a name="option-1-programmatically"></a>Optie 1: programmatisch

Voer de volgende stappen uit om de juiste PATCH-URL via een programma te verkrijgen:

1. Voer de voorbeeld code [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) uit.
1. Zoek de *HelpLocation* -waarde uit de uitvoer van AddEndpoint en kopieer de URL.

   ![HelpLocation in de uitvoer van het addEndpoint-voor beeld.](./media/retrain-classic/addEndpoint-output.png)
1. Plak de URL in een browser om naar een pagina te navigeren die hulp koppelingen voor de webservice biedt.
1. Klik op de koppeling **resource bijwerken** om de Help-pagina voor de patch te openen.

### <a name="option-2-use-the-azure-machine-learning-web-services-portal"></a>Optie 2: de Azure Machine Learning Web Services-portal gebruiken

Volg deze stappen om de juiste PATCH-URL op te halen met behulp van de webportal:

1. Meld u aan bij de [Azure machine learning Web Services](https://services.azureml.net/) -Portal.
1. Klik bovenaan op **Web Services** of **klassieke webservices** .
1. Klik op de Score-webservice waarmee u werkt (als u de standaard naam van de webservice niet hebt gewijzigd, eindigt deze op ' [score exp.] ').
1. Klik op **+ Nieuw**.
1. Nadat het eind punt is toegevoegd, klikt u op de naam van het eind punt.
1. Klik onder de URL van de **patch** op **API Help** om de Help-pagina voor het oplossen van patches te openen.

> [!NOTE]
> Als u het eind punt hebt toegevoegd aan de training-webservice in plaats van de voorspellende webservice, wordt de volgende fout weer gegeven wanneer u op de koppeling **resource bijwerken** klikt: ' Sorry, maar deze functie wordt niet ondersteund of is niet beschikbaar in deze context. Deze webservice heeft geen resources die kunnen worden bijgewerkt. Onze excuses voor het ongemak en werken aan het verbeteren van deze werk stroom. "
>

De Help-pagina voor de PATCH bevat de URL van de PATCH die u moet gebruiken en biedt voorbeeld code die u kunt gebruiken om deze aan te roepen.

![URL van de patch.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Het eind punt bijwerken

U kunt nu het getrainde model gebruiken om het Score-eind punt bij te werken dat u eerder hebt gemaakt.

De volgende voorbeeld code laat zien hoe u de *BaseLocation*, *RelativeLocation*, *SASBLOBTOKEN*en de patch-URL kunt gebruiken om het eind punt bij te werken.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

De *apiKey* en de *endpointUrl* voor de oproep kunnen worden verkregen via het dash board van het eind punt.

De waarde van de para meter *name* in *resources* moet overeenkomen met de resource naam van het opgeslagen getrainde model in het voorspellende experiment. De resource naam ophalen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Klik in het linkermenu op **machine learning**.
1. Klik onder naam op uw werk ruimte en klik vervolgens op **webservices**.
1. Klik onder naam op **optellings model [voorspellend exp.]** .
1. Klik op het nieuwe eind punt dat u hebt toegevoegd.
1. Klik op het dash board eind punt op **resource bijwerken**.
1. Op de pagina update resource API-documentatie voor de webservice vindt u de naam van de **resource** onder bij te **werken resources**.

Als uw SAS-token verloopt voordat u het eind punt hebt bijgewerkt, moet u een GET with-taak-ID uitvoeren om een nieuw token te verkrijgen.

Wanneer de code is uitgevoerd, begint het nieuwe eind punt in ongeveer 30 seconden met het opnieuw getrainde model.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het beheren van webservices of het bijhouden van meerdere experimenten.

* [De Web Services-portal verkennen](manage-new-webservice.md)
* [Experiment herhalingen beheren](manage-experiment-iterations.md)