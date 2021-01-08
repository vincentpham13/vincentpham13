---
title: "Working With Pdf on Server Side Using Golang and Chromedp"
date: "2019-07-20T00:24:45+07:00"
author: "Nhat Pham"
description : ""
draft: false
categories : ["tech"]
type : "post"
featured : "post1.png"
featuredalt : "post 1"
featuredpath : "date"
---

<!-- ### Five steps to design and write an effective backend system for PDF processing. -->
Nowaday, Most of web or mobile applications use pdf as an exposing document type to their users.
For example, exporting a report to the client after an successful bank transaction, another use case is a health service application, the doctor can send the prescription under pdf file to the patient.

This post is tended to the backend developers who looking for an effective solution for PDF processing in their system.  
I prefer [Go lang](https://golang.org/) since it is an expressive, concise, clean, and efficient programming language.    

In this article, we'll go over files steps:

**1. Idealing and Preparation**  
**2. What Chromedb is**  
**3. Construct data modeling**  
**4. Initialize a simple project**  
**3. To be decided..**



### Idealizing and Preparation.
I've recently been working an health service application for French. Obviously, I won't introduce entire features of that system here since they're not in the main point of this article, but there's one thing i wanna share with you guys is its pdf generator in backend, it's one of important parts of prescription creation.

In this post, we'll implement a prescription exporting process, it serves for the doctor who will prescribe and send prescriptions to the patients.  
### What Chromedb is.
Chromedp is a high level Chrome DevTools Protocol client that simplifies driving browsers for scraping, unit testing, or profiling web pages using the CDP, see more
### Construct data modeling.
Assuming we're having a lot of stuff that should be applied in precription file, it might include such as following information: doctor name, doctor email, hospital name, drug info, patient name, patient address,...  

These information should be placed in the right position in the pdf file, just like this:  

![prescription-demo](https://www.rbcinsurance.com/group-benefits/_assets-custom/images/prescription-drug-sample-receipt-en.jpg)

So arcording to the demo prescription, our prescription template should contain:  

- Hospital
  - Hospital Name  
  - Hospital Address  
  - Hospital Phone Number  
- Prescription
  - Prescription Date  
  - Prescription Order 
- Patient
  - Patient Name  
  - Patient Address  
  - Patient City  
  - Patient Phone Number  
- Doctor
  - Doctor Name 
- Drug 
  - Drug Name  
  - Drug Ammount  
  - Drug Days
  - Drug Refill
  - Drug Pricing
- Patient Pay  
- Pharmacist's Signature

Many PDF frameworks or libraries can support annotation that you can predefine an corrodinate then put your annotation on that position, but that way is not fit this use case, because there's many information needed to be displayed on the pdf file, furthermore, these info aren't static content. 

So we should define a template and apply all the static content first, then taking the advanced of the **screenshot** feature of chromedb to screenshot the template and save it into pdf file format. Let move to the next session :))
### Initialize a simple project.

Here is the project structure of our demo  

├── main.go  
├── pdf-template.html  
└── prescription-template.go  

Let start with the `prescription-template.go` first. As introduced before, the prescription template includes so many information, now we construct prescription template model based on those information.

{{< highlight golang >}}
package main

import (
  "time"
)

// Hospital Hospital in the prescription
type Hospital struct {
	Name    string `json:"name"`
	Address string `json:"address"`
	Phone   string `json:"phone"`
}

// Prescription Prescription in the prescription
type Prescription struct {
	Date     time.Time `json:"date"`
	OrderNom string    `json:"orderNom"`
}

// Patient Patient in the prescription
type Patient struct {
	Name    string `json:"name"`
	Address string `json:"address"`
	City    string `json:"city"`
	Phone   string `json:"phone"`
}

// Doctor Doctor in the prescription
type Doctor struct {
	Name string `json:"name"`
}

// Drug Drug in the prescription
type Drug struct {
	Name    string  `json:"name"`
	Ammount int     `json:"amount"`
	Days    int     `json:"days"`
	Refill  int     `json:"refill"`
	Price   float32 `json:"Price"`
}

// PrescriptionTemplate PrescriptionTemplate is used in pdf-template
type PrescriptionTemplate struct {
	Hospital           Hospital     `json:"hospital"`
	Prescription       Prescription `json:"prescription"`
	Patient            Patient      `json:"patient"`
	Doctor             Doctor       `json:"doctor"`
	Drug               Drug         `json:"drug"`
	Pay                float32      `json:"pay"`
	PharmacistSignature string       `json:"pharmacisSignature"`
}
{{< /highlight >}}

The next step, we prepare a html template that contain mapped values with your defined model using the built-in package [text/template](https://golang.org/pkg/text/template/) of Go.

{{< highlight html >}}
<html>
<body>
  <div id="main">
    <h1 class="hospital_name">{{ .Hospital.Name }}</h1>
    <h3 class="hospital_address">{{ .Hospital.Address }}</h3>
    <h3 class="hospital_phone">{{ .Hospital.Phone }}</h3>
    <p class="kind">OFFICAL PRESCRIPTION RECEIPT</p>
    <p class="prescription_date"><b>{{ .Prescription.Date }}</b></p>
    <div>Rx: {{ .Prescription.OrderNom }}</div>
    <p class="patient_name"><b>{{ .Patient.Name }}</b></p>
    <p class="patient_address">{{ .Patient.Address }}</p>
    <p class="patient_city">{{ .Patient.City }}</p>
    <p class="patient_phone">{{ .Patient.Phone }}</p>
    <p class="doctor_name">Dr: {{ .Doctor.Name }}</p>
    <p class="drug_name">{{ .Drug.Name }}</p>
    <div class="drug_info">
      <b class="drug_days">Days: {{ .Drug.days }}</b>
      <b class="drug_ref">Ref: {{ .Drug.Refill }}</b>
    </div>
    <div class="drug_pricing">
      <h3>Pricing GS/CA</h3>
      <p class="price">${{ .Drug.Price }}</p>
    </div>
    <div class="patient_pay">
      <h3>Patient pays: <b class="box">${{ .Pay }}</b></h3>
    </div>
    <div class="pharmacist_signature">
      <h3>Pharmacist's Signature</h3>
      <div class="line"></div>
      <img src="{{ .PharmacisSignature }}" alt="" class="signature">
    </div>
  </div>
</body>
</html>
{{< /highlight >}}

{{< highlight go >}}
func pdfScreenshot(url string, sel string, res *[]byte) chromedp.Tasks {
	return chromedp.Tasks{
		chromedp.Navigate(url),
		chromedp.WaitVisible(sel, chromedp.ByID),
		chromedp.ActionFunc(func(ctx context.Context) error {
			buf, err := page.PrintToPDF().WithPrintBackground(false).Do(ctx)
			if err != nil {
				return err
			}
			*res = buf
			return nil
		}),
	}
}

{{< /highlight >}}
Now we move to the most important part. 


![](https://i.imgur.com/T95aW5X.png)
### To be decided

The completed project here [https://github.com/minhnhatspk/chromedp-screenshot-pdf](https://github.com/minhnhatspk/chromedp-screenshot-pdf)