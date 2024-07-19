---
title: Hantera brev om anställningserbjudande
description: Lär dig hur du genererar ett erbjudandebrev som kan levereras till en ny anställd för signering
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8096
thumbnail: KT-8096.jpg
exl-id: 92f955f0-add5-4570-aa3a-ea63055dadb2
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1714'
ht-degree: 0%

---

# Hantera erbjudandebrev för medarbetare

![Banderoll för användningsfall](assets/UseCaseOfferHero.jpg)

Erbjudandebrev till anställda är en av de första upplevelser som anställda har med din organisation. Därför vill du försäkra dig om att erbjudandebreven följer varumärket, men du vill inte behöva bygga en bokstav i ordbehandlingsprogrammet från början varje gång. [!DNL Adobe Acrobat Services] API:er erbjuder ett snabbt, enkelt och effektivt sätt att hantera viktiga delar av [generera och leverera erbjudandebrev till nya medarbetare](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html).

## Vad du kan lära dig

Den här praktiska självstudiekursen går igenom hur du konfigurerar ett Node Express-projekt som visar ett webbformulär som en användare kan fylla i med information om anställda. De här uppgifterna använder [!DNL Acrobat Services] på webben för att generera ett erbjudandebrev som PDF som kan levereras till en kund för signering med Adobe Sign API.

## Relevanta API:er och resurser

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Adobe-dokumentgenererings-API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Tillägg för taggningsord för dokumentgenerering](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin)

* [Projektexempel](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html)

## Komma igång

[Node.js](https://nodejs.org/) är programmeringsplattformen. Den levereras med en enorm uppsättning bibliotek, såsom Express-webbservern. [Hämta Node.js](https://nodejs.org/en/download/) och följ stegen för att installera den här fantastiska utvecklingsmiljön med öppen källkod.

Om du vill använda Adobe-dokumentgenererings-API:t i Node.js går du till webbplatsen [Dokumentgenererings-API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html) för att få åtkomst till ditt konto eller registrera dig för ett nytt. Ditt konto är [kostnadsfritt i sex månader. Sedan kan du betala löpande](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) för bara $0,05 per dokumenttransaktion, så du kan testa det riskfritt och sedan bara betala när företaget växer.

När du har loggat in på [Adobe Developer Console](https://console.adobe.io/) klickar du på **[!UICONTROL Skapa nytt projekt]**. Projektet heter som standard &quot;Projekt 1&quot;. Klicka på knappen **[!UICONTROL Redigera projektet]** och ändra namnet till &quot;Generator för erbjudandebrev&quot;. I mitten av skärmen finns avsnittet **[!UICONTROL Kom igång med ditt nya projekt]**. Så här aktiverar du säkerhet i ett projekt:

Klicka på **Lägg till API**. Du ser ett antal API:er att välja mellan. I avsnittet **[!UICONTROL Filtrera efter produkt]** väljer du **[!UICONTROL Document Cloud]** och klickar sedan på **[!UICONTROL Nästa]**.

Generera sedan autentiseringsuppgifter för att få åtkomst till API:et. Autentiseringsuppgifterna har formen av en JSON-webbtoken ([JWT](https://jwt.io/)): en öppen standard för säker kommunikation. Om du känner till JWT och redan har genererat nycklar kan du överföra din offentliga nyckel här. Du kan också fortsätta genom att välja **Alternativ 1** så att Adobe kan generera nycklarna åt dig.

![Skärmbild av generering av autentiseringsuppgifter](assets/offer_1.png)

Klicka på knappen **[!UICONTROL Generera nyckelpar]**. Du kan hämta en config.zip -fil. Zippa upp arkivfilen. Det innehåller två filer: certificate_pub.crt och private.key. Se till att den senare hålls säker, eftersom den innehåller dina privata inloggningsuppgifter och kan användas för att generera falska dokument om du inte kan kontrollera.

Klicka på **[!UICONTROL Nästa]**. Nej, aktiverar åtkomst till API:et för generering av PDF. På skärmen **[!UICONTROL Välj produktprofiler]** kontrollerar du **[!UICONTROL Enterprise PDF Services-utvecklare]** och klickar på knappen **[!UICONTROL Spara konfigurerat API]**. Nu är du redo att börja använda API:et.

## Konfigurera projektet

Konfigurera ett nodprojekt som ska köra din kod. I det här exemplet används [Visual Studio Code](https://code.visualstudio.com/) (VS Code) som redigerare. Skapa en mapp med namnet &quot;letter-generator&quot; och öppna den i VS-kod. På menyn **[!UICONTROL Arkiv]** väljer du **[!UICONTROL Terminal]** \> **[!UICONTROL Ny terminal]** för att öppna ett skal i den här mappen. Kontrollera att Node är installerat och att det finns en sökväg genom att ange följande:

```
node -v
```

Du bör se vilken version av Node som är installerad på datorn.

Nu när du har installerat din utvecklingsmiljö kan du gå vidare och skapa ditt projekt.

Initiera först projektet med hjälp av Node Package Manager (npm). Skriv in följande:

```
npm init
```

Du får några frågor om ditt nodprojekt. Du kan hoppa över de flesta av dessa frågor, men se till att projektnamnet är &quot;letter-generator&quot; och att startpunkten är **index.js**. Välj **Ja** för att slutföra projektinitieringen.

Du har nu en package.json-fil. Noden använder den här filen för att organisera projektet. Innan du skapar index.js måste du lägga till Adobe-bibliotek med följande
kommando:

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

En ny mapp med namnet node_MODULES bör läggas till i ditt projekt. I den här mappen hämtas alla bibliotek (som kallas beroenden i noden). Filen package.json uppdateras också med en referens till Adobe PDF-tjänster.

Nu vill du installera Express som ett lättviktigt ramverk för webben. Ange följande kommando:

```
npm install express –save
```

Precis som tidigare uppdateras avsnittet med beroenden i package.json i enlighet med detta.

## Skapa en mall för erbjudandebrev

Nu skapar du en fil i projektets rot som heter app.js. Här lägger vi in följande startkod:

```
const express = require('express');
const bodyParser = require('body-parser');
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk')
const path = require('path');
const app = express();
const port = 8000;
app.use(bodyParser.urlencoded({ extended: true }));
app.get('/', (req, res) => {
res.sendFile(path.join(__dirname + '/index.html'));
});
app.post('/', (req, res) => {
console.log('Got body:', req.body);
res.sendStatus(200);
});
app.listen(port, () => {
console.log(`Candidate offer letter app listening on port ${port}!`)
});
```

Observera att GET-vägen returnerar en **index.html**-fil. Vi skapar en HTML-fil med det namnet och följande enkla formulär. Du kan lägga till CSS-format och andra designelement senare som du vill. Den här blanketten innehåller kandidatens grundläggande information för att skapa ett välkomstbrev:

```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Offer Letter Generator</title>
</head>
<body>
<h1>Enter Candidate Details</h1>
<form action="" method="post">
<div>
<label for="firstname">First name: </label>
<input type="text" name="firstname" id="firstname" required>
</div>
<div>
<label for="lastname">Last name: </label>
<input type="text" name="lastname" id="lastname" required>
</div>
<div>
<label for="salary">Salary ($): </label>
<input type="number" name="salary" id="salary" required>
</div>
<div>
<label for="startdate">Start Date: </label>
<input type="date" name="startdate" id="startdate" required>
</div>
<div>
<input type="submit" value="Generate Letter">
</div>
</form>
</body>
</html>
```

Kör webbservern med följande kommando:

```
node app.js
```

Du bör se meddelandet &quot;Kandidatens erbjudandebrev app lyssnar på port 8000&quot;. Om du öppnar webbläsaren till <http://localhost:8000/> ska formuläret se ut så här:

![Skärmbild av webbformulär](assets/offer_2.png)

Lägg märke till att formuläret postar på sig själv. Om du fyller i data och klickar på **Generera brev** bör du se följande information på konsolen:

```
Got body: { firstname: 'John',
lastname: 'Doe',
salary: '887888',
startdate: '2021-04-01' }
```

Du ersätter den här konsolloggningen med ett webbtjänstanrop till [!DNL Acrobat Services]. Först måste du skapa en JSON-baserad modell av informationen. Modellens format ser ut så här:

```
{
    "offer_letter": {
    "firstname": "John",
    "lastname": "Doe",
    "salary": "887888",
    "startdate": "2021-04-01"
    }
}
```

Du kan göra den här modellen mer detaljerad om du vill, men i den här självstudiekursen behåller du det här enkla exemplet. Det finns ingen validering i det här formuläret eftersom detta ligger utanför artikelns omfattning. Om du vill konvertera formulärtexten till datamodellen som beskrivs ovan ändrar du metoden app.post handler så att den får följande kod:

```
app.post('/', (req, res) => {
const docModel = {'offer_letter': req.body};
generateLetter(docModel);
res.sendStatus(200);
});
```

På den första raden placeras JSON-data i önskat format. Nu skickar du dessa data till en generateLetter-funktion. Stoppa servern och klistra in följande kod i slutet av app.js. Den här koden använder ett Word-dokument som mall och fyller i platshållare med information från ett JSON-dokument.

```
// Letter generation function
function generateLetter(jsonDataForMerge) {
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Create an ExecutionContext using credentials
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials);
// Create a new DocumentMerge options instance
const documentMerge = PDFToolsSdk.DocumentMerge,
documentMergeOptions = documentMerge.options,
options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge,
documentMergeOptions.OutputFormat.PDF);
// Create a new operation instance using the options instance
const documentMergeOperation = documentMerge.Operation.createNew(options)
// Set operation input document template from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(
'resources/OfferLetter-Template.docx');
documentMergeOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
.then(result => result.saveAsFile('output/OfferLetter.pdf'))
.catch(err => {
if(err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log(
'Exception encountered while executing operation', err);
} else {
console.log(
'Exception encountered while executing operation', err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
```

Det finns mycket kod att packa upp där. Låt oss ta huvuddelen först: `documentMergeOperation`. I det här avsnittet tar du dina JSON-data och sammanfogar dem med en Word-dokumentmall. Du kan använda [exemplet på Adobe-webbplatsen](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html#sample-blade) som referens, men låt oss ge ett eget enkelt exempel. Öppna Word och skapa ett nytt tomt dokument. Du kan anpassa den så mycket du vill, men du har åtminstone något som detta:

Hej X!

Vi är glada att kunna erbjuda dig en position för $X ett år. Ditt startdatum är X.

Välkommen

Spara dokumentet som &quot;OfferLetter-Template.docx&quot; i en mapp som heter &quot;resources&quot; (resurser) i projektets rot. Observera de tre Xs:en i dokumentet. Dessa XS är tillfälliga platshållare för din JSON-information. Även om du kan ersätta de här platshållarna med en särskild syntax för att göra det, innehåller Adobe ett Word-tillägg som gör det enklare. Om du vill installera tillägget går du till webbplatsen Adobe [Document Generation Tagger Word Add-in](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin).

Klicka på den nya **dokumentgenereringsknappen** i OfferLetter-mallen. En sidopanel öppnas. Klicka på **Kom igång**. Du får ett textområde som du vill klistra in i JSON-exempeldata. Kopiera JSON-kodavsnittet &quot;offer-data&quot; ovanifrån till textområdet. Det ska se ut så här:

![Skärmbild av brev och kod](assets/offer_3.png)

Klicka på knappen **Generera taggar**. En rullgardinsmeny med taggar visas där du kan infoga taggar i motsvarande punkter i dokumentet. Markera det första krysset i dokumentet och välj **[!UICONTROL förnamn]**. Klicka på **[!UICONTROL Infoga text]** och &quot;Hej X&quot; ändras till &quot;Hej ```{{`offer_letter`.firstname}}```&quot;. Den här taggen har rätt format för `documentMergeOperation`. Fortsätt och lägg till de återstående tre taggarna vid rätt Xs. Glöm inte att spara OfferLetter-template.docx. Det ska se ut så här:

Hej ```{{`offer_letter`.firstname}} {{`offer_letter`.lastname}}```!

Vi är glada att kunna erbjuda dig en position på $ ```{{`offer_letter`.salary}}``` per år. Ditt startdatum är ```{{`offer_letter`.startdate}}```.

Välkommen

Nu har Word-mallen en markering som matchar JSON-formatet. Till exempel ersätts ```{{`offer_letter`.`firstname`}}``` i början av Word-dokumentet med värdet i avsnittet &quot;förnamn&quot; i JSON-data.

Tillbaka till funktionen `generateLetter`. För att skydda ditt REST-anrop skapar du en ny fil som heter pdftools-api-credentials.json i projektets rotmapp. Klistra in följande JSON-data och justera dem med information från tjänstkontoavsnittet (JWT) i [utvecklarkonsolen](https://console.adobe.io/).

```
{
"client_credentials": {
"client_id": "<YOUR_CLIENT_ID>",
"client_secret": "<YOUR_CLIENT_SECRET>"
},
"service_account_credentials": {
"organization_id": "<YOUR_ORGANIZATION_ID>",
"account_id": "<YOUR_TECHNICAL_ACCOUNT_ID>",
"private_key_file": "<PRIVATE_KEY_FILE_PATH>"
}
}
```

* Klient-ID, klienthemlighet och organisations-ID kan kopieras direkt från avsnittet **[!UICONTROL Autentiseringsinformation]** i konsolen.

* Konto-ID är **ID för tekniskt konto**.

* Kopiera filen private.key som du genererade tidigare till projektet och ange dess namn i avsnittet private_key_file i
filen pdftools-api-credentials.json. Om du vill kan du ange en sökväg till filen med den privata nyckeln här. Kom ihåg att hålla den säker eftersom den kan missbrukas en gång utanför din kontroll.

Om du vill skapa en PDF med JSON-data ifyllda går du tillbaka till webbformuläret **[!UICONTROL Ange information om kandidater]** och lägger upp data. Det tar en liten stund eftersom dokumentet måste hämtas från Adobe, men du bör ha en fil med namnet OfferLetter.pdf i en ny mapp med namnet output.

## Nästa steg

Det var det! Detta är bara början. Om du tittar på avsnittet Avancerat på fliken Dokumentgenerering i Word-tillägget, ser du att inte alla platshållarmarkörer kommer från associerade JSON-data. Du kan också lägga till signaturtaggar. Med dessa taggar kan du ta dokumentet som skapas och överföra det till [Adobe Sign](https://acrobat.adobe.com/ca/en/sign.html) för leverans och signering till den nya medarbetaren. Läs Komma igång med Adobe Sign API och lär dig hur du gör det. Den här processen är liknande eftersom du använder REST-anrop som är skyddade med en JWT-token.

Exemplet med ett enda dokument som anges ovan kan användas som grund för ett program när en organisation måste [öka säsongsrekryteringen](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html) av anställda på flera platser. Som visas är det huvudsakliga flödet att ta data från kandidater via en onlineansökan. Data används för att fylla i fälten i ett erbjudandebrev och skicka ut det för elektronisk signatur.

[!DNL Adobe Acrobat Services] kan användas kostnadsfritt i sex månader och sedan [betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) med bara $0,05 per dokumenttransaktion, så att du kan testa det och skala arbetsflödet för erbjudandebrevet allt eftersom företaget växer. [Kom igång](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
skapa egna mallar, [registrera ditt utvecklarkonto](https://www.adobe.io/).
