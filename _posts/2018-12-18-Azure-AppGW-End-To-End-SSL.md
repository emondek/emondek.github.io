---
layout: post
title: End-to-End SSL with Azure Application Gateway
---

I created the following diagram to help explain the configuration and flow of end-to-end SSL with Azure Application Gateway.  For a more detailed description, please refer to the Azure documentation [Overview of end to end SSL with Application Gateway](https://docs.microsoft.com/en-us/azure/application-gateway/ssl-overview).

![Azure Application Gateway End-to-End SSL](/images/appgw-end-to-end-ssl.jpg)

Here's a brief description of each step:

1. The user browses to https://www.abc.com
2. The client machine performs a DNS lookup on www.abc.com
3. DNS returns the IP address 1.2.3.4
4. The browser connects to 1.2.3.4 which is the frontend IP address of the Application Gateway.  The Application Gateway is configured with an SSL Policy that requires a minimum version of TLS and a supported set of Cipher suites.
5. Application Gateway routes the request to the appropriate Listener
6. The Listener is configured with an SSL certificate for the custom doman (e.g. www.abc.com) and terminates the SSL connection
7. Application Gateway applies Rules to route the request to the appropriate Backend
8. The Backend is configured to use HTTPS to connect to the backend servers.  In addition, the Backend must contain the public key of the backend site certificate (e.g. backend.abc.com).  This public key is uploaded as a .cer file and stored in the backend authentication certificate list.  Application Gateway will only connect to backend sites for which it has the public key in its backend authentication certificate list.
9. Application Gateway sends the request
10. Application Gateway connects to the backend using HTTPS

Cheers!
