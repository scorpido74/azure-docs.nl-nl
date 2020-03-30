---
title: Een klassieke webservice omscholen
titleSuffix: ML Studio (classic) - Azure
description: Meer informatie over het omscholen van een model en het bijwerken van een klassieke webservice om het nieuw getrainde model in Azure Machine Learning Studio (klassiek) te gebruiken.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: peterclu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: 8094d64eab1a4b25a76554bf9eb6848c2e4d3493
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204236"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Een klassieke Studio (klassieke) webservice omscholen en implementeren

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Het omscholen van machine learning-modellen is een manier om ervoor te zorgen dat ze nauwkeurig blijven en gebaseerd zijn op de meest relevante gegevens die beschikbaar zijn. Dit artikel laat je zien hoe je een klassieke Studio (klassieke) webservice omscholen. Voor een gids over het omscholen van een nieuwe Studio (klassieke) webservice, [bekijk dit how-to artikel.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al een omscholingsexperiment en een voorspellend experiment hebt. Deze stappen worden uitgelegd in [Retrain en implementeren van een machine learning-model.](/azure/machine-learning/studio/retrain-machine-learning-model) In plaats van uw machine learning-model als nieuwe webservice te implementeren, implementeert u uw voorspellende experiment echter als een klassieke webservice.
     
## <a name="add-a-new-endpoint"></a>Een nieuw eindpunt toevoegen

De voorspellende webservice die u hebt geïmplementeerd, bevat een standaardscoreeindpunt dat synchroon wordt gehouden met het oorspronkelijke getrainde trainings- en scoringsexperimenten-model. Als u uw webservice wilt bijwerken met een nieuw getraind model, moet u een nieuw scoreeindpunt maken.

Er zijn twee manieren waarop u een nieuw eindpunt aan een webservice toevoegen:

* Programmatisch
* De Azure Web Services-portal gebruiken

> [!NOTE]
> Zorg ervoor dat u het eindpunt toevoegt aan de Predictive Web Service, niet aan de WebService Training. Als u zowel een training als een Predictive Web Service correct hebt geïmplementeerd, worden twee afzonderlijke webservices weergegeven. De Predictive Web Service moet eindigen met "[predictive exp.]".
>

### <a name="programmatically-add-an-endpoint"></a>Programmatisch een eindpunt toevoegen

U scoreeindpunten toevoegen met behulp van de voorbeeldcode in deze [GitHub-opslagplaats.](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint)

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>De Azure Web Services-portal gebruiken om een eindpunt toe te voegen

1. Klik in Machine Learning Studio (klassiek) in de linkernavigatiekolom op Webservices.
1. Klik onder aan het dashboard van de webservice op **Voorbeeld van eindpunten beheren**.
1. Klik op**toevoegen**.
1. Typ een naam en beschrijving voor het nieuwe eindpunt. Selecteer het logboekniveau en of voorbeeldgegevens zijn ingeschakeld. Zie [Logboekregistratie inschakelen voor Machine Learning-webservices voor](web-services-logging.md)meer informatie over logboekregistratie.

## <a name="update-the-added-endpoints-trained-model"></a>Het getrainde model van het toegevoegde eindpunt bijwerken

### <a name="retrieve-patch-url"></a>PATCH-URL ophalen

Volg de volgende stappen om de juiste PATCH-URL te krijgen via de webportal:

1. Meld u aan bij de [Azure Machine Learning Web Services-portal.](https://services.azureml.net/)
1. Klik bovenaan op **WebServices** of **Classic Web Services.**
1. Klik op de scorewebservice waarmee u werkt (als u de standaardnaam van de webservice niet hebt gewijzigd, eindigt deze in '[Scoreexp.]").
1. Klik **op +NIEUW**.
1. Nadat het eindpunt is toegevoegd, klikt u op de naam van het eindpunt.
1. Klik onder de **PATCH-URL** op **API Help** om de helppagina voor patchen te openen.

> [!NOTE]
> Als u het eindpunt aan de Webservice Training hebt toegevoegd in plaats van de Predictive Web Service, ontvangt u de volgende fout wanneer u op de koppeling **Resource bijwerken** klikt: 'Sorry, maar deze functie wordt in deze context niet ondersteund of beschikbaar. Deze webservice heeft geen updatable resources. Onze excuses voor het ongemak en werken aan het verbeteren van deze workflow."
>

De helppagina PATCH bevat de URL van patch die u moet gebruiken en bevat voorbeeldcode die u gebruiken om deze te bellen.

![Patch-URL.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Het eindpunt bijwerken

U nu het getrainde model gebruiken om het scoreeindpunt bij te werken dat u eerder hebt gemaakt.

In de volgende voorbeeldcode ziet u hoe u de *URL BaseLocation*, *RelativeLocation*, *SasBlobToken*en PATCH gebruiken om het eindpunt bij te werken.

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
                        RelativeLocation = "your endpoint relative location", //from the output, for example: "experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner"
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: "?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl"
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

De *apiKey* en de *endpointUrl* voor de call zijn verkrijgbaar via het endpoint-dashboard.

De waarde van de parameter *Naam* in *Resources* moet overeenkomen met de resourcenaam van het opgeslagen getrainde model in het voorspellende experiment. Ga als een te gekeer naar de naam van de bron:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik in het linkermenu op **Machine learning**.
1. Klik onder Naam op uw werkruimte en klik vervolgens op **Webservices**.
1. Klik onder Naam op **Census Model [predictive exp.]**.
1. Klik op het nieuwe eindpunt dat u hebt toegevoegd.
1. Klik op het eindpuntdashboard op **Bron bijwerken**.
1. Op de pagina Bron-API-documentatie bijwerken voor de webservice u de **bronnaam** vinden onder **Updatable Resources**.

Als uw SAS-token verloopt voordat u het eindpunt hebt bijgewerkt, moet u een GET met de taak-id uitvoeren om een nieuw token te verkrijgen.

Wanneer de code is uitgevoerd, moet het nieuwe eindpunt het omscholingsmodel in ongeveer 30 seconden gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het beheren van webservices of het bijhouden van meerdere experimenten:

* [Ontdek de Web Services-portal](manage-new-webservice.md)
* [Experimentherhalingen beheren](manage-experiment-iterations.md)