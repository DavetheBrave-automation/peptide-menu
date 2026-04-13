# Peptide Menu Website

A sleek, mobile-first peptide catalog and ordering system for Heather Necaise.

## Quick Deploy to Surge

```bash
cd peptide-site
surge . peptide-menu.surge.sh
```

## Features

- Dark, high-tech aesthetic (navy + cyan/teal accents)
- Mobile-first responsive design
- Product catalog with category filters
- Live shopping cart with $10 flat shipping
- BAC water free with first order logic
- Order form with confirmation modal
- Customer intake form for consultations
- FAQ accordion
- All compliant disclaimer language

## Google Sheets Setup (for form submissions)

### Step 1: Create Google Sheets

1. Go to Google Sheets and create two new spreadsheets:
   - **Peptide Orders** - for order submissions
   - **Customer Intake** - for intake form submissions

2. In **Peptide Orders**, add these column headers in row 1:
   ```
   Timestamp | Order Ref | Name | Email | Phone | Street | City | State | Zip | First Order | Notes | Items | Subtotal | Shipping | Total
   ```

3. In **Customer Intake**, add these column headers in row 1:
   ```
   Timestamp | Name | Email | Phone | Goals | Experience | Previous Peptides | Additional Info | Contact Method
   ```

### Step 2: Create Google Apps Script

1. Go to [script.google.com](https://script.google.com)
2. Create a new project
3. Paste this code:

```javascript
const ORDERS_SHEET_ID = 'YOUR_ORDERS_SHEET_ID';
const INTAKE_SHEET_ID = 'YOUR_INTAKE_SHEET_ID';
const NOTIFICATION_EMAIL = 'david@trustedservicessd.com';

function doPost(e) {
  try {
    const data = JSON.parse(e.postData.contents);
    
    if (data.type === 'order') {
      handleOrder(data);
    } else if (data.type === 'intake') {
      handleIntake(data);
    }
    
    return ContentService.createTextOutput(JSON.stringify({ success: true }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (error) {
    return ContentService.createTextOutput(JSON.stringify({ error: error.message }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

function handleOrder(data) {
  const sheet = SpreadsheetApp.openById(ORDERS_SHEET_ID).getActiveSheet();
  
  sheet.appendRow([
    data.timestamp,
    data.orderRef,
    data.name,
    data.email,
    data.phone,
    data.street,
    data.city,
    data.state,
    data.zip,
    data.firstOrder,
    data.notes,
    data.items,
    data.subtotal,
    data.shipping,
    data.total
  ]);
  
  // Send email notification
  const subject = `New Order: ${data.orderRef} - $${data.total}`;
  const body = `
New peptide order received!

Order Reference: ${data.orderRef}
Customer: ${data.name}
Email: ${data.email}
Phone: ${data.phone}

Shipping Address:
${data.street}
${data.city}, ${data.state} ${data.zip}

First Order: ${data.firstOrder}
Notes: ${data.notes || 'None'}

Items: ${data.items}

Subtotal: $${data.subtotal}
Shipping: $${data.shipping}
Total: $${data.total}
  `;
  
  MailApp.sendEmail(NOTIFICATION_EMAIL, subject, body);
}

function handleIntake(data) {
  const sheet = SpreadsheetApp.openById(INTAKE_SHEET_ID).getActiveSheet();
  
  sheet.appendRow([
    data.timestamp,
    data.name,
    data.email,
    data.phone,
    data.goals,
    data.experience,
    data.previousPeptides,
    data.additionalInfo,
    data.contactMethod
  ]);
  
  // Send email notification
  const subject = `New Intake Form: ${data.name}`;
  const body = `
New customer intake form received!

Name: ${data.name}
Email: ${data.email}
Phone: ${data.phone}
Preferred Contact: ${data.contactMethod}

Goals: ${data.goals}
Previous Experience: ${data.experience}
Previous Peptides: ${data.previousPeptides || 'None'}

Additional Info:
${data.additionalInfo || 'None'}
  `;
  
  MailApp.sendEmail(NOTIFICATION_EMAIL, subject, body);
}
```

4. Replace `YOUR_ORDERS_SHEET_ID` and `YOUR_INTAKE_SHEET_ID` with your actual Sheet IDs
   - Find the ID in the sheet URL: `https://docs.google.com/spreadsheets/d/THIS_IS_THE_ID/edit`

5. Deploy:
   - Click **Deploy** → **New deployment**
   - Select **Web app**
   - Set **Execute as**: Me
   - Set **Who has access**: Anyone
   - Click **Deploy**
   - Copy the Web app URL

### Step 3: Update the Website

In `index.html`, find these commented lines and uncomment/update them:

```javascript
// await fetch('YOUR_GOOGLE_SCRIPT_URL', {
//     method: 'POST',
//     body: JSON.stringify(orderData)
// });
```

Replace `YOUR_GOOGLE_SCRIPT_URL` with your deployed Apps Script URL.

## Contact

- Heather Necaise
- (619) 757-7344
- Venmo: @heather-Necaise
