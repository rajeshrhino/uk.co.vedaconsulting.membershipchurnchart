# uk.co.vedaconsulting.membershipchurnchart

## Introduction

This extension helps you to view the membership churn chart as well as membership summary bar chart for a set interval of years.

## How to Install

1. Download extension from [https://github.com/veda-consulting/uk.co.vedaconsulting.membershipchurnchart/releases/latest](https://github.com/veda-consulting/uk.co.vedaconsulting.membershipchurnchart/releases/latest "https://github.com/veda-consulting/uk.co.vedaconsulting.membershipchurnchart/releases/latest") .
2. Unzip / untar the package and place it in your configured extensions directory.
3. When you reload the Manage Extensions page the new “Membership Churn Chart” extension should be listed with an Install link.
4. Proceed with install.

## Settings

Configure the start year from which the membership churn chart data to be prepared. 

1. Navigate to Click Memberships >> Membership Churn Chart. Click 'Settings' button.
2. Set the start year from which the membership churn chart data to be prepared and save

## Usage

Navigate to Memberships >> Membership Churn Chart to view the churn chart and membership summary chart for the set interval.

## How it works

During installation the extension will create a new scheduled job 'Membership Churn Chart - Prepare Data' which prepares the churn chart data.
The extension will also create 2 database tables to prepare and consolidate churn chart data.

```sql
CREATE TABLE IF NOT EXISTS `membership_churn_table` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'Unique Id',
  `year` int(11) DEFAULT NULL,
  `month` int(11) DEFAULT NULL,
  `membership_id` int(11) DEFAULT NULL,
  `membership_type_id` int(11) DEFAULT NULL,
  `current` int(11) DEFAULT NULL,
  `joined` int(11) DEFAULT NULL,
  `resigned` int(11) DEFAULT NULL,
  `rejoined` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
);
CREATE TABLE IF NOT EXISTS `membership_churn_monthly_table` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'Unique Id',
  `month_year` VARCHAR(255) DEFAULT NULL,
  `year` int(11) DEFAULT NULL,
  `month` int(11) DEFAULT NULL,
  `membership_type_id` int(11) DEFAULT NULL,
  `current` int(11) DEFAULT NULL,
  `joined` int(11) DEFAULT NULL,
  `resigned` int(11) DEFAULT NULL,
  `rejoined` int(11) DEFAULT NULL,
  `brought_forward` int(11) DEFAULT NULL,
  `churn` double(10, 2) DEFAULT NULL,
  PRIMARY KEY (`id`)
);
```

* 'membership_churn_table' table is used to store the status (Joined/Current/Resigned/Rejoined) of each membership records for each month/year.
* 'membership_churn_monthly_table' table is used to consolidate the membership records grouped by membership type/year/month, which is used to render the chart.

### Statuses (For the month):
* Brought forward = (Joined + Rejoined + Current) - Resigned, from the previous month
* Joined = Member joined in that month
* Current = Membership is current in that month
* Resigned = Membership ended in that month
* Rejoined = Member rejoined in that month (The member has a expired membership record & a new membership record was created of the same membership type)
* Churn = (Joined + Rejoined - Resigned) / BroughtForward
