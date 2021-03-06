---
layout: post
title: Learnings from Sharepoint Integration with Angular
date: 2020-12-06 00:00:00 +0300
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: sharepoint-big.png # Add image post (optional)
tags: [Sharepoint, Javascript, MSAL] # add tag
---

## Requirement:
While working on Nuage WhiteLabelStudio there was a requirement to store documents in an organised fashion. Using Work Org approved methods, we decided to Sharepoint should be the ideal location for these files. But it we faced numerous errors connecting Frontend Angular SPA with Work Org Sharepoint site. This wiki documenatation captures all the errors and how we resolved each of the error to successfully integrate Work Org Sharepoint with Angular SPA.

## Challenges & Solution:
We are using official MSAL library by Microsoft for Angular interactions

1. After creating an app in https://portal.azure.com/ and configuring our clientid details in Angular code, we faced the below error.
Since, we are accessing the MS Graph API with common tenant we faced this error. We need to provide our Work Org tenant ID to be able to get past this error.


AADSTS50194: Application '75229b78-044c-415d-9a83-15b9bb93e5af'(Nuage-WhiteLabelStudio) is not configured as a multi-tenant application. Usage of the /common endpoint is not supported for such applications created after '10/15/2018'. Use a tenant-specific endpoint or configure the application to be multi-tenant.

2. We got below error as the reply to URL configured in Azure portal differs from the actual redirect URI of the application.

AADSTS50011: The reply URL specified in the request does not match the reply URLs configured for the application: '75229b78-044c-415d-9a83-15b9bb93e5af'.

3. This is the most common error and the error description is a bit off. Most of the times, this happens because sharepoint needs additional approval for the first time whereby you provide consent to WhiteLabelStudio to act on our behalf. We were needed to change the code to open a popup window.

AADSTS65001: The user or administrator has not consented to use the application with ID '75229b78-044c-415d-9a83-15b9bb93e5af' named 'Nuage-WhiteLabelStudio'. Send an interactive authorization request for this user and resource.

4. This is a configuration issue where we only need to add API permissions related to Sharepoint. While following MSAL official documentation, they have added this additional API permission to show how to add custom API permissions. Once you correct it, you will get past this error.

(\"api://a88bb933-319c-41b5-9f04-eff36d985612\") that your organization \"5d471751-9675-428d-917b-70f44f9630b0\" has not subscribed to or enabled. Contact your IT Admin to review the configuration of your service subscriptions.


5. As described you will face this error when request timesout.

ClientAuthError: Token renewal operation failed due to timeout.
Error monitorWindowForHash unable to find hash in url, timing out


6. We tried to access/create a folder in our closed team sharepoint group site.

{"error":{"code":"-2147024891, System.UnauthorizedAccessException","message":{"lang":"en-US","value":"Access denied. You do not have permission to perform this action or access this resource."}}}

7. We need to provide MFA authentication for the first time when we grant access to WhiteLabelStudio app.

{"error_description":"Invalid JWT token. No certificate thumbprint specified in token header."}
AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '00000003-0000-0000-c000-000000000000'.
Error when acquiring token for scopes: AllSites.Write InteractionRequiredAuthError: AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '00000003-0000-0000-c000-000000000000'.
AADSTS650053: The application 'WhiteLabelStudio' asked for scope 'allsites.write' that doesn't exist on the resource '00000003-0000-0000-c000-000000000000'. Contact the app vendor.

8. This is the crucial error that we recevied as Work Org had restricted its sharepoint API access to Work Org Intune registered devices. This means sharepoint API can be accessed only from Work Org devices. So, we moved our development & testing from Personal device to Work Org VDI.

InteractionRequiredAuthError: AADSTS53000: Device is not in required device state: compliant. Conditional Access policy requires a compliant device, and the device is not compliant. The user must enroll their device with an approved MDM provider like Intune.


9. This error is thrown by Sharepoint API when we pass folder names with leading or trailing spaces. This is in compliance with Microsoft Naming standards.

{
"error": {
"code": "invalidRequest",
"message": "The provided name cannot contain leading, or trailing, spaces.",
"innerError": {
"date": "2020-09-08T12:12:53",
"request-id": "41a6868f-ddc2-4691-85fc-57125def416f"
}
}
}

10. This is an extenstion of above error when few characters aren't allowed in folder names as per Work Org standards.
{
"error": {
"code": "invalidRequest",
"message": "The provided name cannot contain any illegal characters.",
"innerError": {
"date": "2020-09-08T14:51:29",
"request-id": "b9b509cc-c2e7-45bc-9b40-1d38eca77fe7"
}
}
}


## Takeaways

Having gone through this experience of Sharepoint integration, it is now easy to integrate WhiteLabelStudio with other MS applications like Outlook calendar & Teams and come up with new usecases in future. For instance, it would be nice to have Calendar details of BizDev team so that they can it to describe their customer engagements. It will be easier then to see who added and when. This gives BizDev team the necessary context for next engagement with the particular customer.
