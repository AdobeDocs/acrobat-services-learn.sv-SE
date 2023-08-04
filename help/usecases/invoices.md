---
title: Hantera fakturor
description: Lär dig skapa, lösenordsskydda och leverera kundfakturor automatiskt
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8145
thumbnail: KT-8145.jpg
exl-id: 5871ef8d-be9c-459f-9660-e2c9230a6ceb
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1427'
ht-degree: 1%

---

# Hantera fakturor

![Banderoll för användningsfall](assets/UseCaseInvoicesHero.jpg)

Det är fantastiskt när verksamheten blomstrar men produktiviteten blir lidande när det är dags att förbereda alla dessa fakturor. Att skapa fakturor manuellt är tidskrävande, plus att du löper risken att göra ett fel, potentiellt förlora pengar eller reta upp en kund med ett felaktigt belopp.

Tänk på Danielle, till exempel, som jobbar i [redovisningsavdelning](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html) [för ett medicinskt försörjningsföretag](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html). Det är slutet av månaden, så hon hämtar information från flera olika system, dubbelkontrollerar att den är korrekt och formaterar fakturorna. Efter allt det arbetet är hon äntligen redo att konvertera dokumenten till PDF (så att alla kan visa dem utan att köpa specifik programvara) och skicka en personlig faktura till varje kund.

Även när månadsfaktureringen är klar kan Danielle inte komma undan fakturorna. Vissa kunder har faktureringscykler som inte är månatliga, så hon skapar alltid en faktura åt någon. Ibland redigerar en kund sin faktura och betalar för lite. Danielle lägger sedan tid på att felsöka felmatchningen av fakturan. I den här takten måste hon anlita en assistent för att hålla jämna steg med allt arbete!

Det Danielle behöver är ett sätt att generera fakturor snabbt och korrekt, både satsvis i slutet av månaden och ad hoc vid andra tillfällen. Helst, om hon kunde skydda dessa fakturor från redigeringar, hon skulle inte behöva oroa sig för att felsöka felmatchade belopp.

## Vad du kan lära dig

I den här praktiska självstudiekursen får du lära dig hur du använder Adobe dokumentgenererings-API för att automatiskt generera fakturor, lösenordsskydda PDF och leverera en faktura till varje kund. Allt som krävs är lite kunskap om Node.js, JavaScript, Express.js, HTML och CSS.

Den fullständiga koden för det här projektet är [finns på GitHub](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation). Du måste konfigurera den gemensamma katalogen med din mall och rådatamapparna. Under produktionen måste du hämta data från ett externt API. Du kan också utforska den här arkiverade versionen av programmet som innehåller mallresurserna.

## Relevanta API:er och resurser

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Adobe-dokumentgenererings-API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Projektkod](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation)

## Förbereda data

I den här självstudiekursen tittar vi inte på hur data importeras från dina datalager. Dina kundorder kan finnas i en databas, ett externt API eller i anpassad programvara. Adobe Document Generation API förväntar sig ett JSON-dokument som innehåller faktureringsdata, t.ex. information från din kundrelationshantering (CRM) eller eCommerce-plattform. I den här självstudiekursen antas data redan ha JSON-format.

För enkelhetens skull använder du följande JSON-struktur för fakturering:

```
{ 
    "customerName": "John Doe", 
    "customerEmail": "john-doe@example.com", 
    "order": [ 
        { 
            "productId": 26, 
            "productTitle": "Bandages", 
            "price": 15.82 
        }, 
        { 
            "productId": 54, 
            "productTitle": "Masks", 
            "price": 25 
        }, 
        { 
            "productId": 76, 
            "productTitle": "Gloves", 
            "price": 7.59 
        } 
    ] 
} 
```

JSON-dokumentet innehåller kundinformation och orderinformation. Använd det här strukturerade dokumentet för att bygga upp din faktura och visa elementen i PDF-format.

## Skapa en fakturamall

Adobe Document Generation API förväntar sig en Microsoft Word-baserad mall och ett JSON-dokument för att skapa ett dynamiskt PDF- eller Word-dokument. Skapa en Microsoft Word-mall för ditt faktureringsprogram och använd [Kostnadsfritt tillägg för taggning för dokumentgenerering](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo) för att generera malltaggar. Installera tillägget och öppna fliken i Microsoft Word.

![Skärmbild av taggningstillägget för dokumentgenerering](assets/invoices_1.png)

När du har klistrat in JSON-innehållet i tillägget, enligt ovan, klickar du på Generera taggar. Nu visar detta plugin ditt objekts format. I din grundläggande mall kan kundens namn och e-postadress användas, men orderinformationen visas inte. Orderinformationen diskuteras senare i den här självstudiekursen.

![Skärmbild av dokumentgenereringens taggningsförfattarmall](assets/invoices_2.png)

Börja skriva fakturamallen i ditt Microsoft Word-dokument. Lämna markören där du måste infoga dynamiska data och välj sedan taggen från tilläggsfönstret i Adobe. Klicka **Infoga text** så att tillägget Adobe Document Generation Tagger kan generera och infoga taggarna. För personlig anpassning infogar vi kundens namn och e-postadress.

Gå nu vidare till de data som ändras för varje ny faktura. Välj **Avancerat** fliken för tillägget. Klicka på om du vill se tillgängliga alternativ för att generera en dynamisk tabell baserat på de produkter en kund beställt **Tabeller och listor** .

Välj **Ordning** från den första listrutan. I den andra listrutan väljer du kolumnerna för den här tabellen. I den här självstudiekursen markerar du alla tre kolumner för objektet som ska återge tabellen.

![Skärmbild av fliken Avancerat för dokumentgenereringstagg](assets/invoices_3.png)

Dokumentgenererings-API:t kan också utföra komplexa åtgärder som att aggregera element i en array. I dialogrutan **Avancerat** -fliken, välj **Numeriska beräkningar** och i **Sammansättning** markerar du det fält där du vill göra beräkningen.

![Skärmbild av dokumentgenerering med numeriska beräkningar](assets/invoices_4.png)

Klicka på **Infoga beräkning** för att infoga den här taggen där det behövs i dokumentet. Följande text visas nu i din Microsoft Word-fil:

![Skärmbild av taggar i Microsoft Word-dokument](assets/invoices_5.png)

Det här fakturaexemplet innehåller kundinformation, beställda produkter och det totala förfallna beloppet.

## Generera en faktura med Adobe dokumentgenererings-API

Använd Adobe PDF Services Node.js software development kit (SDK) för att kombinera Microsoft Word- och JSON-dokumenten. Skapa ett Node.js-program för att skapa fakturan med hjälp av dokumentgenererings-API:t.

PDF Services API innehåller dokumentgenereringstjänsten, så du kan använda samma autentiseringsuppgifter för båda. Njut av en [sex månaders kostnadsfri provperiod](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html), betala sedan bara $0,05 per dokumenttransaktion.

Här är koden för att slå samman PDF:

```
async function compileDocFile(json, inputFile, outputPdf) { 
    try { 
        // configurations 
        const credentials =  adobe.Credentials 
            .serviceAccountCredentialsBuilder() 
            .fromFile("./src/pdftools-api-credentials.json") 
            .build(); 

        // Capture the credential from app and show create the context 
        const executionContext = adobe.ExecutionContext.create(credentials); 
  
        // create the operation 
        const documentMerge = adobe.DocumentMerge, 
            documentMergeOptions = documentMerge.options, 
            options = new documentMergeOptions.DocumentMergeOptions(json, documentMergeOptions.OutputFormat.PDF);

        const operation = documentMerge.Operation.createNew(options); 
  
        // Pass the content as input (stream) 
        const input = adobe.FileRef.createFromLocalFile(inputFile); 
        operation.setInput(input); 
  
        // Async create the PDF 
        let result = await operation.execute(executionContext); 
        await result.saveAsFile(outputPdf); 
    } catch (err) { 
        console.log('Exception encountered while executing operation', err); 
    } 
} 
```

Den här koden hämtar information från det inmatade JSON-dokumentet och inmatningsmallfilen. Sedan skapas en dokumentsammanfogningsåtgärd för att kombinera filerna i en enda PDF-rapport. Slutligen utförs åtgärden med dina API-inloggningsuppgifter. Om du inte redan har dem, [skapa autentiseringsuppgifter](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#getting-credentials) (Dokumentgenererings- och PDF Services API använder samma autentiseringsuppgifter).

Använd den här koden i Express-routern för att hantera dokumentbegäran:

```
// Create one report and send it back
try {
    console.log(\`[INFO] generating the report...\`);
    const fileContent = fs.readFileSync(\`./public/documents/raw/\${vendor}\`,
    'utf-8');
    const parsedObject = JSON.parse(fileContent);

    await pdf.compileDocFile(parsedObject,
    \`./public/documents/template/Adobe-Invoice-Sample.docx\`,
    \`./public/documents/processed/output.pdf\`);

    await pdf.applyPassword("p@55w0rd", './public/documents/processed/output.pdf',
    './public/documents/processed/output-secured.pdf');

    console.log(\`[INFO] sending the report...\`);
    res.status(200).render("preview", { page: 'invoice', filename: 'output.pdf' });
} catch(error) {
    console.log(\`[ERROR] \${JSON.stringify(error)}\`);
    res.status(500).render("crash", { error: error });
}
```

När den här koden körs visas ett PDF-dokument som innehåller den dynamiskt genererade fakturan baserat på de data som anges. Med JSON-exempeldata (se ovan) blir utdata för den här koden:

![Skärmbild av dynamiskt genererad PDF-faktura](assets/invoices_6.png)

Den här fakturan innehåller dynamiska data från JSON-dokumentet.

## Lösenordsskydda fakturor

Eftersom revisorn Danielle är orolig för kunder som ändrar fakturan ska du använda ett lösenord för att begränsa redigeringen. [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html) Du kan automatiskt tillämpa ett lösenord på dokument. Här använder du Adobe PDF Services SDK för att skydda dokument med ett lösenord. Koden är:

```
async function applyPassword(password, inputFile, outputFile) {
    try {
        // Initial setup, create credentials instance.
        const credentials = adobe.Credentials
        .serviceAccountCredentialsBuilder()
        .fromFile("./src/pdftools-api-credentials.json")
        .build();

        // Create an ExecutionContext using credentials
        const executionContext = adobe.ExecutionContext.create(credentials);
        // Create new permissions instance and add the required permissions
        const protectPDF = adobe.ProtectPDF,
        protectPDFOptions = protectPDF.options;
        // Build ProtectPDF options by setting an Owner/Permissions Password, Permissions,
        // Encryption Algorithm (used for encrypting the PDF file) and specifying the type of content to encrypt.
        const options = new protectPDFOptions.PasswordProtectOptions.Builder()
        .setOwnerPassword(password)
        .setEncryptionAlgorithm(protectPDFOptions.EncryptionAlgorithm.AES_256)
        .build();

        // Create a new operation instance.
        const protectPDFOperation = protectPDF.Operation.createNew(options);

        // Set operation input from a source file.
        const input = adobe.FileRef.createFromLocalFile(inputFile);
        protectPDFOperation.setInput(input);

        // Execute the operation and Save the result to the specified location.
        let result = await protectPDFOperation.execute(executionContext);

        result.saveAsFile(outputFile);
    } catch (err) {
        console.log('Exception encountered while executing operation', err);
    }
}
```

När du använder den här koden skyddas dokumentet med ett lösenord och en ny faktura överförs till systemet. Mer information om hur den här koden används eller hur du testar den finns i [kodexempel](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation).

När du är klar med fakturan kanske du vill skicka den till klienten automatiskt. Det finns några sätt att åstadkomma automatiskt e-post dina kunder. Det snabbaste sättet är att använda ett e-post-API från tredje part tillsammans med ett hjälpbibliotek som [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs). Om du redan har tillgång till en SMTP-server kan du använda [nodemailer](https://www.npmjs.com/package/nodemailer) för att skicka e-postmeddelanden via SMTP.

## Nästa steg

I den här praktiska självstudiekursen har du skapat ett enkelt program för att hjälpa Danielle att redovisa med [fakturering](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html). Med hjälp av PDF Services API och Document Generation SDK har du fyllt i en Microsoft Word-mall med kundorderinformation från ett JSON-dokument, och skapat en PDF-faktura. Du kan sedan lösenordsskydda varje dokument med lösenordsskyddstjänster genom att [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html).

Eftersom Danielle kan generera fakturor automatiskt och inte behöver oroa sig för kunder som redigerar sina fakturor, behöver hon inte anlita en assistent för att hjälpa till med allt manuellt arbete. Hon kan använda sin extra tid till att hitta kostnadsbesparingar i leverantörsreskontrafilerna.

Nu när du har sett hur enkelt det är kan du utöka den här appen med andra Adobe-verktyg för att bädda in fakturor på din webbplats. Detta gör till exempel att kunderna kan se sina fakturor eller saldon när som helst. [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) är gratis att använda. Du kan till och med gå vidare till personalavdelningen eller försäljningsavdelningen, hjälpa till att automatisera deras avtal och samla in elektroniska signaturer.

För att utforska alla möjligheter och börja bygga en egen praktisk app skapar du en kostnadsfri [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) konto för att komma igång idag. Testa gratis i sex månader sedan [betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)
endast $0,05 per dokumenttransaktion när företaget skalas.
