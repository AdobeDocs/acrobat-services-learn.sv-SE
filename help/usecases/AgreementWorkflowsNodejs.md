---
title: Avtalsarbetsflöden i Node.js
description: "[!DNL Adobe Acrobat Services] API:er införlivar enkelt PDF-funktioner i webbapplikationerna"
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-7473.jpg
jira: KT-7473
keywords: Aktuellt
exl-id: 44a03420-e963-472b-aeb8-290422c8d767
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '2182'
ht-degree: 1%

---

# Avtalsarbetsflöden i Node.js

![BANDEROLL MED ANVÄNDNINGSFALL](assets/UseCaseAgreementHero.jpg)

Många affärsprogram och processer kräver dokumentation som förslag och avtal. PDF-dokument gör filerna säkrare och mindre ändringsbara. De har också stöd för digitala signaturer så att kunderna snabbt och enkelt kan fylla i sina dokument. [!DNL Adobe Acrobat Services] API:er kan enkelt införliva PDF-funktioner i dina webbprogram.

## Vad du kan lära dig

I den här praktiska självstudiekursen lär du dig hur du lägger till PDF-tjänster i ett Node.js-program för att digitalisera en avtalsprocess.

## Relevanta API:er och resurser

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF Embed API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Projektkod](https://github.com/adobe/pdftools-node-sdk-samples)

## Konfigurera [!DNL Adobe Acrobat Services]

Konfigurera autentiseringsuppgifter för att komma igång [!DNL Adobe Acrobat Services]. Registrera ett konto och använd [Node.js QuickStart](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#node-js) för att verifiera dina inloggningsuppgifter innan du integrerar funktionerna i ett större program.

Först måste du skaffa ett utvecklarkonto för Adobe. På sidan [Kom igång](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html?ref=getStartedWithServicesSDK) väljer du *Kom igång* under Skapa nya inloggningsuppgifter. Du kan registrera dig för deras kostnadsfria testversion som innehåller 1 000 dokumenttransaktioner som kan användas under sex månader.

![Bild av att skapa nya inloggningsuppgifter](assets/AWNjs_1.png)

På den följande sidan Skapa ny inloggningsinformation uppmanas du att välja mellan PDF Embed API och PDF Services API.

Välj *PDF Services API*.

Ange ett namn för programmet och markera rutan med etiketten *Skapa personligt kodexempel*. Om du markerar den här rutan bäddas dina inloggningsuppgifter automatiskt in i kodexemplet. Om du inte markerar rutan måste du lägga till dina inloggningsuppgifter manuellt i programmet.

Välj *Node.js* för programtypen och klicka på *Skapa autentiseringsuppgifter*.

En liten stund senare börjar en ZIP-fil hämtas med ett exempelprojekt som innehåller dina inloggningsuppgifter. Node.js-paketet för [!DNL Acrobat Services] ingår redan i exempelprojektkoden.

![Bild av val av API-uppgifter för PDF Services](assets/AWNjs_2.png)

## Konfigurera exempelprojektet manuellt

Om du väljer att inte hämta ett exempelprojekt från sidan Skapa nya inloggningsuppgifter kan du även konfigurera projektet manuellt.

Hämta koden (utan att dina inloggningsuppgifter är inbäddade) från [GitHub](https://github.com/adobe/pdftools-node-sdk-samples). Om du använder den här versionen av koden måste du lägga till dina inloggningsuppgifter i filen pdftools-api-credentials.json innan du använder:

```
{
  "client_credentials": {
    "client_id": "<client_id>",
    "client_secret": "<client_secret>"
  },
  "service_account_credentials": {
    "organization_id": "<organization_id>",
    "account_id": "<technical_account_id>",
    "private_key_file": "<private_key_file_path>"
  }
}
```

För ditt eget program måste du kopiera den privata nyckelfilen och inloggningsfilerna till programkällan.

Du måste installera Node.js -paketet för [!DNL Acrobat Services]. Använd följande kommando för att installera paketet:

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

## Konfigurera loggning

Proverna här använder Express för applikationsramverket. De använder också log4js för programloggning. Med log4js kan du enkelt dirigera loggning till konsolen eller ut till en fil:

```
const log4js = require('log4js');
const logger = log4js.getLogger();
log4js.configure( {
    appenders: { fileAppender: { type:'file', filename: './logs/applicationlog.txt'}},
    categories: { default: {appenders: ['fileAppender'], level:'info'}}
});
 
logger.level = 'info';
logger.info('Application started')
```

Ovanstående kod skriver loggade data till en fil i ./logs/applicationlog.txt. Om du vill att den ska skriva till konsolen i stället, kan du kommentera ut samtalet till log4js.configure.

## Konvertera Word-filer till PDF

Avtal och förslag skrivs ofta i produktivitetsprogram, som Microsoft Word. Om du vill acceptera dokument i det här formatet och konvertera dokumentet till PDF kan du lägga till funktioner för programmet. Låt oss titta på hur man laddar upp och sparar ett dokument i ett Express-program och sparar det i filsystemet.

I programmets HTML lägger du till ett filelement och en knapp för att starta överföringen:

```
<input type="file" name="source" id="source" />
<button onclick="upload()" >Upload</button>
```

I sidans JavaScript-skript överför du filen asynkront med hjälp av funktionen fetch:

```
function upload()
{
  let formData = new FormData();
  var selectedFile = document.getElementById('source').files[0];
  formData.append("source", selectedFile);
  fetch('documentUpload', {method:"POST", body:formData});
}
```

Välj en mapp där du vill acceptera de överförda filerna. Programmet behöver en sökväg till den här mappen. Du hittar den absoluta sökvägen genom att använda en relativ sökväg som är kopplad till \_\_dirname:

```
const uploadFolder = path.join(__dirname, "../uploads");
```

Eftersom filen skickas via post måste du svara på ett meddelande på serversidan:

```
router.post('/', (req, res, next) => {
  console.log('uploading')
  if(!req.files || Object.keys(req.files).length === 0) {
  return res.status(400).send('No files were uploaded');
  }
    
  const uploadPath = path.join(uploadFolder, req.files.source.name);
  var buffer = req.files.source.data;
  var result = {"success":true};
  fs.writeFile(uploadPath, buffer, 'binary', (err)=> {
    if(err) {
      result.success = false;
    }
    res.json(result);
  });       
});
```

När den här funktionen har körts sparas filen i programmets överföringsmapp och är tillgänglig för vidare bearbetning.

Konvertera sedan filen från dess ursprungliga format till PDF. Exempelkoden som du hämtade tidigare innehåller ett skript med namnet `create-pdf-from-docx.js` för konvertering av ett dokument till PDF. Följande funktion, `convertDocumentToPDF`, tar ett överfört dokument och konverterar det till en PDF i en annan mapp:

```
function convertDocumentToPDF(sourcePath, destinationPath)
{    
  try {   
    const credentials = PDFToolsSDK.Credentials
    .serviceAccountCredentialsBuilder()
    .fromFile("pdftools-api-credentials.json")
    .build();
 
    const executionContext = 
      PDFToolsSDK.ExecutionContext.create(credentials),
    createPdfOperation = PDFToolsSDK.CreatePDF.Operation.createNew();
 
    const docxReadableStream = fs.createReadStream(sourcePath);
    const input = PDFToolsSDK.FileRef.createFromStream(
      docxReadableStream, 
      PDFToolsSDK.CreatePDF.SupportedSourceFormat.docx);
    createPdfOperation.setInput(input);
 
    createPdfOperation.execute(executionContext)
    .then(result => result.saveAsFile(destinationPath))
    .catch(err => {        
      logger.erorr('Exception encountered while executing operation');        
    })
  }
  catch(err) {        
    logger.error(err);
  }
}
```

Du kan se ett allmänt mönster med koden:

Koden bygger ett autentiseringsobjekt och en körningskontext, initierar en åtgärd och utför sedan åtgärden med körningskontexten. Det här mönstret syns igenom hela provkoden.

Genom att göra några tillägg till överföringsfunktionen så att den anropar den här funktionen, konverteras nu Word-dokument som användare överför automatiskt till PDF.

I följande kod skapas målsökvägen för det konverterade PDF och konverteringen initieras:

```
const documentFolder = path.join(__dirname, "../docs");
var extPosition = req.files.source.name.lastIndexOf('.') - 1;
if(extPosition < 0 ) {
  extPosition = req.files.source.name.length
}
const destinationName = path.join(documentFolder,  
  req.files.source.name.substring(0, extPosition) + '.pdf');
console.log(destinationName);
 
logger.info('converting to ${destinationName}')
  convertDocumentToPDF(uploadPath, destinationName);
```

## Konvertera andra filtyper till PDF

Med verktyget för dokument kan du konvertera andra format till PDF, som statiska HTML, en annan vanlig dokumenttyp. Verktyget tar emot HTML-dokument som paketerats som en ZIP-fil med alla resurser som dokumentet refererar till (CSS-filer, bilder och andra filer) i samma ZIP-fil. Själva HTML-dokumentet måste heta index.html och placeras i roten för ZIP-filen.

Om du vill konvertera en .zip-fil som innehåller HTML använder du följande kod:

```
//Create an HTML to PDF operation and provide the source file to it
htmlToPDFOperation = PDFToolsSdk.CreatePDF.Operation.createNew();     
const input = PDFToolsSdk.FileRef.createFromLocalFile(sourceZipFile);
htmlToPDFOperation.setInput(input);
 
// custom function for setting options
setCustomOptions(htmlToPDFOperation);
 
// Execute the operation and Save the result to the specified location.
htmlToPDFOperation.execute(executionContext)
  .then(result => result.saveAsFile(destinationPdfFile))
  .catch(err => {
    logger.error('Exception encountered while executing operation');
});
```

Funktionen `setCustomOptions` anger andra PDF-inställningar, till exempel sidstorlek. Här visas funktionen ställer in sidstorleken till 11,5 x 11 tum:

```
const setCustomOptions = (htmlToPDFOperation) => {    
  const pageLayout = new PDFToolsSdk.CreatePDF.options.PageLayout();
  pageLayout.setPageSize(11.5, 8);

  const htmlToPdfOptions = 
    new PDFToolsSdk.CreatePDF.options.html.CreatePDFFromHtmlOptions.Builder()
    .includesHeaderFooter(true)
    .withPageLayout(pageLayout)
    .build();
  htmlToPDFOperation.setOptions(htmlToPdfOptions);
};
```

När du öppnar ett HTML-dokument som innehåller vissa termer visas följande i webbläsaren:

![Bild av datorvillkor](assets/AWNjs_3.png)

Källan för det här dokumentet består av en CSS-fil och en HTML-fil:

![Bild av CSS- och HTML-fil](assets/AWNjs_4.png)

När du har bearbetat HTML-filen får du samma text i PDF-format:

![PDF-fil med datorvillkor](assets/AWNjs_5.png)

## Lägga till sidor

En annan vanlig åtgärd för PDF-filer är att lägga till sidor i slutet som kan ha standardtext, till exempel en lista med termer. Verktygssetet för dokument kan kombinera flera PDF-dokument till ett enda dokument. Om du har en lista över dokumentsökvägar (här i `sourceFileList`) kan du lägga till varje fils filreferenser till ett objekt för en kombinationsåtgärd.

När kombinationsåtgärden körs visas en enda fil med källinnehållet. Du kan använda `saveAsFile` på objektet för att behålla filen i lagring.

```
const executionContext = PDFToolsSDK.ExecutionContext.create(credentials);
var combineOperation = PDFToolsSDK.CombineFiles.Operation.createNew();
 
sourceFileList.forEach(f => {
  var combinedSource = PDFToolsSDK.FileRef.createFromLocalFile(f);
  console.log(f);
  combineOperation.addInput(combinedSource);
});
    
 
combineOperation.execute(executionContext)
  .then(result=>result.saveAsFile(destinationFile))
  .catch(err => {
    logger.error(err.message);
});    
```

## Visa PDF-dokument

Du har utfört flera åtgärder på PDF-filer, men i slutändan måste användaren visa dokumenten. Du kan bädda in dokumentet på en webbsida med hjälp av Adobe PDF Embed API.

På sidan med PDF lägger du till en `<div />` -element som dokumentet ska lagras i och ett ID ges. Du använder detta ID inom kort. Inkludera en `<script />` Referens till JavaScript-biblioteket i Adobe:

```
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
```

Den sista kodbiten du behöver är en funktion som visar dokumentet när Adobe PDF Embed API JavaScript har lästs in. När du får ett meddelande om att skriptet har lästs in via en adobe_dc_view\_sdk.ready-händelse skapar du ett nytt AdobeDC.View-objekt. Det här objektet behöver ditt klient-ID och ID för elementet som skapades tidigare. Hitta ditt klient-ID i [Adobe Developer Console](https://console.adobe.io/). När du visar inställningarna för programmet som du skapade när du genererade autentiseringsuppgifter tidigare visas klient-ID:t där.

![Bild av API-klientnyckel](assets/AWNjs_6.png)

## Andra alternativ för PDF

Den [Adobe PDF Embed API-demo](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf) Med kan du förhandsvisa de andra alternativen för att bädda in PDF-dokument.

![Bild av alternativ för att bädda in PDF ](assets/AWNjs_7.png)

Du kan aktivera och inaktivera olika alternativ och omedelbart se hur de återges. När du hittar en kombination som du gillar klickar du på *\&lt;/\> Generera kod* för att generera HTML-koden med dessa alternativ.

![Bild av kodförhandsgranskning](assets/AWNjs_8.png)

## Lägga till digitala signaturer och säkerhet

När ett dokument är klart kan du lägga till digitala signaturer för godkännande med Adobe Sign. Den här funktionen fungerar lite annorlunda än den funktion du har använt hittills. För digitala signaturer måste ett program konfigureras att använda OAuth för användarautentisering.

Det första steget i att konfigurera programmet är att [registrera din ansökan](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/gstarted/create_app.md) för att använda OAuth för Adobe Sign. När du är inloggad går du till skärmen för att skapa program genom att klicka på *Konto* och öppna sedan *Adobe Sign API* och klicka på *API-program* för att öppna listan över registrerade ansökningar.

![Bild av första steget i att registrera programmet](assets/AWNjs_9.png)

Om du vill skapa en ny programpost klickar du på plusikonen i det övre högra hörnet.

![Bild på plusikonen i skärmens övre högra hörn](assets/AWNjs_10.png)

Ange ett programnamn och ett visningsnamn i fönstret som öppnas. Välj *Kund* för domänen och klicka sedan på *Spara*.

![Bild av var du ska ange programnamn och visningsnamn](assets/AWNjs_11.png)

När programmet har skapats kan du välja det i listan och klicka på *Konfigurera OAuth för program*. Välj alternativ. Ange programmets URL i omdirigerings-URL. Här kan du ange flera URL-adresser. För programmet som du testar är värdet:

```
http://localhost:3000/signed-in 
```

Processen att använda OAuth för att erhålla en token är standard. Programmet dirigerar en användare till en URL för inloggning. När användaren har loggat in omdirigeras användaren tillbaka till programmet med ytterligare information i sidans frågeparametrar.

För inloggnings-URL:en måste ditt program skicka ditt klient-ID, omdirigerings-URL och en lista över de omfång som behövs.

Mönstret för URL-adressen ser ut så här:

```
https://secure.adobesign.com/public/oauth?
  redirect_uri=&
  response_type=code&
  client_id=&
  scope=
```

Användaren uppmanas att logga in på sitt ID för Adobe Sign. När de har loggat in tillfrågas de om de vill ge behörigheter till programmet.

![Bild på bekräftelseskärmen](assets/AWNjs_12.png)

Om användaren klickar på *Tillåt åtkomst* På omdirigerings-URL skickar en frågeparameter med namnet code auktoriseringskoden:

https://YourServer.com/?code=**\&lt;authorization_code>**\&amp;api_access_point=https://api.adobesign.com&amp;web_access_point=https://secure.adobesign.com

Genom att lägga upp den här koden på Adobe Sign-servern tillsammans med ditt klient-ID och din klienthemlighet får du en åtkomsttoken för åtkomst till tjänsten. Spara värdena i parametrarna `api_access_point` och `web_access_point`. Dessa värden används för ytterligare begäranden.

```
var requestURL = ' ${api_access_point}oauth/token?code=${code}'
  +'&client_id=${client_id}'
  +'&client_secret=${client_secret}&'
  +'redirect_uri=${redirect_url}&'
  +'grant_type=authorization_code';
request.post(requestURL, {form: { }
}, (err,response,body)=>{                
    var token_response = JSON.parse(body)
    var access_token = token_response.access_token;
    console.log(access_token);
});
```

När ett dokument kräver en signatur måste dokumentet först överföras. Programmet kan överföra dokumentet till `api_access_point` värde som togs emot när OAUTH-token begärdes. Slutpunkten är `/api/rest/v6/transientDocuments`. Data för begäran ser ut så här:

```
POST /api/rest/v6/transientDocuments HTTP/1.1
Host: api.na1.adobesign.com
Authorization: Bearer MvyABjNotARealTokenHkYyi
Content-Type: multipart/form-data
Content-Disposition: form-data; name=";File"; filename="MyPDF.pdf"
<PDF CONTENT>
```

Bygg begäran med följande kod i programmet:

```
var uploadRequest = {
  'method': 'POST',
  'url': '${oauthParameters.signin_domain}/api/rest/v6/transientDocuments',
  'headers': {
    'Authorization': 'Bearer  ${auth_token}'
  },
  formData: {
    'File': {
      'value': fs.createReadStream(documentPath),
      'options': {
        'filename': fileName,
        'contentType': null
      }
    }
  }
};
 
request(uploadRequest, (error, response) => {
  if (error) throw new Error(error);
  var jsonResponse = JSON.parse(response.body);
  var transientDocumentId = jsonResponse.transientDocumentId;
  logger.info('transientDocumentId:', transientDocumentId)
});
```

Begäran returnerar en `transientID` värde. Dokumentet har överförts men har inte skickats än. Om du vill skicka dokumentet använder du `transientID` för att begära att få skicka dokumentet.

Börja med att skapa ett JSON-objekt som innehåller information om dokumentet som ska signeras. I det följande ska variabeln `transientDocumentId` innehåller ID:t från koden ovan och `agreementDescription` innehåller text som beskriver avtalet som kräver signatur. De personer som ska signera dokumentet visas i `participantSetsInfo` efter e-postadress och roll.

```
var requestBody = {
  "fileInfos":[
    {"transientDocumentId":transientDocumentId}],
    "name":agreementDescription,
    "participantSetsInfo":[
      {"memberInfos":[{"email":"user@domain.com"}],
       "order":1,"role":"SIGNER"}
    ],
    "signatureType":"ESIGN","state":"IN_PROCESS"
};
```

Om du skickar den här webbbegäran skapas signeringsbegäran och ett JSON-objekt med ett ID för avtalsbegäran returneras:

```
request(requestBody, function (error, response) {
  if (error) throw new Error(error);
  var JSONResponse = JSON.parse(response.body);
  var requestId = JSONResponse.id;
});
```

Om signerarna glömmer att signera och behöver ett annat e-postmeddelande skickar du meddelandena igen med det ID som du fick tidigare. Den enda skillnaden är att du också måste lägga till parternas deltagar-ID. Du kan hämta deltagar-ID:n genom att skicka en begäran om GET till `/agreements/{agreementID}/members`.

Om du vill begära att påminnelsen skickas måste du först skapa ett JSON-objekt som beskriver begäran. Det minimala objektet behöver en lista över deltagar-ID:n och en status för påminnelsen (&quot;ACTIVE&quot;, &quot;COMPLETE&quot; eller &quot;CANCELED&quot;).

Begäran kan även innehålla ytterligare information, till exempel ett värde för &quot;anteckning&quot; som visas för användaren. Eller så väntar en fördröjning (i timmar) tills påminnelsen skickas (i `firstReminderDelay`) och en påminnelsefrekvens (i fältet &quot;frequency&quot;), som accepterar värden som DAILY_UNTIL_SIGNED, EVERY_THIRD_DAY_UNTIL_SIGNED eller WEEKLY_UNTIL_SIGNED.

```
var requestBody = {
  //participantList is an array of participant ID strings
  "recipientParticipantIds":participantList
  ,"status":"ACTIVE",
  "note":"This is a reminder to sign out important agreement."
}
 
var reminderRequest = {
  'method': 'POST',
  'url': '${oauthParameters.signin_domain}/api/rest/v6/agreements/${agreementID}/reminders',
  'headers': {
    'Authorization': `Bearer ${access_token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(requestBody)
 
};

request(reminderRequest, function (error, response) {
});
```

Och det är allt som krävs för att skicka en påminnelse.

![Bild av webbformulär](assets/AWNjs_13.png)

## Skapa webbformulär

Du kan också använda API:et i Adobe Sign för att skapa webbformulär. Med webbformulär kan du bädda in ett formulär på en webbsida eller länka direkt till den. När ett webbformulär har skapats visas det också bland webbformulären i Adobe Sign-konsolen. Du kan skapa webbformulär med UTKAST-status för inkrementell uppbyggnad, REDIGERINGSstatus för redigering av webbformulärfält och aktiv status för att omedelbart vara värd för formuläret.

![Bild av webbformulär på skärmen Hantera i Adobe Sign](assets/AWNjs_14.png)

Om du vill skapa ett webbformulär använder du formuläret `transientDocumentId`. Bestäm en rubrik för formuläret och en status för att initiera det.

```
var requestBody = {
  "fileInfos": [
    {
      "transientDocumentId": transientDocumentId
    }
  ],
  "name": webFormTitle,
  "state": status,
  "widgetParticipantSetInfo": {
    "memberInfos": [ { "email": "" } ],
    "role": "SIGNER"
  }
}
```

```
var createWebFormRequest = {
  'method': 'POST',
  'url': `${oauthParameters.signin_domain}/api/rest/v6/widgets`,
  'headers': {
    'Authorization': `Bearer ${access_token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(requestBody)
}
```

```
request(createWebFormRequest, function (error, response) {
  var jsonResp = JSON.parse(response.body);
  var webFormID = jsonResp.id;
});
```

Nu kan du bädda in eller länka till dokumentet.

## Nästa steg

Som du kan se av snabbstarterna och den medföljande koden är det enkelt att implementera PDF och digitala dokumentgodkännandeprocesser med Node med [!DNL Adobe Acrobat Services] API:er. Adobe API:er integreras smidigt i dina befintliga klientprogram.

Du kan skapa exempelsamtal från [REST API-dokumentation](https://secure.na4.adobesign.com/public/docs/restapi/v6). Den [Snabbstarter](https://github.com/adobe/pdftools-node-sdk-samples) även visa andra funktioner och filformat som [!DNL Adobe Acrobat Services] API-processer.

Du kan lägga till en mängd PDF-funktioner i dina program, så att dina användare snabbt och enkelt kan visa och signera sina dokument och mycket mer. Börja med att kolla in [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) idag.
