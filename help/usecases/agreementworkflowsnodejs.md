---
title: Avtalsarbetsflöden i Node.js
description: "[!DNL Adobe Acrobat Services] API:er kan enkelt införliva PDF-funktioner i dina webbprogram"
feature: Use Cases
role: Developer
level: Beginner
type: Tutorial
jira: KT-7473
thumbnail: KT-7473.jpg
keywords: Utvalt
exl-id: 44a03420-e963-472b-aeb8-290422c8d767
source-git-commit: f8a31b8f98d99bf1f3787e0f0f19cc9f26e24d8d
workflow-type: tm+mt
source-wordcount: '2094'
ht-degree: 0%

---

# Avtalsarbetsflöden i Node.js

![Banderoll för användningsfall](assets/UseCaseAgreementHero.jpg)

Många affärsprogram och processer kräver dokumentation som förslag och avtal. PDF-dokument gör filerna säkrare och mindre ändringsbara. De har även stöd för digitala signaturer så att dina kunder snabbt och enkelt kan fylla i sina dokument. Med [!DNL Adobe Acrobat Services] API:er kan du enkelt införliva PDF-funktioner i dina webbprogram.

## Vad du kan lära dig

I den här praktiska självstudiekursen lär du dig hur du lägger till PDF-tjänster i ett Node.js-program för att digitalisera en avtalsprocess.

## Relevanta API:er och resurser

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF Embed API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Projektkod](https://github.com/adobe/pdftools-node-sdk-samples)

## Konfigurerar [!DNL Adobe Acrobat Services]

Kom igång genom att konfigurera autentiseringsuppgifter för att använda [!DNL Adobe Acrobat Services]. Registrera ett konto och använd [Node.js Quickstart](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#node-js) för att verifiera att dina inloggningsuppgifter fungerar innan du integrerar funktionen i ett större program.

Först måste du skaffa ett utvecklarkonto för Adobe. På sidan [Kom igång](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html?ref=getStartedWithServicesSDK) väljer du alternativet *Kom igång* under Skapa nya autentiseringsuppgifter. Du kan registrera dig för en kostnadsfri demoversion som innehåller 1 000 dokumenttransaktioner som kan användas under sex månader.

![Bild av att skapa nya autentiseringsuppgifter](assets/AWNjs_1.png)

På följande sida Skapa nya autentiseringsuppgifter uppmanas du att välja mellan PDF Embed API och PDF Services API.

Markera *PDF Services API*.

Ange ett namn för programmet och markera rutan *Skapa personligt kodexempel*. Om du markerar den här rutan bäddas dina inloggningsuppgifter automatiskt in i kodprovet. Om du låter rutan vara avmarkerad måste du manuellt lägga till dina inloggningsuppgifter i programmet.

Välj *Node.js* för programtypen och klicka på *Skapa autentiseringsuppgifter*.

En stund senare börjar en .zip-fil att hämtas med ett exempelprojekt som innehåller dina inloggningsuppgifter. Node.js-paketet för [!DNL Acrobat Services] ingår redan i exempelprojektkoden.

![Bild av val av API-uppgifter för PDF-tjänster](assets/AWNjs_2.png)

## Konfigurera exempelprojektet manuellt

Om du väljer att inte hämta ett exempelprojekt från sidan Skapa nya autentiseringsuppgifter kan du också konfigurera projektet manuellt.

Hämta koden (utan dina inloggningsuppgifter inbäddade) från [GitHub](https://github.com/adobe/pdftools-node-sdk-samples). Om du använder den här versionen av koden måste du lägga till dina inloggningsuppgifter i filen pdftools-api-credentials.json innan du använder den:

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

För ditt eget program måste du kopiera filen med den privata nyckeln och inloggningsuppgifterna till programkällan.

Du måste installera Node.js-paketet för [!DNL Acrobat Services]. Installera paketet med följande kommando:

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

## Konfigurera loggning

I exemplen här används Express för applikationsramverket. De använder också log4js för programloggning. Med log4js kan du enkelt dirigera loggning till konsolen eller ut till en fil:

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

Ovanstående kod skriver loggdata till en fil i ./logs/applicationlog.txt. Om du vill att den ska skriva till konsolen i stället, kan du kommentera samtalet till log4js.configure.

## Konvertera Word-filer till PDF

Avtal och förslag skrivs ofta i ett produktivitetsprogram, som Microsoft Word. Om du vill godkänna dokument i det här formatet och konvertera dokumentet till PDF kan du lägga till funktioner för programmet. Låt oss titta på hur du överför och sparar ett dokument i ett Express-program och sparar det i filsystemet.

Lägg till ett filelement och en knapp för att starta överföringen i programmets HTML:

```
<input type="file" name="source" id="source" />
<button onclick="upload()" >Upload</button>
```

Överför filen asynkront med hjälp av funktionen fetch i sidans JavaScript:

```
function upload()
{
  let formData = new FormData();
  var selectedFile = document.getElementById('source').files[0];
  formData.append("source", selectedFile);
  fetch('documentUpload', {method:"POST", body:formData});
}
```

Välj en mapp för att acceptera dina överförda filer. Programmet behöver en sökväg till den här mappen. Sök efter den absoluta sökvägen med hjälp av en relativ sökväg som har kopplats till \_\_dirname:

```
const uploadFolder = path.join(__dirname, "../uploads");
```

Eftersom filen skickas via post måste du svara på ett post-meddelande på serversidan:

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

När den här funktionen har körts sparas filen i programmappen för överföring och är tillgänglig för vidare bearbetning.

Konvertera sedan filen från dess ursprungliga format till PDF. Exempelkoden du hämtade tidigare innehåller ett skript med namnet `create-pdf-from-docx.js` för att konvertera ett dokument till PDF. Följande funktion, `convertDocumentToPDF`, tar ett uppladdat dokument och konverterar det till en PDF i en annan mapp:

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

Du kan märka ett allmänt mönster med koden:

Koden bygger ett referensobjekt och en körningskontext, initierar en åtgärd och utför sedan åtgärden med körningskontexten. Du kan se det här mönstret genom hela exempelkoden.

Genom att göra några tillägg till uppladdningsfunktionen så att den anropar den här funktionen, konverteras nu Word-dokument som användare laddar upp automatiskt till PDF.

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

Verktyget för dokument konverterar andra format till PDF, som statiska HTML, en annan vanlig dokumenttyp. Verktyget tar emot HTML-dokument som paketerats som en .zip-fil med alla resurser som dokumentet refererar till (CSS-filer, bilder och andra filer) i samma .zip-fil. Själva HTML-dokumentet måste heta index.html och placeras i roten för .zip-filen.

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

Funktionen `setCustomOptions` anger andra PDF-inställningar, t.ex. sidstorlek. Här kan du se funktionen ställer in sidstorleken till 11,5 x 11 tum:

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

När du öppnar ett HTML-dokument som innehåller vissa villkor visas följande i webbläsaren:

![Bild av datorvillkor](assets/AWNjs_3.png)

Dokumentets ursprung består av en CSS-fil och en HTML-fil:

![Bild av CSS- och HTML-fil](assets/AWNjs_4.png)

När du har bearbetat HTML-filen får du samma text i PDF-format:

![PDF-fil med datorvillkor](assets/AWNjs_5.png)

## Lägga till sidor

En annan vanlig åtgärd med PDF-filer är att lägga till sidor i slutet som kan ha standardtext, till exempel en lista med termer. Document Toolkit kan kombinera flera PDF-dokument till ett enda dokument. Om du har en lista med dokumentsökvägar (här i `sourceFileList`) kan du lägga till varje fils filreferenser till ett objekt för en kombinationsåtgärd.

När kombinationsåtgärden utförs tillhandahålls en enda fil med av källinnehållet. Du kan använda `saveAsFile` på objektet för att behålla filen i lagringen.

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

Du har utfört flera åtgärder på PDF-filer, men i slutändan måste användaren visa dokumenten. Du kan bädda in dokumentet i en webbsida med hjälp av Adobe PDF Embed API.

Lägg till ett `<div />`-element för dokumentet på sidan som visar PDF och ge det ett ID. Du använder detta ID inom kort. Inkludera en `<script />`-referens till JavaScript-biblioteket i Adobe på webbsidan:

```
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
```

Den sista kodbiten du behöver är en funktion som visar dokumentet när Adobe PDF Embed API JavaScript har lästs in. Skapa ett nytt AdobeDC.View-objekt när du får ett meddelande om att skriptet har lästs in via en händelse av typen adobe_dc_view\_sdk.ready. Ditt klient-ID och ID för elementet som skapades tidigare behövs för det här objektet. Hitta ditt klient-ID i [Adobe Developer Console](https://console.adobe.io/). När du visar inställningarna för programmet som du skapade när du skapade autentiseringsuppgifter tidigare visas klient-ID:t där.

![Bild av API-klientnyckel](assets/AWNjs_6.png)

## Andra PDF-alternativ

Med [Adobe PDF Embed API-demon](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf) kan du förhandsgranska andra alternativ för att bädda in PDF-dokument.

![Bild av alternativ för att bädda in PDF ](assets/AWNjs_7.png)

Du kan slå på och av olika alternativ och omedelbart se hur de återges. Klicka på knappen *\&lt;/\> Generera HTML-kod* när du hittar en kombination som du gillar för att generera den faktiska kodgenereringen med dessa alternativ.

![Bild av kodförhandsgranskning](assets/AWNjs_8.png)

## Lägga till digitala signaturer och säkerhet

När ett dokument är klart kan du lägga till digitala signaturer för godkännande med Adobe Sign. Den här funktionen fungerar lite annorlunda än den funktion du har använt hittills. För digitala signaturer måste ett program konfigureras för att använda OAuth för användarautentisering.

Det första steget när du konfigurerar programmet är att [registrera programmet](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/gstarted/create_app.md) för att använda OAuth för Adobe Sign. När du har loggat in går du till skärmen där du skapar program genom att klicka på *Konto*, öppna avsnittet *Adobe Sign API* och klicka på *API-program* för att öppna listan över registrerade program.

![Bild från första steget när du registrerar din app](assets/AWNjs_9.png)

Om du vill skapa en ny programpost klickar du på plusikonen i det övre högra hörnet.

![Bild på plusikonen i det övre högra hörnet av skärmen](assets/AWNjs_10.png)

I fönstret som öppnas anger du ett programnamn och ett visningsnamn. Välj *Kund* för domänen och klicka sedan på *Spara*.

![Bild på var du kan ange programnamn och visningsnamn](assets/AWNjs_11.png)

När programmet har skapats kan du välja det i listan och klicka på *Konfigurera OAuth för programmet*. Välj alternativen. Ange programmets URL i Omdirigerings-URL. Här kan du ange flera URL-adresser. För programmet du testar är värdet:

```
http://localhost:3000/signed-in 
```

Processen att använda OAuth för att erhålla en token är standard. Programmet dirigerar en användare till en URL för inloggning. När användaren har loggat in
de omdirigeras tillbaka till programmet med ytterligare information i sidans frågeparametrar.

För inloggnings-URL:en måste ditt program skicka ditt klient-ID, din omdirigerings-URL och en lista över de omfattningar som behövs.

Mönstret för URL-adressen ser ut så här:

```
https://secure.adobesign.com/public/oauth?
  redirect_uri=&
  response_type=code&
  client_id=&
  scope=
```

Användaren uppmanas att logga in på sitt ID för Adobe Sign. När de har loggat in tillfrågas de om de ska ge programmet behörigheter.

![Bild på bekräftad åtkomstskärm](assets/AWNjs_12.png)

Om användaren klickar på *Tillåt åtkomst* på omdirigerings-URL:en skickar en frågeparameter med namnet code auktoriseringskoden:

https://YourServer.com/?code=**\&lt;authorization_code\>**\&amp;api_access_point=https://api.adobesign.com&amp;web_access_point=https://secure.adobesign.com

Genom att publicera koden på Adobe Sign-servern tillsammans med ditt klient-ID och din klienthemlighet får du en åtkomsttoken för åtkomst till tjänsten. Spara värdena i parametrarna `api_access_point` och `web_access_point`. Dessa värden används för ytterligare begäranden.

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

När en signatur krävs för ett dokument måste dokumentet först överföras. Programmet kan överföra dokumentet till värdet `api_access_point` som togs emot när OAUTH-token begärdes. Slutpunkten är `/api/rest/v6/transientDocuments`. Data för begäran ser ut så här:

```
POST /api/rest/v6/transientDocuments HTTP/1.1
Host: api.na1.adobesign.com
Authorization: Bearer MvyABjNotARealTokenHkYyi
Content-Type: multipart/form-data
Content-Disposition: form-data; name=";File"; filename="MyPDF.pdf"
<PDF CONTENT>
```

Skapa begäran med följande kod i programmet:

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

Begäran returnerar ett `transientID`-värde. Dokumentet har överförts, men har inte skickats än. Om du vill skicka dokumentet använder du `transientID` för att begära att dokumentet skickas.

Börja med att skapa ett JSON-objekt som innehåller information om dokumentet som ska signeras. I följande exempel innehåller variabeln `transientDocumentId` ID:t från koden ovan och `agreementDescription` innehåller text som beskriver avtalet som måste signeras. De personer som ska signera dokumentet listas i `participantSetsInfo` efter deras e-postadress och roll.

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

Om signerarna glömmer att signera och behöver ett annat e-postmeddelande skickar du meddelandena igen med det ID du fick tidigare. Den enda skillnaden är att du också måste lägga till deltagar-ID:n för parterna. Du kan hämta deltagar-ID:n genom att skicka en begäran om GET till `/agreements/{agreementID}/members`.

Om du vill begära att påminnelsen skickas skapar du först ett JSON-objekt som beskriver begäran. Det minimala objektet kräver en lista över deltagar-ID:n och en status för påminnelsen (&quot;AKTIV&quot;, &quot;SLUTFÖRD&quot; eller &quot;AVBRUTEN&quot;).

Begäran kan även innehålla ytterligare information, till exempel ett värde för &quot;anteckning&quot; som visas för användaren. Eller en fördröjning (i timmar) att vänta tills påminnelsen skickas (i `firstReminderDelay`) och en påminnelsefrekvens (i fältet &quot;frequency&quot;), som accepterar värden som DAILY_UNTIL_SIGNED, EVERY_THIRD_DAY_UNTIL_SIGNED eller WEEKLY_UNTIL_SIGNED.

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

Och det är allt som krävs för att skicka en påminnelsebegäran.

![Bild på webbformulär](assets/AWNjs_13.png)

## Skapa webbformulär

Du kan också använda API:et för Adobe Sign för att skapa webbformulär. Webbformulär låter dig bädda in ett formulär på en webbsida eller länka direkt till den. När ett webbformulär har skapats visas det också bland webbformulären i Adobe Sign Console. Du kan skapa webbformulär med UTKAST-status för inkrementell uppbyggnad, REDIGERINGSstatus för redigering av webbformulärfälten och ACTIVE-status för att omedelbart vara värd för formuläret.

![Bild av webbformulär på skärmen Hantera i Adobe Sign](assets/AWNjs_14.png)

Använd formuläret `transientDocumentId` om du vill skapa ett webbformulär. Bestäm en rubrik för formuläret och en status för att initiera det.

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

Nu kan du bädda in eller länka till ditt dokument.

## Nästa steg

Som du kan se på snabbstarterna och den medföljande koden är det enkelt att implementera PDF och digitala dokumentgodkännandeprocesser med hjälp av noden med API:erna [!DNL Adobe Acrobat Services]. Adobe API:er integreras sömlöst i dina befintliga klientprogram.

Om du vill identifiera de nödvändiga omfången för ett anrop eller se hur anropet skapas kan du skapa exempelanrop från [REST API-dokumentationen](https://secure.na4.adobesign.com/public/docs/restapi/v6). [Snabbstarterna](https://github.com/adobe/pdftools-node-sdk-samples) demonstrerar också andra funktioner och filformat för API:erna i [!DNL Adobe Acrobat Services].

Du kan lägga till en mängd PDF-funktioner till dina program, så att användarna snabbt och enkelt kan visa och signera sina dokument och mycket mer. Börja med att kolla in [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) i dag.
