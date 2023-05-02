---
title: Hantera juridiska avtal
description: Lär dig hur du automatiskt genererar och skyddar juridiska dokument med anpassad datainmatning
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8097.jpg
kt: 8097
exl-id: e0c32082-4f8f-4d8b-ab12-55d95b5974c5
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '2006'
ht-degree: 1%

---

# Hantera juridiska avtal

![BANDEROLL MED ANVÄNDNINGSFALL](assets/UseCaseLegalHero.jpg)

Digitaliseringen medför utmaningar. Idag har de flesta organisationer många typer av [juridiska kontrakt](https://www.adobe.io/apis/documentcloud/dcsdk/legal-contracts.html) att de måste skapa, redigera, godkänna och ha signerat av olika parter. Dessa juridiska avtal kräver ofta unika anpassningar och varumärken. Organisationer kan också behöva spara dem i ett skyddat format när de har signerats för att skydda dem. För att göra allt detta behöver de en robust lösning för dokumentgenerering och -hantering.

Många lösningar erbjuder viss dokumentgenerering, men kan inte anpassa indata och villkorsstyrd logik, t.ex. satser som bara gäller specifika scenarier. Att manuellt uppdatera ett företags juridiska mallar är en utmaning och felbenägen uppgift eftersom dessa dokument blir allt mer omfattande. Behovet av att automatisera dessa processer är stort.

## Vad du kan lära dig

I den här praktiska självstudiekursen kan du utforska funktionerna i [[!DNL Adobe Acrobat Services] API:er](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html) i genereringen av anpassade inmatningsfält i dokument. Du kan även utforska hur du enkelt konverterar dessa genererade dokument till ett skyddat portabelt dokumentformat (PDF) för att förhindra datamanipulation.

Den här självstudiekursen innehåller lite programmering när du utforskar konvertering av kontrakt till PDF. För att följa med effektivt [Microsoft Word](https://www.microsoft.com/en-us/download/office.aspx) och [Node.js](https://nodejs.org/) bör vara installerat på datorn. En grundläggande förståelse av Node.js och [ES6-syntax](https://www.w3schools.com/js/js_es6.asp) rekommenderas också.

## Relevanta API:er och resurser

* [Adobe-API för dokumentgenerering](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [PDF Embed API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Projektkod](https://github.com/agavitalis/adobe_legal_contracts.git)

## Skapa ett malldokument

Du kan skapa juridiska dokument med Microsoft Word-programmet eller genom att hämta Adobe [exempel på Word-mallar](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html#sample-blade). Ändå är det inte lätt att anpassa indata och signera dessa dokument digitalt utan att använda vissa hjälpverktyg som [Adobe-tillägg för dokumentgenereringstaggar](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin) för Microsoft Word.

Document Generation Tagger är ett Microsoft Word-tillägg som har gjorts för att göra dokumentanpassningen sömlös med hjälp av taggar. Det gör det möjligt att skapa dynamiska fält i dokumentmallar som fylls dynamiskt med JSON-data.

![Skärmdump av hur man lägger till Adobe Document Generation Tagger i Word](assets/legal_1.png)

För att illustrera användningen av dokumentgenereringstaggen installerar du det här tillägget och skapar sedan en JSON-datamodell, som används i taggning av ett enkelt juridiskt kontraktsdokument.

Installera dokumentgenereringstaggen i Word genom att klicka på **Infoga** och klicka sedan på **Mina tillägg**. På menyn Office-tillägg söker du efter &quot;Adobe Document Generation&quot; och klickar sedan på **Lägg till** och följ processen. Dessa steg visas i skärmbilden ovan.

När du har installerat tillägget Document Generation Tagger for Word skapar du en enkel JSON-datamodell för att tagga det giltiga dokumentet.

Om du vill fortsätta öppnar du valfri redigerare, skapar en fil som heter Agreement.json och klistrar in kodfragmentet nedan i JSON-filen som du skapade.

```
{
"Agreement": {
"Date": "1/24/2021",
"Prime Contractor Name": "Ogbonna Vitalis Corp",
"Prime State": "Lagos",
"Address": "Maryland Ave, Lagos State, Ng",
"Sub Contractor Name": "Vivvaa Soln",
"Sub Contractor State": "California",
"Sub Contractor Address": "Molusi Avenue, Dallas Texas, CA",
"Agreement Date": "1/24/2021",
"Length": 5
}
}
```

När du har sparat JSON-dokumentet importerar du det till tillägget Dokumentgenereringstagg. Importera dokumentet genom att klicka på **Dokumentgenerering** i Adobe längst upp till höger på Word-skärmen, vilket visas på skärmbilden nedan.

![Skärmdump av tillägget Adobe Document Generation Tagger i Word](assets/legal_2.png)

En video visas som du kan använda som vägledning. Du kan titta på det eller gå direkt till taggningsfältet genom att klicka på **Kom igång**. Efter klickning **Kom igång** visas ett överföringsformulär. Klicka **Överför JSON-fil** och välj den JSON-fil som du just skapade. När importen är klar klickar du på **Generera tagg** för att generera taggarna.

När du har importerat och genererat märkord kan du lägga till dem i dokumentet. Om du vill lägga till dem placerar du markören på exakt den plats där du vill att taggen ska visas. Välj sedan en tagg från dokumentgenererings-API:et och klicka på **Infoga text**. Skärmbilden nedan beskriver den här proceduren.

![Skärmdump av att lägga till taggar i dokument](assets/legal_3.png)

Förutom de grundläggande taggar som skapas med den importerade JSON-datamodellen kan du även använda avancerade funktioner för fler alternativ, till exempel bilder, villkorlig logik, beräkningar, upprepade element och villkorliga fraser. Du kommer åt dessa funktioner genom att klicka på **Avancerat** på panelen Dokumentgenereringstagg. Du kan se detta i skärmbilden nedan.

![Skärmdump av fliken Advanced i taggen Adobe Document Generation](assets/legal_4.png)

Dessa avancerade funktioner skiljer sig inte från de grundläggande taggarna. Om du vill inkludera villkorlig logik markerar du den del av dokumentet som ska fyllas i. Konfigurera sedan den regel som avgör vilken tagg som ska infogas.

För att ytterligare illustrera, till exempel i avtalet, finns det ett avsnitt som du bara vill inkludera villkorligt. I fältet Välj innehållstyp väljer du **avsnitt.** I fältet Välj poster väljer du det alternativ som avgör om det villkorliga avsnittet ska visas. Välj önskad villkorsoperator och ange värdet som du testar för i fältet Värde . Klicka sedan på **Infoga villkor.** Skärmbilden nedan visar den här processen.

![Skärmdump av att infoga villkorsstyrt innehåll](assets/legal_5.png)

För beräkningar väljer du antingen Aritmetisk eller Aggregering och sedan den relevanta första posten, operatorn och den andra posten som ska användas baserat på de malltaggar som är tillgängliga. Klicka sedan på **Infoga beräkning**.

Juridiska avtal kräver dessutom ofta underskrifter av de inblandade parterna. Du kan infoga en e-signatur med hjälp av Adobe Sign-texttaggar som finns precis under avsnittet &quot;Numeriska beräkningar&quot;. Om du vill inkludera e-signaturen måste du ange antalet mottagare och välja **Signerare** och fälttypen i listrutorna. När du är klar klickar du på **Infoga Adobe Sign-texttagg** för att slutföra processen.

Spara juridiska dokument i skyddat format för att säkerställa dataintegriteten. Med [!DNL Acrobat Services] API:er kan du snabbt omvandla dokument till PDF-format. Du kan skapa ett enkelt Express Node.js-program, integrera Document Generation API i det och använda det här programmet för att konvertera taggade dokument från Word till PDF.

## Projektinställningar

Först konfigurerar du mappstrukturen för programmet Node.js. I det här exemplet anropar du det här enkla programmet AdobeLegalContractAPI. Du kan hämta källkoden [här](https://github.com/agavitalis/adobe_legal_contracts.git).

### Katalogstruktur

Skapa en mapp med namnet AdobeLegalContractAPI och öppna den i en redigerare som du väljer. Skapa ett grundläggande Node.js-program med ```npm init``` med hjälp av mappstrukturen nedan:

```
###Directory Structure
AdobeLegalContractAPI
-----config
----------default.json
-----controllers
----------createPDFController.js
----------previewController.js
-----models
----------document.js
-----routes
----------web.js
-----services
-----------upload.js
-----uploads
-----views
-----index.js
```

Ovanför finns en enkel Node.js-programstruktur för ditt program. Fortsätt nu med installationen av de nödvändiga npm-paketen.

### Paketinstallation

Installera de nödvändiga paketen med kommandot npm install enligt kodfragmentet nedan:

```
npm install express body-parser morgan multer hbs path config mongoose
```

När du har installerat paketen ska du se till att innehållet i filen package.json liknar kodfragmentet nedan:

```
###package.json
{
"name": "adobelegalcontractapi",
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
"start": "node index.js"
},
"repository": {
"type": "git",
"url": "https://github.com/agavitalis/adobe_legal_contracts.git"
},
"author": "Ogbonna Vitalis",
"license": "ISC",
"bugs": {
"url": "https://github.com/agavitalis/adobe_legal_contracts/issues"
},
"homepage": "https://github.com/agavitalis/adobe_legal_contracts#readme"
}
```

I dessa kodfragment installerade du programberoenden, inklusive mallmotorn Handlebars för vyn.

Fokus i den här självstudiekursen ligger främst på att använda [[!DNL Acrobat Services] API:er](https://www.adobe.io/apis/documentcloud/dcsdk/) för att konvertera dokument till PDF. Därför finns det inte en steg-för-steg-process för hur man skapar denna Node.js applikation. Du kan dock hämta den fullständiga fungerande Node.js -programkoden på [GitHub](https://github.com/agavitalis/adobe_legal_contracts.git).

## Integrera [!DNL Adobe Acrobat Services] API:er i ett Node.js-program

[!DNL Adobe Acrobat Services] API:er är molnbaserade, tillförlitliga tjänster som är utformade för smidig hantering av dokument. Det erbjuder tre API:er:

* Adobe PDF Services API

* Adobe PDF Embed API

* Adobe-API för dokumentgenerering

Du behöver inloggningsuppgifter för att använda [!DNL Acrobat Services] API:er (skiljer sig från dina inloggningsuppgifter för PDF Embed API). Om du inte har giltiga inloggningsuppgifter [registrera](https://www.adobe.com/go/dcsdks_credentials?ref=getStartedWithServicesSDK) och slutför arbetsflödet enligt bilden i skärmbilden nedan. Njut av en [kostnadsfri provperiod på sex månader och sedan betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html), endast 0,05 USD per dokumenttransaktion.

![Skärmdump av att skapa nya inloggningsuppgifter](assets/legal_6.png)

När registreringen är klar hämtas ett kodexempel automatiskt till datorn så att du kan komma igång. Du kan extrahera det här kodexemplet och följa med. Glöm inte att kopiera filerna pdftools-api-credentials.json och private.key från det extraherade kodexemplet till Node.js-projektets rotkatalog. Autentiseringsuppgifterna krävs innan du kan komma åt [!DNL Acrobat Services] API-slutpunkter. Du kan också hämta SDK-exempel med dina personliga inloggningsuppgifter så att du inte behöver uppdatera nyckeln i exempelkoden.

Installera nu Adobe PDF Services Node SDK genom att köra ```npm install \--save @adobe/documentservices-pdftools-node-sdk``` -kommandot med terminalen i programmets rotkatalog. När den har installerats kan du använda [!DNL Acrobat Services] API:er för att hantera dokument i programmet.

## Skapa ett PDF-dokument

[!DNL Acrobat Services] API:er stöder skapande av PDF från Microsoft Office-dokument (Word, Excel och PowerPoint) och andra [filformat som stöds](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf) som .txt, .rtf, .bmp, .jpeg, gif, .tiff och .png. Du kan enkelt konvertera juridiska avtal från andra filformat till PDF med Acrobat Service API:er.

Adobe Document Generation API möjliggör konvertering till en Word-fil eller PDF. Du kan t.ex. använda en Word-mall för att skapa ett kontrakt, inklusive rödmarkering för att markera redigerad text. Konvertera det sedan till en PDF och använd PDF Services API för att skydda dokumentet med ett lösenord, skicka det för signering med mera.

Det finns ett formulär för att överföra ett dokument för omvandling med hjälp av PDF-dokument från de tillgängliga filformat som stöds [!DNL Acrobat Services].

Det utformade överföringsformuläret visas i skärmbilden nedan och du kan komma åt HTML- och CSS-filerna på [GitHub](https://github.com/agavitalis/adobe_legal_contracts.git).

![Skärmdump av uppladdning av formulär](assets/legal_7.png)

Nu lägger du till följande kodfragment i filen /createPDFController.js. Med den här koden hämtas det överförda dokumentet och omvandlas till PDF. [!DNL Acrobat Services] sparar den överförda originalfilen och den omvandlade filen i olika mappar.

```
###controllers/createPDFController.js
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
const Document = require('../models/document');
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
// Create an ExecutionContext using credentials and create a new operation instance.
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials),
createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();
// Set operation input from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(filePath);
createPdfOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
createPdfOperation.execute(executionContext)
.then(async(result) => {
let newFileName = `createPDFFromDOCX-${Math.random() * 171}.pdf`
let newFilePath = require('path').resolve('./') + `\\output\\${newFileName}`
await result.saveAsFile(`views/output/${newFileName}`)
//Creates a new document
let newDocument = new Document({
documentName: newFileName,
url: newFilePath
});
//Save it into the DB.
newDocument.save((err, docs) => {
if (err) {
res.send(err);
}
else {
res.redirect('/?response=PDF Successfully created')
}
});
})
.catch(err => {
if (err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log('Exception encountered while executing operation', err);
} else {
console.log('Exception encountered while executing operation', err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
//export all the functions
module.exports = { createPDF, createPDFPost };
```

Ovanstående kodfragment krävde dokumentmodellen och [!DNL Acrobat Services] Node SDK som du tidigare installerade. Det finns två funktioner:

* createPDF visar överföringsdokumentformuläret.

* createPDFPost omvandlar det överförda dokumentet till ett PDF.

Funktionerna sparar de omformade PDF-dokumenten i katalogen views/output, där du kan hämta dem till datorn.

Du kan också förhandsvisa den omformade PDF-filen med det kostnadsfria inbäddade API:t PDF. Med hjälp av PDF Embed API kan du skapa Adobe-autentiseringsuppgifter [här](https://www.adobe.com/go/dcsdks_credentials) (som skiljer sig från [!DNL Acrobat Services] uppgifter) och registrera tillåtna domäner för åtkomst till API:et. Följ proceduren och generera API-uppgifter för PDF Embed för programmet. Du kan också kolla in demonstrationen [här](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf), från vilken du enkelt kan generera koder för att komma igång snabbt.

Tillbaka till programmet, skapa list.hbs- och preview.hbs-filer i visningsmappen i ditt program och klistra in kodfragmentet nedan i list.hbs- respektive preview.hbs-filerna.

```
###views/list.hbs
<!DOCTYPE html>
<html lang="en">
<head>
<title>Adobe Legal Contract</title>
<!-- Meta tags -->
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,
initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="ie=edge">
<!-- //Meta tags -->
<link
href=".min.css" rel="stylesheet" integrity="sha384-eOJMYsd53ii+scO/
bJGFsiCZc+5NDVN2yr8+0RDqr0Ql0h+rP48ckxlpbzKgwra6" crossorigin="anonymous">
<link rel="stylesheet" href="css/style.css" type="text/css"
media="all" /><!-- Style-CSS -->
<link href="css/font-awesome.css" rel="stylesheet" /><!--
font-awesome-icons -->
</head>
<body>
<section>
<div class="form-36-mian section-gap">
<div class="wrapper">
<div class="container">
<div class="row">
{{#each documents}}
<div class="col-md-4 mb-2">
<div class="card" style="width:
18rem;">
<img class="card-img-top"
src="./images/pdf.png"
alt="Card image cap">
<div class="card-body">
<h5
class="card-title">{{documentName}}</h5>
<a
href="/downloadPDF/{{_id}}" class="btn btn-primary"><i class="fa
fa-download" aria-hidden="true"></i> Download</a>
<a
href="/previewPDF/{{_id}}" class="btn btn-info"><i class="fa fa-eye"
aria-hidden="true"></i> Preview</a>
</div>
</div>
</div>
{{/each}}
</div>
</div>
<!-- copyright -->
<div class="copy-right">
<p>(c) 2021 Vitalis</p>
</div>
<!-- //copyright -->
</div>
</div>
</section>
</body>
</html>
###views/preview.hbs
<!DOCTYPE html>
<html lang="en">
<head>
<title>[!DNL Adobe Acrobat Services] PDF Embed API</title>
<meta charset="utf-8" />
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
<meta id="viewport" name="viewport" content="width=device-width,
initial-scale=1" />
</head>
<body style="margin: 0px">
<input type="hidden" id="pdfDocumentName"
value={{document.documentName}} />
<input type="hidden" id="pdfDocumentUrl" value={{document.url}} />
<div id="adobe-dc-view"></div>
<script
src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
let pdfDocumentName =
document.getElementById("pdfDocumentName").value;
let pdfDocumentUrl =
document.getElementById("pdfDocumentUrl").value;
document.addEventListener("adobe_dc_view_sdk.ready", function
() {
var adobeDCView = new AdobeDC.View({ clientId:
"XXXXXXXXXXXXXXXX", divId: "adobe-dc-view" });
adobeDCView.previewFile({
content: { location: { url:
`http://localhost:5000/output/${pdfDocumentName}` } },
metaData: { fileName: pdfDocumentName }
}, {});
});
</script>
</body>
</html>
```

Du kan också skapa en controller/previewController.js och klistra in kodfragmenten nedan i den.

```
const Document = require('../models/document');
/*
* GET /listFiles route to show PDF file lists.
*/
async function listFiles(req, res) {
let documents = await Document.find({});
res.render('lists', { documents })
}
/*
* GET /previewPDF route to show PDF file in AdobeEmbedAPI.
*/
async function previewPDF(req, res) {
//catch any response on the url
let documentId = req.params.documentId
let document = await Document.findOne({_id:documentId});
res.render('preview', { document })
}
/*
* GET /downloadPDF To Download PDF Documents.
*/
async function downloadPDF(req, res) {
let documentId = req.params.documentId
let document = await Document.findOne({_id:documentId});
res.download(document.url);
}
//export all the functions
module.exports = {listFiles, previewPDF, downloadPDF };
```

I kontrollfilen ovan finns det tre funktioner, listFiles, previewPDF och downloadPDF. Funktionen listFiles listar alla PDF-filer som hittills har genererats med [!DNL Acrobat Services] API:er. Med funktionen previewPDF kan du förhandsvisa PDF-filer med hjälp av PDF Embed API, medan funktionen downloadPDF låter dig ladda ned den genererade PDF-filen till datorn. I skärmbilden nedan visas ett exempel på förhandsvisningen i PDF med API:t för inbäddning i PDF.

![Skärmdump av PDF preview](assets/legal_8.png)

## Sammanfattning

I den här praktiska självstudiekursen taggar du ett dokument med tillägget Microsoft Word för dokumentgenereringstaggar. Integrerat [!DNL Acrobat Services] API:er till ett Node.js-program och konverterade ett taggat dokument till ett nedladdningsbart PDF-format, även om du också kunde ha skapat det juridiska avtalet direkt till PDF. Slutligen använde du Adobe PDF Embed API för att förhandsgranska det genererade PDF för verifiering och signering.

Det färdiga programmet gör det mycket enklare att tagga [mallar för juridiskt bindande kontrakt](https://www.adobe.io/apis/documentcloud/dcsdk/legal-contracts.html) med dynamiska fält konverterar du dem till PDF, förhandsgranskar dem och signerar dem med [!DNL Acrobat Services] API:er. Istället för att lägga tid på att skapa ett unikt kontrakt kan teamet automatiskt skicka rätt kontrakt till varje kund och sedan lägga mer tid på att utveckla verksamheten.

Organisationer använder [!DNL Adobe Acrobat Services] API:er för deras fullständighet och användarvänlighet. Bäst av allt, du kan njuta av en [sex månaders kostnadsfri testversion och sedan betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html). Du betalar bara för det du använder. Dessutom är PDF Embed API alltid kostnadsfritt.

Vill du öka produktiviteten genom att förbättra dokumentflödet? [Kom igång](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) idag.
