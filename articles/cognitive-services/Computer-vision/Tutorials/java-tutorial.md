---
title: Bewerkingen op een afbeelding uitvoeren - Java
titleSuffix: Azure Cognitive Services
description: Ontdek een eenvoudige Java Swing-app die gebruikmaakt van de Computer Vision-API in Azure Cognitive Services. Voer OCR uit, maak miniaturen en werk met visuele kenmerken in een afbeelding.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.author: kefre
ms.custom: seodec18
ms.date: 04/30/2019
ms.openlocfilehash: 4fdafee622a67582787829bb63331a8d0225506f
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70137495"
---
# <a name="use-computer-vision-features-with-the-rest-api-and-java"></a>Computer Vision functies gebruiken met de REST API en Java

In deze zelfstudie leert u de functies van de Computer Vision-REST API in Azure Cognitive Services.

Ontdek een Java Swing-toepassing die de Computer Vision-REST API gebruikt om optische tekenherkenning (OCR) uit te voeren, slim bijgesneden miniaturen te maken en visuele kenmerken (inclusief gezichten) in een afbeelding te herkennen, categoriseren, labelen en beschrijven. In dit voorbeeld kunt u een afbeeldings-URL verzenden voor analyse en verwerking. U kunt dit open source-voorbeeld gebruiken als sjabloon bij het bouwen van uw eigen Java-app die gebruikmaakt van de Computer Vision-REST API.

In deze zelfstudie wordt uitgelegd hoe u Computer Vision kunt gebruiken voor de volgende taken:

> [!div class="checklist"]
> * Een afbeelding analyseren
> * Een natuurlijk of kunstmatig oriëntatiepunt in een afbeelding identificeren
> * Een beroemdheid in een afbeelding identificeren
> * Een hoogwaardige miniatuur op basis van een afbeelding maken
> * Gedrukte tekst in een afbeelding lezen (OCR)
> * Handgeschreven tekst in een afbeelding lezen (OCR)

De Java-formulier toepassing voor Swinging is al geschreven, maar heeft geen functionaliteit. In deze zelfstudie kunt u de specifieke code toevoegen aan de Computer Vision-REST API om de functionaliteit van de toepassing te voltooien.

## <a name="prerequisites"></a>Vereisten

### <a name="platform-requirements"></a>Platformvereisten

Deze zelfstudie is ontwikkeld met de NetBeans-IDE. Hierbij gaat het om precies te zijn om de **Java SE**-versie van NetBeans, die u hier [ kunt downloaden](https://netbeans.org/downloads/index.html).

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Abonneren op Computer Vision-API en een abonnementssleutel ophalen

Voordat u het voor beeld maakt, moet u een abonnements sleutel voor Computer Vision hebben. U kunt een gratis proef versie verkrijgen van [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Of volg de instructies in [Create a cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op computer vision en uw sleutel op te halen. Noteer ook de URL van het service-eind punt.

## <a name="acquire-incomplete-tutorial-project"></a>Onvolledige zelfstudie project ophalen

### <a name="download-the-project"></a>Het project downloaden

1. Ga naar de opslagplaats [Cognitive Services Java Computer Vision Tutorial](https://github.com/Azure-Samples/cognitive-services-java-computer-vision-tutorial).
1. Klik op de knop **Klonen of Downloaden**.
1. Klik op **ZIP downloaden** om een ZIP-bestand van het zelfstudieproject te downloaden.

Het is niet nodig om de inhoud van het ZIP-bestand uit te pakken omdat NetBeans het project vanuit het ZIP-bestand importeert.

### <a name="import-the-tutorial-project"></a>Het zelfstudieproject importeren

Importeer het bestand **cognitive-services-java-computer-vision-tutorial-master.zip** in NetBeans.

1. Klik in NetBeans op **Bestand** > **Project importeren** > **Vanuit ZIP...** . Het dialoogvenster **Project(en) importeren vanuit ZIP** verschijnt.
1. Klik in het veld **ZIP-bestand** op de knop **Bladeren** om het bestand **cognitive-services-java-computer-vision-tutorial-master.zip** te zoeken, en klik vervolgens op **Openen**.
1. Klik op **Importeren** in het dialoogvenster **Project(en) importeren vanuit ZIP**.
1. Vouw in het venster **Projecten** het item **ComputerVision** > **Bronpakketten** >  **&lt;standaardpakket&gt;** uit. 
   Sommige versies van NetBeans gebruiken **src** in plaats van **Bronpakketten** >  **&lt;standaardpakket&gt;** . Vouw in dat geval **src** uit.
1. Dubbelklik op **MainFrame.java** om het bestand in de NetBeans-editor te laden. Het tabblad **Ontwerpen** van het bestand **MainFrame.java** verschijnt.
1. Klik op het tabblad **Bron** om de Java-broncode te bekijken.

### <a name="build-and-run-the-tutorial-project"></a>Het zelfstudieproject compileren en uitvoeren

1. Druk op **F6** om de zelfstudietoepassing te compileren en uit te voeren.

    Klik in de zelfstudietoepassing op een tabblad om het deelvenster voor de bijbehorende functie te openen. De knoppen hebben lege methoden, dus ze doen niets.

    Onderaan het venster staan de velden **Abonnementssleutel** en **Abonnementsregio**. Deze velden moeten worden ingevuld met een geldige abonnementssleutel en de juiste regio voor de betreffende abonnementssleutel. Als u een abonnementssleutel wilt ontvangen, gaat u naar [Abonnementen](https://azure.microsoft.com/try/cognitive-services/). Als u uw abonnementssleutel hebt ontvangen via de gratis proefversie onder bovenstaande koppeling, is de standaardregio **westcentralus** de juiste regio voor uw abonnementssleutels.

1. Sluit de zelfstudietoepassing af.

## <a name="add-tutorial-code-to-the-project"></a>Zelfstudie code toevoegen aan het project

De Java Swing-toepassing bestaat uit zes tabbladen. Elk tabblad toont een andere functie van Computer Vision (analyse, OCR, enzovoort). De zes zelfstudiesecties zijn niet onderling afhankelijk, dus u kunt één sectie, alle zes de secties of een subset toevoegen. U kunt de secties in elke gewenste volgorde toevoegen.

### <a name="analyze-an-image"></a>Een afbeelding analyseren

De functie Analyseren van Computer Vision scant een afbeelding op meer dan 2000 herkenbare objecten, levende wezens, landschappen en acties. Zodra de analyse is voltooid, retourneert Analyseren een JSON-object dat de afbeelding beschrijft met labels, kleuranalyse, bijschriften en meer.

Gebruik de volgende stappen uit om de functie Analyseren van de zelfstudietoepassing uit te voeren:

#### <a name="add-the-event-handler-code-for-the-analyze-button"></a>De code van de gebeurtenis-handler voor de analyse knop toevoegen

De gebeurtenis-handlermethode **analyzeImageButtonActionPerformed** wist het formulier, geeft de in de URL opgegeven afbeelding weer en roept vervolgens de methode **AnalyzeImage** aan om de afbeelding te analyseren. Wanneer **AnalyzeImage** terugkeert, geeft de methode het opgemaakte JSON-antwoord weer in het tekstgebied **Antwoord**. De methode haalt vervolgens het eerste bijschrift uit het **JSONObject** en geeft het bijschrift weer, samen met het betrouwbaarheidskans dat het bijschrift juist is.

Kopieer en plak de volgende code in de methode **analyzeImageButtonActionPerformed**.

> [!NOTE]
> In NetBeans kunt u niet plakken op de definitieregel van de methode (```private void```) of op de sluitende accolade van die methode. Om de code te kopiëren, kopieert u de regels tussen de methodedefinitie en de afsluitende accolade, en plakt u deze over de inhoud van de methode.

```java
    private void analyzeImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL analyzeImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        analyzeImage.setIcon(new ImageIcon());
        analyzeCaptionLabel.setText("");
        analyzeResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            analyzeImageUrl = new URL(analyzeImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(analyzeImageUrl);
            scaleAndShowImage(bImage, analyzeImage);
        } catch(IOException e) {
            analyzeResponseTextArea.setText("Error loading Analyze image: " + e.getMessage());
            return;
        }
        
        // Analyze the image.
        JSONObject jsonObj = AnalyzeImage(analyzeImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        analyzeResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("description") && jsonObj.getJSONObject("description").has("captions")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("description").getJSONArray("captions").getJSONObject(0);
            
            if (jsonCaption.has("text") && jsonCaption.has("confidence")) {
                
                analyzeCaptionLabel.setText("Caption: " + jsonCaption.getString("text") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>De wrapper voor de REST API-aanroep toevoegen

De methode **AnalyzeImage** verpakt de REST-API-aanroep om een afbeelding te analyseren. De methode retourneert een **JSONObject** met een beschrijving van de afbeelding, of **null** als er een fout is opgetreden.

Kopieer en plak de methode **AnalyzeImage** naar een positie net onder de methode **analyzeImageButtonActionPerformed**.

```java
    /**
     * Encapsulates the Microsoft Cognitive Services REST API call to analyze an image.
     * @param imageUrl: The string URL of the image to analyze.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject AnalyzeImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call for Analyze Image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseAnalyze;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color,Adult");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
 ```

#### <a name="run-the-analyze-function"></a>De functie analyseren uitvoeren

Druk op **F6** om de toepassing uit te voeren. Plaats uw abonnementssleutel in het veld **Abonnementssleutel** en controleer in **Abonnementregio** of u de juiste regio gebruikt. Voer een URL in naar een afbeelding die u wilt analyseren en klik vervolgens op de knop **Afbeelding analyseren** om de afbeelding te analyseren en het resultaat te bekijken.

### <a name="recognize-a-landmark"></a>Een oriëntatiepunten herkennen

Met de functie Oriëntatiepunten van Computer Vision wordt een afbeelding geanalyseerd op natuurlijke en kunstmatige oriëntatiepunten, zoals bergen of beroemde gebouwen. Wanneer de analyse is voltooid, retourneert de functie Oriëntatiepunt een JSON-object waarin de in de afbeelding gevonden oriëntatiepunten zijn geïdentificeerd.

Gebruik de volgende stappen uit om de functie Oriëntatiepunten van de zelfstudietoepassing uit te voeren:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>De code van de gebeurtenis-handler toevoegen voor de formulierknop

De gebeurtenis-handlermethode **landmarkImageButtonActionPerformed** wist het formulier, geeft de in de URL opgegeven afbeelding weer en roept vervolgens de methode **LandmarkImage** aan om de afbeelding te analyseren. Wanneer **LandmarkImage** terugkeert, geeft de methode het opgemaakte JSON-antwoord weer in het tekstgebied **Antwoord** weer. De methode haalt vervolgens de eerste naam van een oriëntatiepunt uit het **JSONObject** en toont deze in het venster, samen met het betrouwbaarheidskans dat het oriëntatiepunt correct is geïdentificeerd.

Kopieer en plak de volgende code in de methode **landmarkImageButtonActionPerformed**.

> [!NOTE]
> In NetBeans kunt u niet plakken op de definitieregel van de methode (```private void```) of op de sluitende accolade van die methode. Om de code te kopiëren, kopieert u de regels tussen de methodedefinitie en de afsluitende accolade, en plakt u deze over de inhoud van de methode.

```java
    private void landmarkImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL landmarkImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        landmarkImage.setIcon(new ImageIcon());
        landmarkCaptionLabel.setText("");
        landmarkResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            landmarkImageUrl = new URL(landmarkImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(landmarkImageUrl);
            scaleAndShowImage(bImage, landmarkImage);
        } catch(IOException e) {
            landmarkResponseTextArea.setText("Error loading Landmark image: " + e.getMessage());
            return;
        }
        
        // Identify the landmark in the image.
        JSONObject jsonObj = LandmarkImage(landmarkImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        landmarkResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("landmarks")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("landmarks").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                landmarkCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>De wrapper voor de REST API-aanroep toevoegen

De methode **LandmarkImage** verpakt de REST-API-aanroep om een afbeelding te analyseren. De methode retourneert een **JSONObject** met een beschrijving van de gevonden oriëntatiepunten in de afbeelding, of **null** als er een fout is opgetreden.

Kopieer en plak de methode **LandmarkImage** naar een positie net onder de methode **landmarkImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify a landmark in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject LandmarkImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify a Landmark in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseLandmark;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-landmark-function"></a>De functie oriëntatie punt uitvoeren

Druk op **F6** om de toepassing uit te voeren. Plaats uw abonnementssleutel in het veld **Abonnementssleutel** en controleer in **Abonnementregio** of u de juiste regio gebruikt. Klik op het tabblad **Oriëntatiepunt**, voer een URL naar een afbeelding van een oriëntatiepunt in, klik vervolgens op de knop **afbeelding analyseren** om een afbeelding te analyseren, en bekijk het resultaat.

### <a name="recognize-celebrities"></a>Beroemdheden herkennen

De functie Beroemdheden van de Computer Vision analyseert een afbeelding op beroemde mensen. Wanneer de analyse is voltooid, retourneert de functie Beroemdheden een JSON-object waarin de in de afbeelding gevonden beroemdheden zijn geïdentificeerd.

Gebruik de volgende stappen uit om de functie Beroemdheden van de zelfstudietoepassing uit te voeren:

#### <a name="add-the-event-handler-code-for-the-celebrities-button"></a>De code van de gebeurtenis-handler voor de knop beroemdheden toevoegen

De gebeurtenis-handlermethode **celebritiesImageButtonActionPerformed** wist het formulier, geeft de in de URL opgegeven afbeelding weer en roept vervolgens de methode **CelebritiesImage** aan om de afbeelding te analyseren. Wanneer **CelebritiesImage** terugkeert, geeft de methode het opgemaakte JSON-antwoord weer in het tekstgebied **Antwoord** weer. De methode haalt vervolgens de eerste naam van een beroemdheid uit het **JSONObject** en toont deze in het venster, samen met het betrouwbaarheidskans dat de beroemdheid correct is geïdentificeerd.

Kopieer en plak de volgende code in de methode **celebritiesImageButtonActionPerformed**.

> [!NOTE]
> In NetBeans kunt u niet plakken op de definitieregel van de methode (```private void```) of op de sluitende accolade van die methode. Om de code te kopiëren, kopieert u de regels tussen de methodedefinitie en de afsluitende accolade, en plakt u deze over de inhoud van de methode.

```java
    private void celebritiesImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL celebritiesImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        celebritiesImage.setIcon(new ImageIcon());
        celebritiesCaptionLabel.setText("");
        celebritiesResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            celebritiesImageUrl = new URL(celebritiesImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(celebritiesImageUrl);
            scaleAndShowImage(bImage, celebritiesImage);
        } catch(IOException e) {
            celebritiesResponseTextArea.setText("Error loading Celebrity image: " + e.getMessage());
            return;
        }
        
        // Identify the celebrities in the image.
        JSONObject jsonObj = CelebritiesImage(celebritiesImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        celebritiesResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("celebrities")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("celebrities").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                celebritiesCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>De wrapper voor de REST API-aanroep toevoegen

De methode **CelebritiesImage** verpakt de REST-API-aanroep om een afbeelding te analyseren. De methode retourneert een **JSONObject** met een beschrijving van de gevonden beroemdheden in de afbeelding, of **null** als er een fout is opgetreden.

Kopieer en plak de methode **CelebritiesImage** naar een positie net onder de methode **celebritiesImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify celebrities in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject CelebritiesImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseCelebrities;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-celebrities-function"></a>De functie beroemdheden uitvoeren

Druk op **F6** om de toepassing uit te voeren. Plaats uw abonnementssleutel in het veld **Abonnementssleutel** en controleer in **Abonnementregio** of u de juiste regio gebruikt. Klik op het tabblad **Beroemdheden**, voer een URL naar een afbeelding van een beroemdheid in, klik vervolgens op de knop **Afbeelding analyseren** om een afbeelding te analyseren, en bekijk het resultaat.

### <a name="intelligently-generate-a-thumbnail"></a>Intelligent een miniatuur genereren

Met de functie Miniatuur van Computer Vision wordt een miniatuur van een afbeelding gegenereerd. De functie Miniatuur identificeert het interessantste gebied in een afbeelding en centreert de miniatuur op dit gebied. Vervolgens wordt de functie **Slim bijsnijden** gebruikt om een zo fraai mogelijke miniatuurafbeelding te genereren.

Gebruik de volgende stappen uit om de functie Miniatuur van de zelfstudietoepassing uit te voeren:

#### <a name="add-the-event-handler-code-for-the-thumbnail-button"></a>De code van de gebeurtenis-handler voor de knop miniatuur toevoegen

De gebeurtenis-handlermethode **thumbnailImageButtonActionPerformed** wist het formulier, geeft de in de URL opgegeven afbeelding weer en roept vervolgens de methode **getThumbnailImage** aan om de miniatuur te maken. Wanneer **getThumbnailImage** terugkeert, geeft de methode de gegenereerde miniatuur weer.

Kopieer en plak de volgende code in de methode **thumbnailImageButtonActionPerformed**.

> [!NOTE]
> In NetBeans kunt u niet plakken op de definitieregel van de methode (```private void```) of op de sluitende accolade van die methode. Om de code te kopiëren, kopieert u de regels tussen de methodedefinitie en de afsluitende accolade, en plakt u deze over de inhoud van de methode.

```java
    private void thumbnailImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL thumbnailImageUrl;
        JSONObject jsonError[] = new JSONObject[1];
        
        // Clear out the previous image, response, and thumbnail, if any.
        thumbnailSourceImage.setIcon(new ImageIcon());
        thumbnailResponseTextArea.setText("");
        thumbnailImage.setIcon(new ImageIcon());

        // Display the image specified in the text box.
        try {
            thumbnailImageUrl = new URL(thumbnailImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(thumbnailImageUrl);
            scaleAndShowImage(bImage, thumbnailSourceImage);
        } catch(IOException e) {
            thumbnailResponseTextArea.setText("Error loading image to thumbnail: " + e.getMessage());
            return;
        }
        
        // Get the thumbnail for the image.
        BufferedImage thumbnail = getThumbnailImage(thumbnailImageUrl.toString(), jsonError);
        
        // A non-null value indicates error.
        if (jsonError[0] != null) {
            // Format and display the JSON error.
            thumbnailResponseTextArea.setText(jsonError[0].toString(2));
            return;
        }
        
        // Display the thumbnail.
        if (thumbnail != null) {
            scaleAndShowImage(thumbnail, thumbnailImage);
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>De wrapper voor de REST API-aanroep toevoegen

De methode **getThumbnailImage** verpakt de REST-API-aanroep om een afbeelding te analyseren. De methode retourneert een **BufferedImage** die de miniatuur bevat, of **null** als er een fout is opgetreden. De foutmelding wordt geretourneerd in het eerste element van de **jsonError**-tekenreeksmatrix.

Kopieer en plak de volgende methode **getThumbnailImage** naar een positie net onder de methode **thumbnailImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to create a thumbnail for an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A BufferedImage containing the thumbnail, or null if a runtime error occurs. In the case
     * of an error, the error message will be returned in the first element of the jsonError string array.
     */
    private BufferedImage getThumbnailImage(String imageUrl, JSONObject[] jsonError) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseThumbnail;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(requestEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200)
            {
                // Return the thumbnail.
                return ImageIO.read(entity.getContent());
            }
            else
            {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                jsonError[0] = new JSONObject(jsonString);
                return null;
            }
        }
        catch (Exception e)
        {
            String errorMessage = e.getMessage();
            System.out.println(errorMessage);
            jsonError[0] = new JSONObject(errorMessage);
            return null;
        }
    }
```

#### <a name="run-the-thumbnail-function"></a>De functie thumbnail uitvoeren

Druk op **F6** om de toepassing uit te voeren. Plaats uw abonnementssleutel in het veld **Abonnementssleutel** en controleer in **Abonnementregio** of u de juiste regio gebruikt. Klik op het tabblad **Miniatuur**, voer een URL in naar een afbeelding, klik vervolgens op de knop **Miniatuur genereren** om een afbeelding te analyseren, en bekijk het resultaat.

### <a name="read-printed-text-ocr"></a>Gedrukte tekst lezen (OCR)

De functie voor optische tekenherkenning (OCR) van Computer Vision analyseert een afbeelding van gedrukte tekst. Nadat de analyse is voltooid is, retourneert OCR een JSON-object met de tekst en de locatie van de tekst in de afbeelding.

Gebruik de volgende stappen uit om de functie OCR van de zelfstudietoepassing uit te voeren:

#### <a name="add-the-event-handler-code-for-the-ocr-button"></a>De code van de gebeurtenis-handler voor de OCR-knop toevoegen

De gebeurtenis-handlermethode **ocrImageButtonActionPerformed** wist het formulier, geeft de in de URL opgegeven afbeelding weer en roept vervolgens de methode **OcrImage** aan om de afbeelding te analyseren. Wanneer **OcrImage** terugkeert, geeft de methode de herkende tekst weer als opgemaakte JSON in het tekstgebied **Antwoord**.

Kopieer en plak de volgende code in de methode **ocrImageButtonActionPerformed**.

> [!NOTE]
> In NetBeans kunt u niet plakken op de definitieregel van de methode (```private void```) of op de sluitende accolade van die methode. Om de code te kopiëren, kopieert u de regels tussen de methodedefinitie en de afsluitende accolade, en plakt u deze over de inhoud van de methode.

```java
    private void ocrImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL ocrImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        ocrImage.setIcon(new ImageIcon());
        ocrResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            ocrImageUrl = new URL(ocrImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(ocrImageUrl);
            scaleAndShowImage(bImage, ocrImage);
        } catch(IOException e) {
            ocrResponseTextArea.setText("Error loading OCR image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = OcrImage(ocrImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        ocrResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>De wrapper voor de REST API-aanroep toevoegen

De methode **OcrImage** verpakt de REST-API-aanroep om een afbeelding te analyseren. De methode retourneert een **JSONObject** van de JSON-gegevens die op basis van de oproep zijn geretourneerd, of **null** als er een fout is opgetreden.

Kopieer en plak de volgende methode **OcrImage** naar een positie net onder de methode **ocrImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject OcrImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseOcr;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation ", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-ocr-function"></a>De OCR-functie uitvoeren

Druk op **F6** om de toepassing uit te voeren. Plaats uw abonnementssleutel in het veld **Abonnementssleutel** en controleer in **Abonnementregio** of u de juiste regio gebruikt. Klik op het tabblad **OCR**, voer een URL in naar een afbeelding van gedrukte tekst, klik vervolgens op de knop **Afbeelding lezen** om de afbeelding te analyseren, en bekijk het resultaat.

### <a name="read-handwritten-text-handwriting-recognition"></a>Handgeschreven tekst lezen (handschriftherkenning)

De functie Handschriftherkenning van Computer Vision analyseert een afbeelding van handgeschreven tekst. Nadat de analyse is voltooid is, retourneert Handschriftherkenning een JSON-object met de tekst en de locatie van de tekst in de afbeelding.

Gebruik de volgende stappen uit om de functie Handschriftherkenning van de zelfstudietoepassing uit te voeren:

#### <a name="add-the-event-handler-code-for-the-handwriting-button"></a>De code van de gebeurtenis-handler voor de knop voor handgeschreven berichten toevoegen

De gebeurtenis-handlermethode **handwritingImageButtonActionPerformed** wist het formulier, geeft de in de URL opgegeven afbeelding weer en roept vervolgens de methode **HandwritingImage** aan om de afbeelding te analyseren. Wanneer **HandwritingImage** terugkeert, geeft de methode de herkende tekst weer als opgemaakte JSON in het tekstgebied **Antwoord**.

Kopieer en plak de volgende code in de methode **handwritingImageButtonActionPerformed**.

> [!NOTE]
> In NetBeans kunt u niet plakken op de definitieregel van de methode (```private void```) of op de sluitende accolade van die methode. Om de code te kopiëren, kopieert u de regels tussen de methodedefinitie en de afsluitende accolade, en plakt u deze over de inhoud van de methode.

```java
    private void handwritingImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL handwritingImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        handwritingImage.setIcon(new ImageIcon());
        handwritingResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            handwritingImageUrl = new URL(handwritingImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(handwritingImageUrl);
            scaleAndShowImage(bImage, handwritingImage);
        } catch(IOException e) {
            handwritingResponseTextArea.setText("Error loading Handwriting image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = HandwritingImage(handwritingImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        handwritingResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>De wrapper voor de REST API-aanroep toevoegen

De methode **HandwritingImage** verpakt de twee REST API-aanroepen die nodig zijn om een afbeelding te analyseren. Aangezien handschriftherkenning een tijdrovend proces is, wordt het proces in twee stappen uitgevoerd. Bij de eerste aanroep wordt de afbeelding verzonden voor verwerking en bij de tweede aanroep wordt de gedetecteerde tekst opgehaald wanneer de verwerking is voltooid.

Nadat de tekst is opgehaald, retourneert de methode **HandwritingImage** een **JSONObject** met een beschrijving van de tekst en de locaties van de tekst, of **null** als er een fout is opgetreden.

Kopieer en plak de volgende methode **HandwritingImage** naar een positie net onder de methode **handwritingImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read handwritten text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject HandwritingImage(String imageUrl) {
        try (CloseableHttpClient textClient = HttpClientBuilder.create().build();
             CloseableHttpClient resultClient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseHandwriting;
            URIBuilder uriBuilder = new URIBuilder(uriString);
            
            // Request parameters.
            uriBuilder.setParameter("handwriting", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response.
            HttpResponse textResponse = textClient.execute(request);
            
            // Check for success.
            if (textResponse.getStatusLine().getStatusCode() != 202) {
                // An error occurred. Return the JSON error message.
                HttpEntity entity = textResponse.getEntity();
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            }
            
            String operationLocation = null;

            // The 'Operation-Location' in the response contains the URI to retrieve the recognized text.
            Header[] responseHeaders = textResponse.getAllHeaders();
            for(Header header : responseHeaders) {
                if(header.getName().equals("Operation-Location"))
                {
                    // This string is the URI where you can get the text recognition operation result.
                    operationLocation = header.getValue();
                    break;
                }
            }
            
            // NOTE: The response may not be immediately available. Handwriting recognition is an
            // async operation that can take a variable amount of time depending on the length
            // of the text you want to recognize. You may need to wait or retry this operation.
            //
            // This example checks once per second for ten seconds.
            
            JSONObject responseObj = null;
            int i = 0;
            do {
                // Wait one second.
                Thread.sleep(1000);
                
                // Check to see if the operation completed.
                HttpGet resultRequest = new HttpGet(operationLocation);
                resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());
                HttpResponse resultResponse = resultClient.execute(resultRequest);
                HttpEntity responseEntity = resultResponse.getEntity();
                if (responseEntity != null)
                {
                    // Get the JSON response.
                    String jsonString = EntityUtils.toString(responseEntity);
                    responseObj = new JSONObject(jsonString);
                }
            }
            while (i < 10 && responseObj != null &&
                    !responseObj.getString("status").equalsIgnoreCase("Succeeded"));
            
            // If the operation completed, return the JSON object.
            if (responseObj != null) {
                return responseObj;
            } else {
                // Return null for timeout error.
                System.out.println("Timeout error.");
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-handwriting-function"></a>De functie voor hand schrift uitvoeren

Druk op **F6** om de toepassing uit te voeren. Plaats uw abonnementssleutel in het veld **Abonnementssleutel** en controleer in **Abonnementregio** of u de juiste regio gebruikt. Klik op het tabblad **Handgeschreven tekst lezen**, voer een URL in naar een afbeelding van handgeschreven tekst, klik vervolgens op de knop **Afbeelding lezen** om een afbeelding te analyseren, en bekijk het resultaat.

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u de Computer Vision REST API met Java gebruikt om veel van de beschik bare functies voor het analyseren van installatie kopieën te testen. Raadpleeg vervolgens de referentie documentatie voor meer informatie over de betrokken Api's.

- [Computer Vision REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)
