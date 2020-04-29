---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 3c6059e131eadf1144fd189c47691b2352176745
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75446416"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Formulieren analyseren voor sleutel-waardeparen en tabellen

Vervolgens gebruikt u uw pas getrainde model voor het analyseren van een document en het extra heren van sleutel-waardeparen en tabellen. Roep de **[analyse formulier](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** -API aan door de volgende code uit te voeren in een nieuw python-script. Voordat u het script uitvoert, moet u de volgende wijzigingen aanbrengen:

1. Vervang `<file path>` door het bestandspad van uw formulier (bijvoorbeeld C:\temp\file.PDF). Dit kan ook de URL zijn van een extern bestand. Voor deze Quick Start kunt u de bestanden in de map **test** van de [set met voorbeeld gegevens](https://go.microsoft.com/fwlink/?linkid=2090451)gebruiken.
1. Vervang `<model_id>` door de model-id die u in de vorige sectie hebt ontvangen.
1. Vervang `<endpoint>` door het eind punt dat u hebt verkregen met de abonnements sleutel voor uw formulier herkenning. U vindt deze op het tabblad **overzicht** van resource Recognizer.
1. Vervang `<file type>` door het bestands type. Ondersteunde typen: `application/pdf`, `image/jpeg`, `image/png`, `image/tiff`.
1. Vervang `<subscription key>` door uw abonnementssleutel.

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. Sla de code op in een bestand met de extensie. py. Bijvoorbeeld *Form-Recognizer-analyze.py*.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python form-recognizer-analyze.py`.

Wanneer u de **analyse formulier** -API aanroept, ontvangt u `201 (Success)` een antwoord met een **bewerkings locatie-** header. De waarde van deze header is een ID die u gebruikt om de resultaten van de analyse bewerking bij te houden. Het bovenstaande script drukt de waarde van deze header af naar de-console.

## <a name="get-the-analyze-results"></a>De resultaten analyseren

Voeg de volgende code toe onder aan het python-script. Dit maakt gebruik van de ID-waarde van de vorige aanroep in een nieuwe API-aanroep om de analyse resultaten op te halen. De bewerking **formulier analyseren** is asynchroon, dus de API wordt met regel matige tussen pozen door dit script aangeroepen totdat de resultaten beschikbaar zijn. We raden een interval van één seconde of meer aan.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
