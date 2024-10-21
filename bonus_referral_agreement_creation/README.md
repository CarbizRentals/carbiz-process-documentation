# Bonus Referral Agreement Creation

## Description

This markdown file documents the current process for referral agreements and discusses the previous process that we have stopped using. At Carbiz, there are three types of Referral Payment. The most common one is the Referral Payment that is given out to a repairer or a tow truck company that referred us a job during a specific time frame. This Referral Payment is created based of a Referral Agreement object, attached to the referrer's account. As such, the documentation outlines the steps to create the Referral Agreements for each referrer.  

## Notes

- A Referal Payment will only be created for a referrer if the Referral Agreement of the referrer is 'Active' . Accident Automation flow is the flow that creates Referral Payments.
- Initially, there was a flow called Campaign Automation that was supposed to bulk create Referral Agreements when a Campaign is Active. However, salesforce flows cannot create thousands of records at a time. Hence, the flow was scrapped.
- Since, the flow wasn't working. We had to generate these Referral Agreements manually by writing up a custom Apex Script in Salesforce Developer console. This method was also inefficient as we can only create 20 records (due to flow triggers) at a time whilst we have to create around 7000 records. Believe me (Min), you do not want to click on the debug button 7000/20 = 250 times.
- As such, we decided to bulk create these Referral Agreements using Salesforce's Data Import Wizard. The Data Import Wizard allows us to bulk create thousands of Referral Agreement for our referrers at a time without triggering the flows.
- There are around 7000 referrers (repairers and tow truck companies) in Carbiz's salesforce at the time of writing this documentation. When we started bulk creating the referral agreements, we created them for all our referrers (based of this report). However, upon further analysis, out of all the referrers, only around 1000 (based of this report) of them has ever referred to us. Thereforce, we have decided to only create the Referral Agreements for the referrers that has only referred to us.
   
- Related repositories:
  - Referral Agreement Activation
  - Referral Agreement Deactivation

## Creation Steps

### 1. Export this [Report](https://carbiz.lightning.force.com/lightning/r/Report/00OOd000004hX3GMAU/view?queryScope=userFolders)

<p align="center">
  <img src="assets\ReportExport.png" alt="Export the Report">
</p>

### 2. Open the exported file in excel and copy the FullID column

<p align="center">
  <img src="assets\FullIDExcel.png" alt="Copy Full ID Column">
</p>

### 3. Format and prepare the <a href="assets\template.xlsx" download="template.xlsx">template sheet</a> with the data and save it in csv format

Please make sure that the following fields are correctly formatted as per the description:
- Agreement Status - Draft (By default it must be set to ```Draft```. Please refrain from setting it to ```Active``` unless specifically instructed to do so.)
- Start Date - The date the referral agreement must be activated 
- End Date - The date the referral agreement must be deactivated (For e.g: if we want to stop giving out referral payment until the 1st of April. It must be set to 2nd of April.)
- Record Type - Must always be set to ```Bonus```
- Description - Must always in Upper Cases

<p align="center">
  <img src="assets\Prepare.png" alt= "Data Preparation">
</p>


### 4. Go to the Data Import Wizard

<p align="center">
  <img src="assets\DataImportWizard.png" alt = "Data Import Wizard">
</p>


### 5. Click on the Launch Wizard Button

<p align="center">
  <img src="assets\LaunchWizard.png" alt="Launch Wizard">
</p>

### 6. Click on the Custom Objects Tab and look for Referral Agreements

<p align="center">
  <img src="assets\ChooseData.png" alt="Choose Data to Import">
</p>

### 7. Click on the "Add new records" option and change the Record type to "Bonus"

<p align="center">
  <img src="assets\Config.png" alt="Data Wizard Configuration">
</p>

### 8. Upload the CSV and Click "Next"

<p align="center">
  <img src="assets\CSVUpload.png" alt="CSV Upload">
</p>

### 9. Map the fields


<p align="center">
  <img src="assets\Mapped.png" alt="Data Mapping">
</p>

### 10. Monitor the job


<p align="center">
  <img src="assets\BulkJob.png" alt="Bulk Job Monitoring">
</p>