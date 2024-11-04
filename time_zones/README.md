# Time Zones

## Description

Currently, Carbiz provides it's service across all Australia and has branches or satellite branches throughout all the States. Customers from different time zones contact Carbiz's customer service on a daily basis and due to how Salesforce handles timezones, there are mismatches when a customer service officer makes a booking for a customer that is in a different time zone.

## Notes
- In Salesforce, the timezone of the date & time fields are based on the user's salesforce account setting.
<p align="center">
  <img src="assets\usertimezone.png" alt="User Time Zone Settings">
</p>
- Operations Officers, who deliver the car to customers does not have Salesforce Licenses. They have access to a portal called [Zibra](https://zibra.carbiz.com.au/), that allows them to make changes to some of the fields in the bookings.

## Example Scenairo

#### Scenario One

Customer from Gold Coast wants the car at 9:00 AM QLD time. Carbiz customer service officer, in Sydney, books the booking at 9:00 AM. In Salesforce, the timezone of the date & time fields are based on the user's salesforce account setting. As such, when the Carbiz customer service officer booked the booking at 9:00 AM, it was actually 9:00 AM Sydney Time (which is 8:00 AM QLD time). When an Carbiz Operations Officer (the person who delivers the car) in Gold Coast checks the booking time, he/she will see the booking time as 8:00 AM QLD time when the actual time that the client wanted was 9:00 AM QLD time.  

#### Scenario Two

Customer from Perth calls the Operations Officer, who is also in Perth, and wanted to change his/her Booking time from 10:30 AM to 12:30 PM Perth Time. When the Operations Officer logs in to [Zibra](https://zibra.carbiz.com.au/), he/she will see two date & time fields known as "Time" and "Local Time". 
<p align="center">
  <img src="assets\zibrafields.png" alt="Zibra Fields">
</p>
The "Time" field is editable shows the time in Sydney Time. The "Local Time" is not editable and shows the time based on the location of the user. So, now, when the operation officer wants to update the date/time, he/she will need to do a mental calculation (12:30 pm Perth Time is 2:00 pm Sydney Time) and update the "Time" field accordingly. 


## Current Working Implementation

Before this implementation, time zone has never been working properly. Employees from different branches has their own internal process on how to address date & time changes. However, this is not feasible because employees come and go and the internal processes are not carried over from one employee to another. The primary purpose of this implementation is to standarised Carbiz employees interact with date & time regardless of where they are located.

### Salesforce

Vehicles are delivered and picked up based on the time & date in the job object in Salesforce. In Salesforce, two most common ways to create a job is through "Create a New Job" option and "Book Replacement Vehicle" button in an Accident Record Page. Both of these are linked to their respective screen flows which allows the user to input job details including the date time. Before the current implementation, the Carbiz employees has no way to change the time zone of the date & time of the job. With the current implementation, the users now have the option to select the time zone of the date & time of the job.


<p align="center">
  <img src="assets\before.png" alt="before">
</p>

<p align="center">
  <img src="assets\after.png" alt="after">
</p>


#### How it works behind the scene:
1. Users create a new job or booking and entered the date & time.
2. The flows checked if the selected value in the "Time Zone" picklist is the same as User's Salesforce time zone.
3. If the timezones are the same, no changes are made to the date & time value
4. If the timezones are different, the value of the selected date & time and timezone are passed to an custom apex class called ```ConvertTimeZone``` for conversion.

#### What the custom apex class ```ConvertTimeZone``` is doing:
1. It receives the passed in date & time value and the timezone value.
2. It converts the date & time into 24 hour format first.
3. Following this, based on the user selected time zone and user's time zone, the offset is calucated.
4. The calculated offset is then applied to the date & time.
5. The converted date & time is then returned.

#### Example Conversion

Date & Time: 10:00 AM

Input Time Zone: Australia/Brisbane

User's Time Zone: Australia/Sydney

Output: 10:00 AM Australia/Brisbane (11:00 AM Australia/Sydney)

#### User's Perspective

1. Client calls Carbiz's CSE who is located in Sydeny
2. Client wants the vehicle at 10:00 AM Australia/Brisbane time
3. CSE put in the time 10:00 AM and select Australia/Brisbane in the Time Zone Fields
4. When the CSE submited the form, the job is created.
5. When a Carbiz Employee in the same time zone as the job views the job record, he/she will see the Due Date & Time as 10:00 AM
6. When a Carbiz Employee in a different time zone as the job views the job record, he/she will see the Due Date & Time in their time zone. A banner will also be dispalyed, warning the user that they are viewing a job record which is assigned to a different time zone.
   1. Sydney POV - 11:00 AM
   2. Melbourne POV - 11:00 AM
   3. Perth POV - 8:00 AM
   4. Darwin POV - 9:30 AM
   5. Adelaide POV - 10:30 AM
   6. Hobart POV - 11:00 AM
<p align="center">
  <img src="assets\differenttimezone.png" alt="Different Time Zones">
</p>

### Zibra


## Change logs

### Salesforce 
- Removed "Create Custom Booking" from the nav bar drop down.
- Added an Aura component called ```TimezoneBanner``` for displaying a banner when a user is interacting with a job located in the different time zone.
- Created a custom apex class called ```ConvertTimeZone``` for time conversion.
- Created a custom apex class called ```RetrieveJobBranch``` for retrieving the due date & time in the branch's timezone. Used in the banner.
- Added ```Update Due Date & Time``` action button in the job record page.
- Added ```Update Job Due Date & Time``` and ```Local Time Update Handler``` flows. The first flow is created so that user can edit date & time based on different time zones. The latter was created to handle zibra's local time update.
- Updated ```Job Automation V3```,```Create a New Job V4```  and ```Accident Screen Customer Car Booking V3``` flows.
- Created ```Due_Date_Time__c``` formula field and added it to the Job page layout. This field was added because formula field are not editable whereas date & time fields are.
- Removed ```Due_Date_SLA__c``` fields from Job page layouts. This was done so to remove inline editing of the due date and time.
- Created ```Booking_Start_Date_Time__c```,```Estimated_Booking_Return_Date_Time__c``` and ```Booking_End_Date_Time__c``` fields and added them to Booking page. These formula fields were added because formula fields are not editable where as date& Time fields are.
- Removed ```Estimated_Booking_Return_Date_and_Time__c```, ```Booking_Start__c``` and ```Booking_Finish_Return_Date_and_Time__c``` fields from Booking page layouts. This was done so to remove inline editing of the date & time fields.


### Zibra

## Known Issues & Limitations