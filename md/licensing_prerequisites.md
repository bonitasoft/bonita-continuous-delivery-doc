# Licensing Prerequisites

In order that your deployment retrieves a license for Bonita automatically, you need to set the following variables in your scenario:

|Name|Description|Sample|
|-|-|-|
|lic_ws_login|Login to Bonitasoft license webservice.|acme|
|lic_ws_password|Password to Bonitasoft license webservice.|Secr3t|
|lic_sub_login|Subscription Login provided by Bonitasoft. It has an email address format.|john.doe@acme.com|
|lic_sub_password|Subscription Password provided by Bonitasoft.|SomePassword|
|lic_sub_id|Subscription ID provided by Bonitasoft. It's a string of 15 characters.|a0bA0000001B2Cd|
|lic_type|Type of license in [production\|development\|qualification\|trial].|trial|
|lic_name|Full name of the licensee. This field can only contain alphanumeric characters, dashes, underscores and spaces.|John Doe|
|lic_company|Company name.|ACME Inc|
|lic_email|Email address where the license is to send.|john.doe@acme.com|
