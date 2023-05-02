---
title: Hantera medarbetares erbjudandebrev
description: Lär dig hur du genererar ett erbjudandebrev som kan levereras till en ny medarbetare för underskrift
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8096.jpg
kt: 8096
exl-id: 92f955f0-add5-4570-aa3a-ea63055dadb2
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1794'
ht-degree: 2%

---

# Hantera medarbetarnas offertbrev

![BANDEROLL MED ANVÄNDNINGSFALL](assets/UseCaseOfferHero.jpg)

Brev med anställningserbjudanden är en av de första upplevelser som anställda har med din organisation. Därför vill du försäkra dig om att erbjudandebreven är varumärkesanpassade, men du vill inte behöva skapa ett brev i ordbehandlingsprogrammet från grunden varje gång. [!DNL Adobe Acrobat Services] API:er erbjuder ett snabbt, enkelt och effektivt sätt att hantera viktiga delar av [skapa och leverera brev till nya medarbetare](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html).

## Vad du kan lära dig

Den här praktiska självstudiekursen visar hur du konfigurerar ett Node Express-projekt som visar ett webbformulär som en användare kan fylla i med information om anställda. Dessa uppgifter använder [!DNL Acrobat Services] på webben för att skapa ett erbjudande via PDF som kan skickas till en kund för signering med Adobe Sign API.

## Relevanta API:er och resurser

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Adobe-API för dokumentgenerering](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Dokumentgenereringstaggar, Word-tillägg](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin)

* [Projektexempel](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html)

## Komma igång

[Node.js](https://nodejs.org/) är programmeringsplattformen. Den levereras med en enorm uppsättning bibliotek, såsom Express-webbservern. [Hämta Node.js](https://nodejs.org/en/download/) och följ stegen för att installera denna fantastiska utvecklingsmiljö med öppen källkod.

Om du vill använda Adobe Document Generation API i Node.js går du till [API för dokumentgenerering](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html) webbplats för att komma åt ditt konto eller registrera dig för ett nytt. Ditt konto är [gratis i sex månader och sedan betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) för bara 0,05 USD per dokumenttransaktion, så du kan testa det riskfritt och sedan bara betala i takt med att företaget växer.

När du har loggat in på [Adobe Developer Console](https://console.adobe.io/)klickar du på **[!UICONTROL Skapa nytt projekt]**. Projektet kallas som standard &quot;Projekt 1&quot;. Klicka på **[!UICONTROL Redigera projekt]** och ändra namnet till &quot;Offer Letter Generator.&quot; I mitten av skärmen finns en **[!UICONTROL Kom igång med ditt nya projekt]** avsnitt. Gör så här för att aktivera säkerhet för projektet:

Klicka **Lägg till API**. Du ser ett antal API:er att välja mellan. I dialogrutan **[!UICONTROL Filtrera efter produkt]** väljer du **[!UICONTROL Document Cloud]** och klicka sedan på **[!UICONTROL Nästa]**.

Generera nu autentiseringsuppgifter för åtkomst till API:et. Autentiseringsuppgifterna har formen av en JSON Web Token ([JWT](https://jwt.io/)): en öppen standard för säker kommunikation. Om du känner till JWT och redan har genererat nycklar kan du överföra din offentliga nyckel här. Du kan också fortsätta genom att välja **Alternativ 1** så att Adobe kan generera nycklarna åt dig.

![Skärmdump av generering av autentiseringsuppgifter](assets/offer_1.png)

Klicka på **[!UICONTROL Generera knapptryckning]** -knappen. Du kan hämta filen config.zip. Packa upp arkivfilen. Den innehåller två filer: certificate_pub.crt och private.key. Se till att den senare hålls säker, eftersom den innehåller dina privata inloggningsuppgifter och kan användas för att generera falska dokument om du inte kan kontrollera det.

Klicka på **[!UICONTROL Nästa]**. Nej, aktiverar åtkomst till PDF Generation API. På **[!UICONTROL Välj produktprofiler]** skärm, kontrollera **[!UICONTROL Enterprise PDF Services Developer]** och klicka på **[!UICONTROL Spara konfigurerat API]** -knappen. Nu kan du börja använda API:et.

## Konfigurera projektet

Konfigurera ett nodprojekt som ska köra koden. I det här exemplet används [Visual Studio Code](https://code.visualstudio.com/) (VS-kod) som redigerare. Skapa en mapp med namnet &quot;letter-generator&quot; och öppna den i VS-kod. Från **[!UICONTROL Fil]** väljer du **[!UICONTROL Terminal]** \> **[!UICONTROL Ny terminal]** för att öppna ett skal i den här mappen. Kontrollera att Node är installerat och att det finns på sökvägen genom att ange följande:

```
node -v
```

Du bör se vilken version av Node som du har installerat.

Nu när du har installerat utvecklingsmiljön kan du skapa ett projekt.

Initiera först projektet med hjälp av Node Package Manager (npm). Ange följande:

```
npm init
```

Du får några frågor om ditt nodprojekt. Du kan hoppa över de flesta av dessa frågor, men se till att projektnamnet är &quot;Letter-generator&quot; och startpunkten är **index.js**. Välj **Ja** för att slutföra projektinitieringen.

Du har nu en package.json-fil. Nod använder den här filen för att organisera projektet. Innan du skapar index.js måste du lägga till Adobe-bibliotek med följande kommando:

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

En ny mapp med namnet node_modules bör läggas till i projektet. I den här mappen hämtas alla bibliotek (som kallas beroenden i noden). Filen package.json uppdateras också med en hänvisning till Adobe PDF-tjänster.

Nu vill du installera Express som ditt lättviktiga ramverk för webben. Ange följande kommando:

```
npm install express –save
```

Precis som tidigare uppdateras avsnittet om beroenden i package.json.

## Skapa en mall för erbjudandebrev

Nu skapar du en fil med namnet app.js i projektets rot. Vi lägger in följande startkod där:

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

Observera att get-vägen returnerar en **index.html** fil. Låt oss skapa en HTML-fil med det namnet och följande enkla formulär. Du kan lägga till CSS-format och andra designelement senare när du vill. Formuläret innehåller kandidatens grundläggande information för att skapa ett välkomstbrev:

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

Du bör se meddelandet &quot;Kandidaterbjudande brev app lyssna på port 8000&quot;. Om du öppnar webbläsaren i <http://localhost:8000/>ska formuläret se ut så här:

![Skärmdump av webbformulär](assets/offer_2.png)

Lägg märke till att formuläret postar på sig själv. Om du fyller i data och klickar på **Generera brev,** bör du se följande information på konsolen:

```
Got body: { firstname: 'John',
lastname: 'Doe',
salary: '887888',
startdate: '2021-04-01' }
```

Du ersätter den här konsolloggningen med ett webbtjänstanrop till [!DNL Acrobat Services]. Först måste du skapa en JSON-baserad modell av informationen. Formatet på den här modellen ser ut så här:

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

Du kan göra den här modellen mer avancerad om du vill, men för den här självstudiekursen ska du hålla dig till det här enkla exemplet. Det finns ingen validering i det här formuläret eftersom detta ligger utanför artikelns räckvidd. Om du vill konvertera formulärtexten till datamodellen som beskrivs ovan ändrar du metoden app.post-hanterare så att den har följande kod:

```
app.post('/', (req, res) => {
const docModel = {'offer_letter': req.body};
generateLetter(docModel);
res.sendStatus(200);
});
```

Den första raden placerar dina JSON-data i önskat format. Nu skickar du dessa data till en generateLetter-funktion. Stoppa servern och klistra in följande kod i slutet av app.js. Den här koden tar ett Word-dokument som mall och fyller i platshållare med information från ett JSON-dokument.

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

Det finns mycket kod att packa upp där. Låt oss ta huvuddelen först: den `documentMergeOperation`. Här sammanfogar du JSON-data med en Word-dokumentmall. Du kan använda kommandot [exempel på webbplatsen Adobe](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html#sample-blade) som en referens, men låt oss göra ditt eget enkla exempel. Öppna Word och skapa ett nytt tomt dokument. Du kan anpassa den så mycket du vill, men du har åtminstone något som detta:

Hej X!

Vi är glada att erbjuda dig en position för $X ett år. Startdatum blir X.

Välkommen

Spara dokumentet som &quot;OfferLetter-Template.docx&quot; i en mapp med namnet &quot;resources&quot; i projektets rot. Lägg märke till de tre Xs:en i dokumentet. Dessa Xs är tillfälliga platshållare för din JSON-information. Även om du kan ersätta de här platshållarna med en särskild syntax, innehåller Adobe ett Word-tillägg som gör det enklare att utföra den här åtgärden. Gå till Adobe för att installera tillägget [Dokumentgenereringstaggar, Word-tillägg](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin) webbplats.

Klicka på den nya **Dokumentgenerering** -knappen. En sidopanel öppnas. Klicka på **Kom igång**. Du får ett textområde att klistra in i JSON-exempeldata. Kopiera JSON-fragmentet &quot;offer-data&quot; ovanifrån till textområdet. Det ska se ut så här:

![Skärmdump av brev och kod](assets/offer_3.png)

Klicka på **Generera taggar** -knappen. En nedrullningsbar meny med taggar visas där du kan infoga taggar vid lämpliga punkter i dokumentet. Markera det första krysset i dokumentet och välj **[!UICONTROL förnamn]**. Klicka **[!UICONTROL Infoga text]** och &quot;Kära X,&quot; ändras till &quot;Kära ```{{`offer_letter`.firstname}}```&quot;. Det här märkordet har rätt format för `documentMergeOperation`. Fortsätt och lägg till de återstående tre taggarna vid rätt Xs. Glöm inte att spara OfferLetter-template.docx. Det ska se ut så här:

Hej ```{{`offer_letter`.firstname}} {{`offer_letter`.lastname}}```!

Vi är glada att erbjuda dig en position för $ ```{{`offer_letter`.salary}}``` ett år. Startdatum blir ```{{`offer_letter`.startdate}}```.

Välkommen

Nu har Word-mallen en kod som matchar JSON-formatet. Exempel: ```{{`offer_letter`.`firstname`}}``` i början av Word-dokumentet ersätts av värdet i avsnittet &quot;förnamn&quot; i JSON-data.

Tillbaka till `generateLetter` funktion. För att skydda ditt REST-anrop skapar du en ny fil med titeln pdftools-api-credentials.json i projektets rot. Klistra in följande JSON-data och justera dem med information från tjänstkontot (JWT) i [Developer Console](https://console.adobe.io/).

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

* Klient-ID, klienthemlighet och organisations-ID kan kopieras direkt från **[!UICONTROL Autentiseringsuppgifter]** i konsolen.

* Konto-ID är **ID för tekniskt konto**.

* Kopiera filen private.key som du genererade tidigare till projektet och ange dess namn i avsnittet private_key_file i filen pdftools-api-credentials.json . Om du vill kan du ange en sökväg till den privata nyckelfilen här. Kom ihåg att hålla den säker eftersom den kan missbrukas en gång utanför din kontroll.

Om du vill skapa en PDF med ifyllda JSON-data går du tillbaka till **[!UICONTROL Ange information om kandidat]** webbformulär och publicera data. Det tar en liten stund eftersom dokumentet måste hämtas från Adobe, men du bör ha en fil med namnet OfferLetter.pdf i en ny mapp med namnet output.

## Nästa steg

Nu räcker det! Detta är bara början. Om du studerar avsnittet Avancerat på fliken Dokumentgenerering i Word-tillägget, ser du att inte alla platshållarmarkörer kommer från tillhörande JSON-data. Du kan också lägga till signaturtaggar. Med dessa taggar kan du ta det resulterande dokumentet och överföra det till [Adobe Sign](https://acrobat.adobe.com/ca/en/sign.html) för leverans och signering till den nya medarbetaren. Läs Komma igång med Adobe Sign API och lär dig hur du gör det. Den här processen är liknande eftersom du använder REST-anrop som är skyddade med en JWT-token.

Ovanstående exempel på ett enda dokument kan användas som grund för en ansökan när en organisation måste [säsongsanställning](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html) över flera platser. Det huvudsakliga flödet är, vilket har visats, att hämta in data från kandidater genom en onlineansökan. Uppgifterna används för att fylla i fälten i ett erbjudandebrev och skicka ut det för elektronisk signering.

[!DNL Adobe Acrobat Services] får användas i sex månader, därefter [betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) för endast 0,05 USD per dokumenttransaktion, så du kan testa det och skala arbetsflödet för erbjudandebrev när verksamheten växer. till [kom igång](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
bygga egna mallar, [registrera ditt utvecklarkonto](https://www.adobe.io/).
