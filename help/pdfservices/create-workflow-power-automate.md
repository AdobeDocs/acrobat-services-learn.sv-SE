---
title: Skapa ditt första arbetsflöde i Microsoft Power Automate
description: Lär dig hur du använder Adobe PDF Services-anslutningen i Microsoft Power Automate
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-10379.jpg
kt: 10379
exl-id: 095b705f-c380-42cc-9329-44ef7de655ee
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1999'
ht-degree: 2%

---


# Skapa ditt första arbetsflöde i Microsoft Power Automate

Lär dig skapa ditt första flöde i [Microsoft Power Automate](https://flow.microsoft.com) med hjälp av [Adobe PDF-tjänster](https://us.flow.microsoft.com/sv-se/connectors/shared_adobepdftools/adobe-pdf-services/) anslutning.

I den här praktiska självstudiekursen lär du dig att:

* Konvertera Word-dokument till PDF
* Kombinera PDF-dokument till ett PDF
* Protect ett PDF-dokument med ett lösenord

## Förberedelse

### Det du behöver

* **Autentiseringsuppgifter för testversioner eller produktion av Adobe PDF-tjänster**
Läs mer om hur du hämtar och konfigurerar inloggningsuppgifter i Microsoft Power Automate [här](https://experienceleague.adobe.com/docs/document-services/tutorials/pdfservices/getting-credentials-power-automate.html).
* **Microsoft Power Automate med Premium-anslutningar**
Lär dig kontrollera licensieringsnivån för Power Automate [här](https://docs.microsoft.com/en-us/power-platform/admin/power-automate-licensing/types).
* **OneDrive**
OneDrive-lagringskopplingen används i den här självstudiekursen, men alla lagringskopplingar kan bytas ut.

### Exempelfiler

Det finns två [exempelfiler](assets/sample-assets.zip) som du behöver för att packa upp och överföra till OneDrive:

* WordDocument01.docx
* WordDocument02.docx

### Hämtar autentiseringsuppgifter

För att slutföra den här självstudiekursen behöver du dina inloggningsuppgifter som redan har konfigurerats i Microsoft Power Automate för Adobe PDF-tjänster. Om du inte har slutfört det här steget, se [instruktioner här](https://experienceleague.adobe.com/docs/document-services/tutorials/pdfservices/getting-credentials-power-automate.html).

## Del 1: Skapa nytt flöde och konvertera Word till PDF

### Skapa flödet

I den här delen skapar du ett nytt flöde i [Microsoft Power Automate](https://flow.microsoft.com) med ett direktflöde lägger du till parametrar, hämtar filer från OneDrive och konverterar dem till PDF.

1. Gå till [Microsoft Power Automate](https://flow.microsoft.com) och logga in med dina inloggningsuppgifter.
1. I sidopanelen väljer du **[!UICONTROL Skapa]**.

   ![Knappen Skapa](assets/createButtonPowerAutomate.png)

1. Välj **[!UICONTROL Direktflöde]**.
1. Ge ditt flöde ett namn.
1. Under *Välj hur det här flödet ska utlösas* väljer du **[!UICONTROL Utlösa ett flöde manuellt]**.
1. Välj **[!UICONTROL Skapa]**.

### Hämta filinnehåll

Hämta sedan exempelfilernas filinnehåll.

>[!PREREQUISITES]
>
>Om du inte har överfört [exempelfiler](assets/sample-assets.zip) till OneDrive, packa upp dem och ladda upp dem.


1. I [Power Automate](https://flow.microsoft.com)väljer du **[!UICONTROL + Nytt steg]**.
1. Sök efter *OneDrive* i sökfältet.
1. Välj antingen ditt jobbkonto eller personliga OneDrive-konto genom att välja **[!UICONTROL OneDrive för företag]** eller **[!UICONTROL OneDrive]**.
1. Sök efter *Hämta filinnehåll* i sökfältet.
1. I dialogrutan **[!UICONTROL Fil]** väljer du mappikonen för att gå till *WordDocument01.docx* i OneDrive.

   ![Åtgärden Hämta filinnehåll på OneDrive i Microsoft Power Automate](assets/getFileContentOneDrive.png)

### Konvertera fil till PDF

Nu när du har filinnehållet kan du konvertera dokumentet till PDF.

1. I [Power Automate](https://flow.microsoft.com)väljer du **[!UICONTROL + Nytt steg]**.
1. Sök efter *Adobe PDF-tjänster* i sökfältet.
1. Välj **[!UICONTROL Adobe PDF-tjänster]**.
1. Sök efter *Konvertera Word till PDF* i sökfältet.
1. I **[!UICONTROL Filnamn]**, namnge filen som du önskar men den måste sluta med *.docx*. Det här tillägget krävs för att konvertera dokument från Word till PDF.
1. Placera markören i **[!UICONTROL Filinnehåll]** fält.
1. Med hjälp av **[!UICONTROL Dynamiskt innehåll]** panel väljer du **[!UICONTROL Filinnehåll]**.

   ![Åtgärden Konvertera Word till PDF i Microsoft Power Automate](assets/convertWordToPDFActionPowerAutomate.png)

### Spara filen på OneDrive

Spara filen i OneDrive när dokumentet har skapats.

1. I [Microsoft Power Automate](https://flow.microsoft.com)väljer du **[!UICONTROL + Nytt steg]**.
1. Sök efter *OneDrive* i sökfältet.
1. Välj antingen ditt jobbkonto eller personliga OneDrive-konto genom att välja **[!UICONTROL OneDrive för företag]** eller **[!UICONTROL OneDrive]**.
1. Sök efter *Hämta filinnehåll* i sökfältet.
1. Sök efter *Skapa fil* i sökfältet.
1. Välj **[!UICONTROL Skapa fil]**.
1. I dialogrutan **[!UICONTROL Mappsökväg]** markerar du mappikonen och anger var filen ska sparas i OneDrive.
1. I **[!UICONTROL Filnamn]**, namnge filen som du önskar men den måste sluta med *.docx*. Det här tillägget krävs för att konvertera dokument från Word till PDF.
1. I dialogrutan **[!UICONTROL Filinnehåll]** fält, använda **[!UICONTROL Dynamiskt innehåll]** för att infoga variabeln Innehåll i PDF-fil.

### Testa flöde

1. I det övre vänstra hörnet väljer du **[!UICONTROL Namnlös]** för att byta namn på flödet.
1. Välj **[!UICONTROL Spara]**.
1. Välj **[!UICONTROL Test]**.
1. Välj **[!UICONTROL Manuellt]** och därefter **[!UICONTROL Spara och testa]**.
1. Välj **[!UICONTROL Fortsätt]**.
1. Välj **[!UICONTROL Kör flöde]**.

I OneDrive-mappen bör du nu se det konverterade PDF.

![Markerade konverterade PDF-dokument i OneDrive](assets/selectedGeneratedFileInOneDrive.png)

## Del 2: Generera ett dynamiskt dokument från en mall

Denna nästa del bygger på del 1 och använder *Generera dokument från Word* för att dynamiskt sammanfoga data i dokumentet.

### Granska dokumentmallen

Öppna *WordDocument02_.docx* från dina exempelfiler i OneDrive. Word-dokumentet innehåller flera olika texttaggar som representerar platser där data fylls i dokumentet.

### Lägg till parametrar som ska utlösas

Om du vill att dynamiska data ska överföras till dokumentet måste du skapa några parametrar för utlösaren så att du kan fråga efter värden.

1. När du redigerar flödet väljer du **[!UICONTROL Utlösa ett flöde manuellt]** för att expandera åtgärden.
1. Välj **[!UICONTROL Lägg till indata]**.
1. Välj **[!UICONTROL Text]**.
1. Namnge fältet *Förnamn*.

Upprepa steg 2-4 för att lägga till följande fält:

* Efternamn
* Lön

![Utlösare i Power Automate med parameterfält](assets/triggerParametersInPowerAutomate.png)

### Hämta filinnehåll för en mall

Om du vill generera ett dokument måste du först hämta Word-mallens filinnehåll.

1. I Power Automate väljer du + **[!UICONTROL Nytt steg]**.
1. Sök efter *OneDrive* i sökfältet.
1. Välj antingen ditt jobbkonto eller personliga OneDrive-konto genom att välja **[!UICONTROL OneDrive för företag]** eller **[!UICONTROL OneDrive]**.
1. Sök efter *Hämta filinnehåll* i sökfältet.
1. I dialogrutan **[!UICONTROL Fil]** väljer du mappikonen för att gå till *WordDocument02.docx* i OneDrive.

![Hämta en åtgärd för filinnehåll från OneDrive i Microsoft Power Automate](assets/getFileContentAction02.png)

### Generera dokument från mall

1. I Power Automate väljer du **[!UICONTROL + Nytt steg]**.
1. Sök efter *Adobe PDF-tjänster* i sökfältet.
1. Välj **[!UICONTROL Adobe PDF-tjänster]**.
1. Välj **[!UICONTROL Generera dokument från Word-mall]** åtgärd .
1. I dialogrutan **[!UICONTROL Mallfilnamn]** , namnge filen som du önskar men den måste sluta med *.docx*.

#### Sammanfoga data

Med hjälp av *Generera dokument från Word-mall* kan du sammanfoga data i dokumentet från någon av de olika variablerna i flödet med hjälp av Dynamiskt innehåll.

Kopiera JSON-data nedan till **Sammanfoga data** fält:

```
{
    "FirstName": "",
    "LastName": "",
    "Salary": ""
}
```

1. Placera markören i fältet mellan de två citattecknen för *FirstName* värde.
1. Med hjälp av **[!UICONTROL Dynamiskt innehåll]** panelen infogar du *Förnamn* värde från utlösa en flödesåtgärd manuellt.

   ![Generera dokument med datataggar i JSON](assets/generateDocumentJSONAction.png)

1. Upprepa steg 7-8 för **[!UICONTROL LastName]** och **[!UICONTROL Lön]** fält.
1. I dialogrutan **[!UICONTROL Mallfilsinnehåll]** fältet använder du **[!UICONTROL Dynamiskt innehåll]** panel för att infoga **[!UICONTROL Filinnehåll]** värde från *Hämta filinnehåll* steg.

![Generera ett dokument från Word-mallåtgärd i Power Automate med alla värden slutförda](assets/generateDocumentJSONActionCompleted.png)

>[!TIP]
>
>Den *Generera dokument från Word-mall* åtgärden använder API:et för att skapa Adobe-dokument. Här är några resurser om du vill veta mer om hur du skapar mallar:
>
>* [Läs mer om Adobe dokumentgenerering](https://developer.adobe.com/document-services/apis/doc-generation/)
>* [Adobe Document Generation Tagger for Microsoft Word](https://appsource.microsoft.com/en-US/product/office/WA200002654)
>* [API-dokumentation för dokumentgenerering i Adobe](https://developer.adobe.com/document-services/docs/overview/document-generation-api/)


### Spara filen på OneDrive

När dokumentet har skapats kan du spara filen i OneDrive igen.

1. I Power Automate väljer du **+ [!UICONTROL Nytt steg]**.
1. Sök efter *OneDrive* i sökfältet.
1. Välj antingen ditt jobbkonto eller personliga OneDrive-konto genom att välja **[!UICONTROL OneDrive för företag]** eller **[!UICONTROL OneDrive]**.
1. Sök efter *Skapa fil* i sökfältet.
1. Välj **[!UICONTROL Skapa fil]**.
1. I dialogrutan **[!UICONTROL Mappsökväg]** markerar du mappikonen och anger var filen ska sparas i OneDrive.
1. I dialogrutan **[!UICONTROL Filnamn]** anger du filens namn. Eftersom utdata är PDF måste filnamnet sluta med filnamnstillägget .pdf.
1. Använd kommandot **[!UICONTROL Dynamiskt innehåll]** för att infoga variabeln PDF File Content i **[!UICONTROL Filinnehåll]** fält.

### Testa flöde

![Skärmen Run flow i Microsoft Power Automate där du uppmanas att ange indata](assets/runFlowParameters.png)

1. Välj **[!UICONTROL Spara]**.
1. Välj **[!UICONTROL Test]**.
1. Välj **[!UICONTROL Manuellt]** och därefter **[!UICONTROL Spara och testa]**.
1. Välj **[!UICONTROL Fortsätt]**.
1. Ange värden för *Förnamn*, *Efternamn* och *Lön*.
1. Välj **[!UICONTROL Kör flöde]**.

I OneDrive-mappen visas nu en PDF som genereras från Word-dokumentet. När du öppnar PDF-dokumentet i OneDrive ser du att data har sammanfogats med texttaggsplatserna.


## Del 3: Kombinera PDF till en

När du nu har skapat och konverterat ett Word-dokument till en PDF, är nästa steg att kombinera flera PDF-dokument.

>[!NOTE]
>
>I föregående åtgärder sparade du en kopia av dokumentet som en fil i OneDrive. För att använda verktyg som Sammanfoga PDF behöver du inte spara filen på OneDrive. I stället kan du skicka utdata direkt från en åtgärd till nästa, vilket är bättre än att spara på OneDrive efter varje åtgärd. Men i demonstrationssyfte sparar du filerna på OneDrive.

### Lägg till sammanfogning, PDF, steg

1. När du redigerar flödet väljer du **[!UICONTROL + Nästa steg]** om du vill lägga till en åtgärd i slutet av flödet.
1. Sök efter *Adobe PDF-tjänster* i sökfältet.
1. Välj **[!UICONTROL Adobe PDF-tjänster]**.
1. Välj **[!UICONTROL Sammanfoga PDF]** åtgärd.
1. I dialogrutan **[!UICONTROL Sammanfoga PDF-filnamn]** skriver du in önskat filnamn (d.v.s.*CombinedDocument.pdf*).
1. I dialogrutan **[!UICONTROL Filinnehåll -1]** fältet använder du **[!UICONTROL Dynamiskt innehåll]** panel för att infoga *PDF-filinnehåll* värde från **[!UICONTROL Konvertera Word till PDF]** steg.
1. Om du vill lägga till nästa dokument väljer du **+ [!UICONTROL lägg till nytt objekt]**.
1. I dialogrutan **[!UICONTROL Filinnehåll - 2]** fältet använder du **[!UICONTROL Dynamiskt innehåll]** panel för att infoga **[!UICONTROL Innehåll i utdatafil]** värde från *Generera dokument från Word-mall* steg.

![Åtgärden Sammanfoga PDF i Microsoft Power Automate](assets/mergePDFAction.png)

### Spara sammanfogad PDF till OneDrive

När dokumentet har kombinerats kan du spara dokumentet i OneDrive igen.

1. I Power Automate väljer du **+ [!UICONTROL Nytt steg]**.
1. Sök efter *OneDrive* i sökfältet.
1. Välj antingen ditt jobbkonto eller personliga OneDrive-konto genom att välja **[!UICONTROL OneDrive för företag]** eller **[!UICONTROL OneDrive]**.
1. Sök efter *Skapa fil* i sökfältet.
1. Välj **[!UICONTROL Skapa fil]**.
1. I dialogrutan **[!UICONTROL Mappsökväg]** markerar du mappikonen och anger var filen ska sparas i OneDrive.
1. I dialogrutan **[!UICONTROL Filnamn]** anger du filens namn. Eftersom utdata är PDF måste filnamnet sluta med .pdf.
1. I dialogrutan **[!UICONTROL Filinnehåll]** fält, använda **[!UICONTROL Dynamiskt innehåll]** panel för att infoga *PDF-filinnehåll* värde från **[!UICONTROL Sammanfoga PDF]** steg.

   ![Flöde i Microsoft Power Automate - översikt](assets/flowOverviewSavedMergedDocument.png)

### Testa flöde

1. Välj **[!UICONTROL Spara]**.
1. Välj **[!UICONTROL Test]**.
1. Välj **[!UICONTROL Manuellt]** och därefter **[!UICONTROL Spara och testa]**.
1. Välj **[!UICONTROL Fortsätt]**.
1. Ange värden för *Förnamn*, *Efternamn* och *Lön*.
1. Välj **[!UICONTROL Kör flöde]**.

I OneDrive-mappen visas det kombinerade PDF med sidor från det första och andra dokumentet.

## Del 4: Protect PDF-dokument

När du har skapat dokumentet kan du skydda det från redigering genom att ta med ett extra steg innan du sparar det på OneDrive.

### Skydda en PDF-fil

1. När du redigerar ditt flöde i Power Automate väljer du **+** mellan **[!UICONTROL Sammanfoga PDF]** och **[!UICONTROL Skapa fil 3]** åtgärd.

   ![Plus-symbol mellan två åtgärder för att lägga till en ny åtgärd](assets/addActionToProtect.png)

1. Välj **[!UICONTROL Lägg till en åtgärd]**.
1. Sök efter *Adobe PDF-tjänster* i sökfältet.
1. Välj **[!UICONTROL Adobe PDF-tjänster]**.
1. Välj **[!UICONTROL Protect PDF från visning]** åtgärd.
1. I dialogrutan **[!UICONTROL Filnamn]** anger du önskat namn, så länge det slutar med filnamnstillägget .pdf.
1. Ange **[!UICONTROL Lösenord]** till det angivna lösenordet för att öppna dokumentet.
1. I dialogrutan **[!UICONTROL Filinnehåll]** fältet använder du **[!UICONTROL Dynamiskt innehåll]** panel för att infoga *PDF-filinnehåll* värde från **[!UICONTROL Sammanfoga PDF]** steg.

### Uppdatera spara på OneDrive

När dokumentet är skyddat kan du spara tillbaka filen i OneDrive. I det här exemplet uppdaterar du de befintliga **Skapa fil 3** med en ny *Filinnehåll* värde.

1. Markera markören på menyn **[!UICONTROL Filinnehåll]** i **[!UICONTROL Skapa fil 3]** åtgärd.
1. Använd kommandot **[!UICONTROL Dynamiskt innehåll]** panel för att infoga *PDF-filinnehåll* värde från **Protect PDF från visning** steg.

### Testa flöde

1. Välj **[!UICONTROL Spara]**.
1. Välj **[!UICONTROL Test]**.
1. Välj **[!UICONTROL Manuellt]** och därefter **[!UICONTROL Spara och testa]**.
1. Välj **[!UICONTROL Fortsätt]**.
1. Ange värden för *Förnamn*, *Efternamn* och *Lön*.
1. Välj **[!UICONTROL Kör flöde]**.

I OneDrive-mappen visas det kombinerade PDF som nu uppmanar dig att ange ett lösenord för att visa dokumentet.

## Nästa steg

I den här självstudiekursen konverterade du ett Word-dokument till ett PDF, genererade ett dokument baserat på data, sammanfogade dokument och lösenordsskyddade. Om du vill veta mer kan du utforska några av de andra åtgärderna som finns i Adobe PDF Services-anslutningen i Microsoft Power Automate:

* Visa de förskapade mallarna i Microsoft Power Automate.
* Lär dig av [artiklar](https://medium.com/adobetech/tagged/microsoft-power-automate) på Adobe Tech Blog.
* Granska [dokumentation](https://developer.adobe.com/document-services/docs/overview/document-generation-api/) för Adobe Document Generation API.
