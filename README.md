# How to Bypass CAPTCHA With Puppeteer

To access protected websites, you must bypass CAPTCHA. Puppeteer, a Node.js library with a user-friendly API for managing Chrome/Chromium via the DevTools Protocol, can help. It can run in full-browser mode instead of headless mode.

Why isn’t Puppeteer enough? Automated access using Puppeteer often triggers CAPTCHA or blocks as websites detect the automation.

Let’s validate it using the following steps:

You must have Node.JS installed on your system. Create a new Node.JS project and install Puppeteer using the following `npm` command:

```npm i puppeteer```

2. Import the Puppeteer library in your Node.JS file.

```const puppeteer = require('puppeteer');```

3. Create a new browser instance in headless mode and a new page using the following code:

```
(async () => {
  // Create a browser instance
  const browserObj = await puppeteer.launch();

  // Create a new page
  const newpage = await browserObj.newPage();
```

4. Since we need to take the screenshot on the desktop device, we can set the viewport size using the following code:

```
  // Set the width and height of viewport
  await newpage.setViewport({ width: 1920, height: 1080 });
```

The setViewPort() method sets the size of the webpage. You can change it according to your device requirements. 

5. After that, navigate to a page URL (that you think is a CAPTCHA-protected page) and take a screenshot. For demonstration purposes, the code uses Oxylabs [scraping sandbox](https://sandbox.oxylabs.io/products). Remember to close the browser object at the end.

```
  const url = 'https://sandbox.oxylabs.io/products';

  //  Open the required URL in the newpage object
  await newpage.goto(url);
  await newpage.waitForNetworkIdle(); // Wait for network resources to fully load

  // Capture screenshot
  await newpage.screenshot({
    path: 'screenshot.png',
  });

  // Close the browser object
  await browserObj.close();
})();
```

This is what the complete code looks like:

```
const puppeteer = require('puppeteer');

(async () => {
  const browserObj = await puppeteer.launch();
  const newpage = await browserObj.newPage();
  await newpage.setViewport({ width: 1920, height: 1080 });

  const url = 'https://sandbox.oxylabs.io/products';

  await newpage.goto(url);
  await newpage.waitForNetworkIdle();
  await newpage.screenshot({
    path: 'screenshot.png',
  });

  await browserObj.close();
})();
```

## Using Puppeteer-stealth to bypass CAPTCHA

Here is the step-by-step procedure to implement this CAPTCHA bypass: 

1. To start, you need to install the `puppeteer-extra` and `puppeteer-extra-plugin-stealth` packages.

```
npm install puppeteer-extra-plugin-stealth puppeteer-extra
```

2. After that, import the following required libraries in your Node.JS file:

```
const puppeteerExtra = require('puppeteer-extra');
const Stealth = require('puppeteer-extra-plugin-stealth');

puppeteerExtra.use(Stealth());
```

3. The next step is to create the browser object in headless mode, navigate to the URL and take a screenshot.

```
(async () => {
    const browserObj = await puppeteerExtra.launch();
    const newpage = await browserObj.newPage();
  
    await newpage.setViewport({ width: 1920, height: 1080 });
  
    await newpage.setUserAgent(
      'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36'
    );
  
    await newpage.goto('https://sandbox.oxylabs.io/products');
    await newpage.waitForNetworkIdle(); // Wait for network resources to fully load
  
    await newpage.screenshot({ path: 'screenshot_stealth.png' });
  
    await browserObj.close();
})();
```

The `setUserAgent` method makes our requests imitate a real browser's User-Agent, making our automated headless browsers appear more like regular users. Setting one of the common User-Agent strings helps evade detection and bypass anti-bot mechanisms that analyze the User-Agent header.

Here is what our complete script looks like:

```
const puppeteerExtra = require('puppeteer-extra');
const Stealth = require('puppeteer-extra-plugin-stealth');

puppeteerExtra.use(Stealth());

(async () => {
    const browserObj = await puppeteerExtra.launch();
    const newpage = await browserObj.newPage();
  
    await newpage.setViewport({ width: 1920, height: 1080 });
  
    await newpage.setUserAgent(
      'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36'
    );
  
    await newpage.goto('https://sandbox.oxylabs.io/products');
    await newpage.waitForNetworkIdle(); // Wait for network resources to fully load
  
    await newpage.screenshot({ path: 'screenshot_stealth.png' });
  
    await browserObj.close();
})();
```

## Using Web Unblocker with Node.JS

Web Unblocker uses AI to help users prevent CAPTCHA and gain access to public data from websites with advanced anti-bots implemented. To begin, you can send a basic query without any special options – the Web Unblocker tool will select the fastest CAPTCHA proxy, add all necessary headers, and provide you with the response body. 

1. Install the node-fetch and HttpsProxyAgent using the following command:

```npm install node-fetch https-proxy-agent```

2. [Sign up to Oxylabs](https://dashboard.oxylabs.io/en/) and get your credentials for using the API. 

3. Before importing the libraries, open the package.json file and enter these lines `"type": "module"`, for example:

```
{
  "type": "module",
  "dependencies": {
    "https-proxy-agent": "^7.0.4",
    "node-fetch": "^3.3.2",
    "puppeteer": "^22.6.5",
    "puppeteer-extra": "^3.3.6",
    "puppeteer-extra-plugin-stealth": "^2.11.2"
  }
}
```

Since the newest version of `node-fetch` is an ESM-only module, you can’t import it using the `require()` function. Learn more about it [here](https://www.npmjs.com/package/node-fetch#installation).

Next, import the required modules in your JS file using the `import-from` syntax:

```
import fetch from 'node-fetch';
import HttpsProxyAgent from 'https-proxy-agent';
import fs from 'fs';
```

The `fs` library can help save the response in an HTML file. 

4. Provide your user credentials and set up a proxy using `HttpsProxyAgent`.

```
const username = '<Your-username>';
const password = '<Your-password>';

(async () => {
    const agent = new HttpsProxyAgent.HttpsProxyAgent(
        `http://${username}:${password}@unblock.oxylabs.io:60000`
    );
```

5. Next, set the URL and issue a fetch request.

```
    // Ignore the certificate
    process.env['NODE_TLS_REJECT_UNAUTHORIZED'] = 0;

    const response = await fetch('https://ip.oxylabs.io/', {
        method: 'get',
        agent: agent,
    });
```

The environment variable `NODE_TLS_REJECT_UNAUTHORIZED` is set to zero so that Node.JS doesn't verify the SSL/TLS certificates. This is a required setting if you’re using Oxylabs’ Web Unblocker.

6. In the end, you can convert the response into text and save it in an HTML file.

```
    const resp = await response.text();
    fs.writeFile('result.html', resp.toString(), (err) => {
        if (err) throw err;
        console.log('Result saved to result.html');
    });
})();
```

Here is the complete script:
```
import fetch from 'node-fetch';
import HttpsProxyAgent from 'https-proxy-agent';
import fs from 'fs';

const username = '<Your-username>';
const password = '<Your-password>';

(async () => {
    const agent = new HttpsProxyAgent.HttpsProxyAgent(
        `http://${username}:${password}@unblock.oxylabs.io:60000`
    );

    // Ignore the certificate
    process.env['NODE_TLS_REJECT_UNAUTHORIZED'] = 0;

    const response = await fetch('https://ip.oxylabs.io/', {
        method: 'get',
        agent: agent,
    });

    const resp = await response.text();
    fs.writeFile('result.html', resp.toString(), (err) => {
        if (err) throw err;
        console.log('Result saved to result.html');
    });
})();
```





