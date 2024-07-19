---
title: Söka och indexera
description: Lär dig hur du skapar sökbara PDF-filer från skannade dokument
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8095
thumbnail: KT-8095.jpg
exl-id: a22230b5-1ff2-4870-84da-f06a904c99e1
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1298'
ht-degree: 0%

---

# Söka och indexera

![Banderoll för användningsfall](assets/UseCaseSearchingHero.jpg)

Organisationer måste ofta digitalisera sina papperskopior av dokument och skannade filer. Överväg det här [scenariot](https://docs.google.com/document/d/11jZdVQAw-3fyE3Y-sIqFFTlZ4m02LsCC/edit). En advokatbyrå har tusentals juridiska avtal som de har skannat för att skapa digitala filer. De vill fastställa om något av dessa rättsliga avtal har en särskild klausul eller ett tillägg som de måste se över. För att uppfylla kraven är det nödvändigt att vara noggrann. Lösningen är att inventera de digitala dokumenten, göra texten sökbar och skapa ett index för att hitta denna information.

Utmaningen att skapa digitala arkiv för att hämta information för redigering eller nedströmsverksamhet är en mardröm för de flesta organisationer.

## Vad du kan lära dig

I den här praktiska självstudiekursen går vi igenom hur funktioner i [!DNL Adobe Acrobat Services] API:er kan användas för att arkivera och digitalisera dokument. Du utforskar dessa funktioner genom att skapa en Express NodeJS-applikation och sedan integrera [!DNL Acrobat Services] API:er för arkivering, digitalisering och dokumentomvandling.

Om du vill följa behöver du [Node.js](https://nodejs.org/) installerat och en grundläggande förståelse för Node.js och [ES6-syntax](https://www.w3schools.com/js/js_es6.asp).

## Relevanta API:er och resurser

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Projektkod](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git)

## Projektinställningar

Först gör du mappstrukturen för programmet. Du kan hämta källkoden [här](https://github.com/agavitalis/AdobeDocumentAPI.git).

## Katalogstruktur

Skapa en mapp med namnet AdobeDocumentServicesAPI:er och öppna den i en redigerare som du väljer. Skapa ett grundläggande NodeJS-program med kommandot `npm init` med hjälp av den här mappstrukturen:

```
AdobeDocumentServicesAPIs
config
default.json
controllers
createPDFController.js
makeOCRController.js
searchController.js
models
document.js
output
.gitkeep
routes
web.js
services
upload.js
views
index.hbs
ocr.hbs
search.hbs
index.js
```

Du använder MongoDB som databas för detta program. Om du vill konfigurera placerar du därför standarddatabaskonfigurationerna i mappen config/ genom att klistra in kodfragmentet nedan i filen default.json i den här mappen och lägger sedan till URL:en till databasen.

```
### config/default.json and config/dev.json
{ "DBHost": "YOUR_DB_URI" }
```

## Paketinstallation

Installera nu några paket med hjälp av kommandot npm install som visas i kodfragmentet nedan:

```
{
    "name": "adobedocumentservicesapis",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "directories": {
    "test": "test"
    },
    "dependencies": {
    "body-parser": "^1.19.0",
    "config": "^3.3.6",
    "express": "^4.17.1",
    "hbs": "^4.1.1",
    "mongoose": "^5.12.1",
    "morgan": "^1.10.0",
    "multer": "^1.4.2",
    "path": "^0.12.7"
    },
    "devDependencies": {},
    "scripts": {
    "start": "set NODE_ENV=dev && node index.js"
    },
    "repository": {
    "type": "git",
    "url": "git+https://github.com/agavitalis/AdobeDocumentServicesAPIs.git"
    },
    "author": "Ogbonna Vitalis",
    "license": "ISC",
    "bugs": {
    "url": "https://github.com/agavitalis/AdobeDocumentServicesAPIs/issues"
    },
    "homepage": "https://github.com/agavitalis/AdobeDocumentServicesAPIs#readme"
}
```

```
###bash
npm install express mongoose config body-parser morgan multer hbs path pdf-parse
Ensure that the content of your package.json file is similar to this code snippet:
###package.json
{
```

Dessa kodfragment installerar programberoenden, inklusive mallmotorn Handlebars för vyn. I taggen scripts konfigurerar du programmets körningsparametrar.

## Integrerar [!DNL Acrobat Services] API:er

[!DNL Acrobat Services] innehåller tre API:er:

* Adobe PDF Services API

* Adobe PDF Embed API

* Adobe-dokumentgenererings-API

Dessa API:er automatiserar generering, manipulering och omvandling av PDF-innehåll genom en uppsättning molnbaserade webbtjänster.

För att få autentiseringsuppgifterna måste du [registrera](https://www.adobe.com/go/dcsdks_credentials?ref=getStartedWithServicesSDK) och slutföra arbetsflödet. PDF Embed API är gratis att använda. PDF Services API och Document Generation API är kostnadsfria i sex månader. När provperioden upphör kan du [betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) för bara $0,05 per dokumenttransaktion. Du betalar bara när företaget växer och bearbetar fler avtal.

![Skärmbild av att skapa autentiseringsuppgifter](assets/searching_1.png)

När du har slutfört registreringen hämtas ett kodexempel till datorn som innehåller dina API-inloggningsuppgifter. Extrahera koden och placera filerna private.key och pdftools-api-credentials.json i rotkatalogen i programmet.

Installera sedan [PDF Services Node.js SDK](https://www.npmjs.com/package/@adobe/documentservices-pdftools-node-sdk) genom att köra kommandot ` npm install --save @adobe/documentservices-pdftools-node-sdk ` med hjälp av terminalen i programmets rotkatalog.

## Skapa en PDF

[!DNL Acrobat Services] har stöd för att skapa PDF från Microsoft Office-dokument (Word, Excel och PowerPoint) och andra [filformat som stöds](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf), t.ex. .txt, .rtf, .bmp, .jpg, .gif, .tiff och .png.

Om du vill skapa PDF-dokument från de filformat som stöds använder du det här formuläret för att överföra dokumenten. Du kommer åt HTML- och CSS-filerna för formuläret på [GitHub](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git).

![Skärmbild av webbformuläret](assets/searching_2.png)

Lägg nu till följande kodfragment i filen controllers/createPDFController.js. Med den här koden hämtas dokumentet och omvandlas till en PDF.

Originalfilerna och den omformade filen sparas i en mapp i programmet.

```
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
/*
* GET / route to show the createPDF form.
*/
function createPDF(req, res) {
//catch any response on the url
let response = req.query.response
res.render('index', { response })
}
/*
* POST /createPDF to create a new PDF File.
*/
function createPDFPost(req, res) {
let filePath = req.file.path;
let fileName = req.file.filename;
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Create an ExecutionContext using credentials and create a new operation
instance.
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials),
createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();
// Set operation input from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(filePath);
createPdfOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
createPdfOperation.execute(executionContext)
.then((result) => {
result.saveAsFile('output/createPDFFromDOCX.pdf')
//download the file
res.redirect('/?response=PDF Successfully created')
})
.catch(err => {
if (err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log('Exception encountered while executing operation',
err);
} else {
console.log('Exception encountered while executing operation',
err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
//export all the functions
module.exports = { createPDF, createPDFPost };
```

Det här kodfragmentet kräver [PDF Services Node.js SDK](https://www.npmjs.com/package/@adobe/documentservices-pdftools-node-sdk). Använd funktionerna:

* createPDF, som visar formuläret för att ladda upp dokument

* createPDFPost, som omvandlar det uppladdade dokumentet till en PDF

De omformade PDF-dokumenten sparas i utdatakatalogen, medan originalfilen sparas i uppladdningskatalogen.

## Använda textigenkänning

Optisk teckenigenkänning (OCR) konverterar bilder och skannade dokument till sökbara filer. Du kan konvertera [!DNL Acrobat Services] API:er, bilder och skannade dokument till sökbart PDF. När du har utfört en OCR-åtgärd blir filen redigerbar och sökbar. Du kan lagra filens innehåll i ett datalager för indexering och annan användning.

Kom ihåg att sökning och indexering av skannade dokument är avgörande för många organisationer där filhantering och informationsbehandling är viktigt. OCR-funktionen eliminerar dessa utmaningar.

Om du vill implementera den här funktionen måste du designa ett överföringsformulär som liknar det ovan. Den här gången begränsar du formuläret till PDF-filer eftersom du bara kan använda OCR-funktionen på PDF-dokument.

Här är uppladdningsformuläret för det här exemplet:

![Skärmbild av formulär för att ladda upp filer](assets/searching_3.png)

Nu kan du manipulera den överförda PDF och utföra några OCR-åtgärder genom att lägga till kodfragmentet nedan i filen controllers/makeOCRController.js. Med den här koden implementeras OCR-processen på en uppladdad fil och sedan sparas filen i programmets filsystem.

```
const fs = require('fs')
const pdf = require('pdf-parse');
const mongoose = require('mongoose');
const Document = require('../models/document');
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
/*
* GET /makeOCR route to show the makeOCR form.
*/
function makeOCR(req, res) {
//catch any response on the url
let response = req.query.response
res.render('ocr', { response })
}
/*
* POST /makeOCRPost to create a new PDF File.
*/
function makeOCRPost(req, res) {
let filePath = req.file.path;
let fileName = req.file.filename;
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Create an ExecutionContext using credentials and create a new operation
instance.
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials),
ocrOperation = PDFToolsSdk.OCR.Operation.createNew();
// Set operation input from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(filePath);
ocrOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
ocrOperation.execute(executionContext)
.then(async (result) => {
let newFileName = `createPDFFromDOCX-${Math.random() * 171}.pdf`;
await result.saveAsFile(`output/${newFileName}`);
let documentContent = fs.readFileSync(
require("path").resolve("./") + `\\output\\${newFileName}`
);
pdf(documentContent)
.then(function (data) {
//Creates a new document
var newDocument = new Document({
documentName: fileName,
documentDescription: description,
documentContent: data.text,
url: require("path").resolve("./") + `\\output\\${newFileName}`
});
//Save it into the DB.
newDocument.save((err, docs) => {
if (err) {
res.send(err);
} else {
//If no errors, send it back to the client
res.redirect(
"/makeOCR?response=OCR Operation Successfully performed on
the PDF File"
);
}
});
})
.catch(function (error) {
// handle exceptions
console.log(error);
});
})
.catch(err => {
if (err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log('Exception encountered while executing operation',
err);
} else {
console.log('Exception encountered while executing operation',
err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
//export all the functions
module.exports = { makeOCR, makeOCRPost };
```

Du behöver [!DNL Acrobat Services]-nodens SDK och mongoose-, pdf-parse- och fs-modulerna samt dokumentmodellschemat. Dessa moduler är nödvändiga för att spara innehållet i den transformerade filen till en MongoDB-databas.

Skapa nu två funktioner: gör OCR för att visa det överförda formuläret och gör sedan OCRPost för att bearbeta det överförda dokumentet. Spara det ursprungliga formuläret i en databas och spara sedan det omformade formuläret i programmappen för utdata i programmet.

De inloggningsuppgifter som Adobe tillhandahåller från filen pdftools-api-credentials.json läses in i varje enskilt fall innan filen omvandlas.

>[!NOTE]
>
>OCR-funktionen stöder bara PDF-dokument.

Lägg till kodfragmentet nedan i programfilen Modes/Document.js.

I kodfragmentet definierar du en mongoose-modell och beskriver sedan dokumentets egenskaper som ska sparas i databasen. Indexera också fältet documentContent för att göra det enkelt och effektivt att söka efter text.

```
const mongoose = require("mongoose");
const Schema = mongoose.Schema;
//Document schema definition
var DocumentSchema = new Schema(
{
documentName: { type: String, required: false },
documentDescription: { type: String, required: false },
documentContent: { type: String, required: false },
url: { type: String, required: false },
status: {
type: String,
enum : ["active","inactive"],
default: "active"
}
},
{ timestamps: true }
);
//for text search
DocumentSchema.index({
documentContent: "text",
});
//Exports the DocumentSchema for use elsewhere.
module.exports = mongoose.model("document", DocumentSchema);
```

## Söka i texter

Nu implementerar du en enkel sökfunktion så att användare kan utföra vissa enkla textsökningar. Du kan också lägga till hämtningsfunktioner för att aktivera hämtning av PDF-filer.

Den här funktionen kräver ett enkelt formulär och kort för att visa sökresultatet. Du hittar designen för formuläret och korten på [GitHub](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git).

Skärmbilden nedan visar sökfunktionen och sökresultaten. Du kan hämta vilket som helst av sökresultaten.

![Skärmbild av sökfunktioner](assets/searching_4.png)

För att implementera sökfunktionen skapar du filen searchController.js i programmets styrenhetsmapp och klistrar in kodfragmentet nedan:

```
const fs = require('fs')
const mongoose = require('mongoose');
const Document = require('../models/document');
/*
* GET / route to show the search form.
*/
function search(req, res) {
//catch any response on the url
let response = req.query.response
res.render('search', { response })
}
/*
* POST /searchPost to search the contents of your saved file.
*/
function searchPost(req, res) {
let searchString = req.body.searchString;
Document.aggregate([
{ $match: { $text: { $search: searchString } } },
{ $sort: { score: { $meta: "textScore" } } },
])
.then(function (documents) {
res.render('search', { documents })
})
.catch(function (error) {
let response = error
res.render('search', { response })
});
}
//export all the functions
module.exports = { search, searchPost, downloadPDF };
```

Implementera nu en hämtningsfunktion för att aktivera hämtning av dokument som returneras från en användares sökning.

## Hämtar dokument

Att implementera en hämtningsfunktion liknar det du redan har gjort. Lägg till följande kodfragment efter funktionen searchPost i filen controllers/earchController.js:

```
/*
* POST /downloadPDF To Download PDF Documents.
*/
async function downloadPDF(req, res) {
console.log("here")
let documentId = req.params.documentId
let document = await Document.findOne({_id:documentId});
res.download(download.link);
}
```

## Nästa steg

I den här praktiska självstudiekursen har du integrerat [!DNL Acrobat Services] API:er i ett Node.js-program och även använt API:t för att implementera en dokumentomvandling som konverterar filer till PDF. Du har lagt till en OCR-funktion som gör bilder och skannade filer sökbara. Sedan sparade du filerna i en mapp så att de kan hämtas.

Därefter lade du till en sökfunktion för att söka i dokument som konverterats till text med OCR. Slutligen har du implementerat en hämtningsfunktion för att göra det möjligt att enkelt hämta de filerna. Din ifyllda ansökan gör det mycket enklare för ett juridiskt företag att hitta och bearbeta specifik text.

Att använda [!DNL Acrobat Services] för dokumenttransformering rekommenderas starkt eftersom det är robust och enkelt att använda jämfört med andra tjänster. Du kan snabbt skapa ett konto och börja använda funktionerna i [!DNL Acrobat Services] API:er för dokumentomvandling och -hantering.

Nu när du har en god förståelse för hur du använder [!DNL Acrobat Services] API:er kan du utveckla dina färdigheter med övning. Du kan klona databasen som används i den här självstudiekursen och experimentera med några av de kunskaper du just har lärt dig. Ännu bättre är att du kan försöka återskapa programmet samtidigt som du utforskar de obegränsade möjligheterna med [!DNL Acrobat Services] API:er.

Är du redo att aktivera dokumentdelning och granskning i din egen app? Registrera dig för ditt [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
utvecklarkonto. Testa kostnadsfritt i sex månader och sedan [betala löpande](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) för bara \$0,05 per dokumenttransaktion när företaget växer.
