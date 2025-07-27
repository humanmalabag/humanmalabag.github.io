---
title: File Upload Attacks Saiyan Speedrun
date: 2025-07-28 00:14:00 +0800
description: HackTheBox File Upload Attacks exercises
categories: [Level-Up, Cybersecurity]
tags: [hack the box, walkthrough, file upload]     # TAG names should always be lowercase
author: humanmalabag
image:
    path: /assets/images/fileuploadattacks-ss.png
---

> Try to upload a PHP script that executes the (hostname) command on the back-end server, and submit the first word of it as the answer.
{: .prompt-info }

Create a script file to be uploaded in the Web Application. Upon browsing, the code will execute (The server and our code have the same language - had it happened that they were in different language, the web application will just display the text in the page).

![image.png](assets/images/fileuploadattacks1.png)

![image.png](assets/images/fileuploadattacks2.png)


> Try to exploit the upload feature to upload a web shell and get the content of /flag.txt.
{: .prompt-info }

Acquire a readily-made webshell (from phpbash > phpbash.php) then upload it into the application. We will then have an interactive shell in our browser.

![image.png](assets/images/fileuploadattacks3.png)

> Try to bypass the client-side file type validations in the above exercise, then upload a web shell to read /flag.txt (try both bypass methods for better practice).
{: .prompt-info }

First method is thru Burp proxy: Intercept the request during image upload. Modify the filename and the file content.

![image.png](assets/images/fileuploadattacks4.png)

Then browse the profile image URL with the corresponding payload.

![image.png](assets/images/fileuploadattacks5.png)

For the second method, we can modify the Front-end validation by deleting the function which checks the file extension. Then successfully upload the our shell.

![image.png](assets/images/fileuploadattacks6.png)

![image.png](assets/images/fileuploadattacks7.png)

> Try to find an extension that is not blacklisted and can execute PHP code on the web server, and use it to read "/flag.txt”.
{: .prompt-info }

Fuzz the file upload for not blacklisted extensions using Burp’s Intruder. Sort the response length to easily distinguish extensions.

![image.png](assets/images/fileuploadattacks8.png)

Test each extensions to see which one will work. Not all extensions will work with all web server configurations.

![image.png](assets/images/fileuploadattacks9.png)

> The above exercise employs a blacklist and a whitelist test to block unwanted extensions and only allow image extensions. Try to bypass both to upload a PHP script and execute code to read "/flag.txt”.
{: .prompt-info }

Let’s fuzz first the upload functionality to determine which extension is allowed.

![image.png](assets/images/fileuploadattacks10.png)

Then fuzz for the extension which is not blacklisted. See the difference in their error response.

![image.png](assets/images/fileuploadattacks11.png)

![image.png](assets/images/fileuploadattacks12.png)

Combine and play around with the two extensions to bypass both whitelisted and blacklisted extensions. The final working extension, for this case, will be as follows:

![image.png](assets/images/fileuploadattacks13.png)

> The above server employs Client-Side, Blacklist, Whitelist, Content-Type, and MIME-Type filters to ensure the uploaded file is an image. Try to combine all of the attacks you learned so far to bypass these filters and upload a PHP file and read the flag at "/flag.txt”
{: .prompt-info }

Upload our test image, .jpg with Image MIME type and GIF8 File Signature entered as plain text >> Success

![image.png](assets/images/fileuploadattacks14.png)

Inject payload after file signature >> Success but code will not execute

![image.png](assets/images/fileuploadattacks15.png)

![image.png](assets/images/fileuploadattacks16.png)

Change extension to .php >> Extension not allowed

Now, fuzz which extension is allowed or at least not blacklisted.

Some examples of not blacklisted extensions - Error message returns as “Only images are allowed” instead of “Extension no allowed”

![image.png](assets/images/fileuploadattacks17.png)

From the idea that .jpg is an accepted file extension despite malformed file content; phtm is not blacklisted, we can play around with the extensions first.

![image.png](assets/images/fileuploadattacks18.png)

![image.png](assets/images/fileuploadattacks19.png)

Notice how some of our payloads will not be executed. Despite a successful file upload, the server will not interpret it as a valid php code.

The final working extension will be as follows:

![image.png](assets/images/fileuploadattacks20.png)

> The exercise contains an upload functionality that should be secure against arbitrary file uploads. Try to exploit it using one of the attacks shown in this section to read "/flag.txt”.
{: .prompt-info }

![image.png](assets/images/fileuploadattacks21.png)

Try uploading an svg file to read file contents. After uploading, browse the home page and notice the result is included in the response. Modify the file into /flag.txt to retrieve the flag contents.

![image.png](assets/images/fileuploadattacks22.png)

>Try to read the source code of 'upload.php' to identify the uploads directory, and use its name as the answer. (write it exactly 
{: .prompt-info }

Repeat the process but this time use a different payload (PayloadAllTheThings > XXE Injection > Files > XXE PHP Wrapper) to read the contents of upload.php.

![image.png](assets/images/fileuploadattacks23.png)

The response will contain the base64 encoded source code. Decode it to find the uploads directory.

![image.png](assets/images/fileuploadattacks24.png)