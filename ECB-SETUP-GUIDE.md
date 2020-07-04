
#  Enterprise Chat Bridge (ECB) - <em>Setup Guide</em>

This document guides you through the process of setting ECB on your server, which is the first step in implementing ECB.

- [x]  **1. ECB:** Download, configure and start ECB 
- [ ]  **2. Beckend Server:** Implement your ECB backend server
- [ ]  **3. Web Client:** Embed ECB client in your web application

You need to familiarize yourself with ECB through the [introduction document](URLLL) first if you haven't already ;) 

## Checklist
- [ ]  [**1.1 Node.js:** Download & Setup Node.js on your server](#1-1)
- [ ]  [**1.2 ECB:** Download ECB binary (executable) to your server](#1-2)
- [ ]  [**1.3 Activation:** Create activation key & request serial key](#1-3)
- [ ]  [**1.4 Key Pair:** Create a new RS256 public/private key pair](#1-4)
- [ ]  [**1.5 Configure:** Configure ECB's settings (port, backend, etc...)](#1-5)
- [ ]  [**1.6 Start:** Start ECB and configure your OS to start it automatically](#1-6)


## 1.1 Node.js <a id="1-1"></a>
### Download & Setup Node.js on your server
ECB support Node.js 10 and above. So if you haven't installed Node.js yet visit https://nodejs.org/en/download/ to finish this step.

## 1.2 ECB <a id="1-2"></a>
### Download ECB binary (executable) to your server
The following binaries are available:
- Windows 10 - x64 ([Download](link...))
- Windows 10 - x32 ([Download](link...))

Create a folder and extract the archive file to it. We will refer to this folder as `<ECB folder>`.
  
## 1.3 Activation <a id="1-3"></a>
### Create activation key & request serial key
1. Open a CLI to the `<ECB folder>` folder, and run **ECB** 
2. Provide your legal organization name
3. The activation file `activation.key` will be generated, send it to connect@artofintel.com 
4. Our team will send you a `serial.key`, place it in `<ECB folder>` folder 
 


## 1.4 Key Pair <a id="1-4"></a>
### Create a new RS256 public/private key pair

You need to generate a new pair of RS256 keys.
- **Private Key (secret):** Stored and used by your backend server
- **Public Key:** Stored and used by both **ECB** and your web client

> Read more about [Understanding ECB security](ECB-SECURITY.md) 

#### Windows
1. Navigate to `<ECB folder>/openssl/` folder
2. Generate the **private key (secret)**: 
`openssl genrsa -out private.key 2048`
3. Generate the **public key**:
`openssl rsa -in private.key -pubout -out public.key`
4. Move `public.key` to `<ECB folder>`

>  **IMPORTANT:**
> Move `private.key` to a secure location, we will use it in step 2 [(Implement your ECB backend server)](link...)

## 1.5 Configure <a id="1-5"></a>
### Configure ECB's settings (port, backend, etc...)
Open a CLI to the `<ECB folder>` folder, run **ECB**, and configure the following:
 
#### 1. ECB Port Number
This is the TCP port you will expose on ECB's server, web clients will connect to it.
 
#### 2. Backend Url
This is your backend server's base url, **ECB** will communicate with your REST API resources exposed over this url.

#### 3. Block non-thread members requests
When a thread is retrieved from your server, your server lists all thread participants (members) inside the thread's token.

Then when a user tries to send a new message to an existing thread, or retrieve and existing **ECB** can block this request if this setting is set to **true** *(default)*. 

Alternatively, you may want to receive the request and handle it on your server. In this case, set this to **false**

> This is useful if you want a non-thread member to **join** an existing thread, such as in a customer support scenario.    	
 

## 1.6 Start <a id="1-6"></a>
### Start ECB and configure your OS to start it automatically

That's it, your **ECB** service is ready. 

To insure service availability, you need to configure **ECB's** host server to start the service automatically.  