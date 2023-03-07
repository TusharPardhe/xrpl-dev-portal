---
parent: build-apps.html
targets:
  - en
  - ja # TODO: translate this page
blurb: Build a graphical desktop wallet for the XRPL using Javascript.
---
# Build a Desktop Wallet in Javascript
<!-- STYLE_OVERRIDE: wallet -->

This tutorial demonstrates how to build a desktop wallet for the XRP Ledger using the Javascript programming language and various libraries. This application can be used as a starting point for building a more complete and powerful application, as a reference point for building comparable apps, or as a learning experience to better understand how to integrate XRP Ledger functionality into a larger project.

## Prerequisites

To complete this tutorial, you should meet the following guidelines:

1. You have [Node.js](https://nodejs.org/en/download/) v10.15.3 or higher installed.
2. You have [Yarn](https://yarnpkg.com/en/docs/install) (v1.17.3 or higher) or [NPM](https://www.npmjs.com/get-npm) (v6.4.1 or higher) installed.
3. You are somewhat familiar with coding with JavaScript and have completed the [Get Started Using JavaScript](get-started-using-python.html) tutorial.

## Source Code

You can find the complete source code for all of this tutorial's examples in the [code samples section of this website's repository]({{target.github_forkurl}}/tree/{{target.github_branch}}/content/_code-samples/build-a-wallet/js/).

## Goals

At the end of this tutorial, you should be able to build a simple XRP wallet displayed below.

![Home Page Screenshot](img/js-wallet-home.png)

This application can:

- Show updates to the XRP Ledger in real-time.
- View any XRP Ledger account's activity, including showing how much XRP was delivered by each transaction.
- Show how much XRP is set aside for the account's [reserve requirement](reserves.html). 
- Send [direct XRP payments](direct-xrp-payments.html), and provide feedback about the intended destination address, including:
    - Displaying your account's available balance
    - Verifying that the destination address is valid
    - Validating the account has enough XRP to send
    - Allowing you to specify a destination tag

## Steps

Before you begin, make sure you have the pre-requisites installed. Check your node version by running `node -v`. If you don't have node installed, you can download it [here](https://nodejs.org/en/download/).

### Step 1: Setting up the project

1. Navigate to the directory that you want to create the project in.
2. Run 
   ```sh
    yarn create vite-app <project-name>
    ```
    or
    ```sh
    npm init vite-app <project-name>
    ```
    to create a new project.

3. Navigate to your project directory and copy this [package.json](_code-samples/build-a-wallet/js/package.json) file to your project.
        - Alternatively you can also do `yarn add <package-name>` or `npm install <package-name>` for each individual package to add them to your `package.json` file.
  
4. Run `yarn` or `npm i` to install the packages.

5. Create a new file `.env` in the root directory of the project and add the following variables:

```bash
CLIENT="wss://s.altnet.rippletest.net/" // or any other server
EXPLORER_NETWORK="testnet" // or "mainnet"
SEED="sEd7B2Jm7RBhhh5i88SH38aZB8ot8Kq" // Replace with your seed
```
6. Change the seed to your own seed. You can get a testnet account from [here](https://xrpl.org/xrp-test-net-faucet.html).

### Step 2: Creating the Home Page (Displaying Account & Ledger Details)

In this step, we will create the home page that will display the account details and the ledger details.

![Home Page Screenshot](img/js-wallet-home.png)

1. Make a new folder named src in the root directory of the project.

2. Create new files named index.html, index.js and index.css. 

3. Copy the contents of the [index.html](_code-samples/build-a-wallet/js/index.html), [index.js](_code-samples/build-a-wallet/js/index.js) and [index.css](_code-samples/build-a-wallet/js/index.css) files to your files.

4. Create a new file in helpers folder named get-wallet-details.js and copy the code written below to the file.

{{ include_code("_code-samples/build-a-wallet/js/src/helpers/get-wallet-details.js", language="js") }}

In the function getWalletDetails, we are using the [account_info](https://xrpl.org/account_info.html) method to fetch the account details. We are also using the [server_info](https://xrpl.org/server_info.html) method to calculate the reserve base and increment. You can read more about the reserve requirement [here](https://xrpl.org/reserves.html). Follow the comments in the code to understand the logic.

5. Create another file in helpers folder named get-ledger-details.js which will be used to fetch the ledger details and copy the code written below to the file.

{{ include_code("_code-samples/build-a-wallet/js/src/helpers/get-ledger-details.js", language="js") }}

In the function getLedgerDetails, we are using the [ledger](https://xrpl.org/ledger.html) that will fetch the details of the latest ledger, you can tweak the code to fetch the details of a specific ledger and also to fetch account details and other information. Follow the link to read more about the ledger method.


6. In the index.js file, import the getWalletDetails and getLedgerDetails functions from the helpers folder.

Your index.js file should look like this:

{{ include_code("_code-samples/build-a-wallet/js/index.js", language="js") }}

In index.js file, define the variables that get the elements from the DOM and then add event listeners to the buttons using the javascript addEventListener method you read more about this method [here](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener). On clicking the buttons you'll be redirected to the Send XRP page and Transactions page respectively which we'll create in the next steps of this tutorial. 

Call the getWalletDetails and getLedgerDetails functions to fetch the account and ledger details respectively. We are using the setInterval method to call the getLedgerDetails function every 4 seconds. You can read learn about the setInterval method from this [link](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval). (Note: The ledger's estimate close time is 4 seconds, so we are calling the function every 4 seconds to get the latest ledger details. This can be adjusted according to your needs.)

**Optional:**

In the code above you'll notice addXrpLogo function, this function is used to add the XRP logo to the pages. This is an optional step just for aesthetic purpose, you can skip this step if you don't want to add the XRP logo to the pages.

{{ include_code("_code-samples/build-a-wallet/js/src/helpers/render-xrpl-logo.js", language="js") }}

7. Writing the HTML and CSS for the home page. Copy the contents of the [index.html](_code-samples/build-a-wallet/js/index.html) file to your index.html file.

It's basic HTML code with preloading CSS files, we are using plain CSS for this tutorial. You can use any CSS framework of your choice and beautify the page as you like. For now, we'll keep it simple. The CSS code is written in the index.css file. You can copy the code from the [index.css](_code-samples/build-a-wallet/js/index.css) file to your project's index.css file.

### Step 3: Creating the Send XRP Page

Till now, we have created the home page that displays the account and ledger details. In this step, we will create the Send XRP page that will allow users to send XRP to another account.

![Send XRP Page Screenshot](img/js-wallet-send-xrp.png)

1. Create a folder named send-xrp in the src directory.

2. Add two files named send-xrp.js and send-xrp.html. You can also create a separate css file depending on your choice. 

3. Copy the contents of the [send-xrp.html](_code-samples/build-a-wallet/js/src/send-xrp/send-xrp.html) file to your send-xrp.html file. The provided HTML code includes three input fields for the destination address, amount, and destination tag, each with their corresponding labels. Additionally, there is a button; to send the transaction. You can refer to this code as a template and modify it to fit your specific requirements. This may include adding additional input fields or changing the styling of the page to make it more appealing.

4. Copy the code written below to the send-xrp.js file.

{{ include_code("_code-samples/build-a-wallet/js/src/send-xrp/send-xrp.js", language="js") }}

5. In helpers folder, create a new file named submit-transaction.js and copy the code written below to the file.

{{ include_code("_code-samples/build-a-wallet/js/src/helpers/submit-transaction.js", language="js") }}

We won't go into details about signing and submitting the transaction, you can read more about the process [here](https://xrpl.org/send-xrp.html#send-xrp) and how to sign transactions [here](https://xrpl.org/sign.html).

In the code above we retrieve the necessary elements from the Document Object Model (DOM) and add event listeners to the buttons. When the buttons are clicked, the user will be redirected to either the home page or transactions page. Additionally, we add event listeners to the input fields to validate user input. You can customize the validation rules for each input field as per your needs, but commonly validated fields are the destination address, amount, and destination tag.

For validating the destination address, we use the isValidClassicAddress function from the xrpl.js library. For validating the amount, also a regular expression to ensure the input matches the expected format. You can choose any method to validate input fields as per your preference. As for the destination tag, any string can be used, and there is no specific validation required.

The code includes comments to help you understand the logic. To summarise, the input event listeners check whether the inputs are valid, and if they are, the send button is enabled. If the inputs are invalid, the send button is disabled, and the input field is highlighted with a red border to notify the user of the error.

In the send button event listener, we call the submitTransaction function and pass the destination address, amount, and destination tag as parameters. The submitTransaction function will submit the transaction to the XRP Ledger and return the transaction response. You can use the response to display a success message to the user or an error message in case of failure. Additionally, you can use the response to display the transaction details or create a popup to navigate to the transaction page.

### Step 4: Creating the Transactions Page

In this step, we will create the Transactions page that will display the latest transactions for the account. The transactions will be fetched from the XRP Ledger using the getTransactions method from the xrpl.js library. The transactions will be displayed in a table with the following columns:
  1. Account: The account that sent the transaction.
  2. Destination: The account that received the transaction.
  3. Amount: The amount of XRP sent in the transaction.
  4. Transaction Type: The type of transaction.
  5. Result: The result of the transaction.
  6. Link: A link to the transaction on the XRP Ledger Explorer.

![Transactions Page Screenshot](img/js-wallet-transaction.png)

1. Create a folder named transaction-history in the src directory.

2. Add two files named transaction-history.js and transaction-history.html. You can also create a separate css file depending on your choice.

3. In the transaction-history.js file, copy the code written below.

{{ include_code("_code-samples/build-a-wallet/js/src/transaction-history/transaction-history.js", language="js") }}

In the code above we create a HTML table and add headers to the table. Following that, we define a function that will fetch the latest transactions for the account and display them in the table. The getTransactions will fetch the latest transactions for the account and adds rows to the table.

There are various types of transactions that can be sent to the XRP Ledger. You can read more about the different types of transactions [here](https://xrpl.org/transaction-types.html). Not all transactions have XRP as their mode of payment, some use other currencies issued by an issuer. You can issue your own currency on the XRP Ledger and use it to send transactions. Read more about issuing currencies [here](https://xrpl.org/issue-a-fungible-token.html#issue-a-fungible-token). getCurrencyName function returns the name of the currency. If the currency is XRP, it returns XRP. If the currency is an issued currency, it returns the currency code. 

Note: While fetching the transactions, disable the Load More button to prevent the user from clicking it multiple times. Once the transactions are fetched, enable the button again if there are more transactions to be fetched.

All the necessary code with the comments is provided in the code sample above. Click the links mentioned in the comments to understand about the different functions used in the code.

4. Copy the contents of the [transaction-history.html](_code-samples/build-a-wallet/js/src/transaction-history/transaction-history.html) file to your transaction-history.html file.

You can use this code as a starting point and customize it to suit your needs. For example, you might want to add more columns to the table, such as transaction type or fee. Or you might want to change the styling of the page to make it more visually appealing.

Remember that you can also modify the CSS code in the index.css file to adjust the appearance of the transactions page. You can make changes to font size, color, and layout, for example, to give the page a unique look and feel. Furthermore, you can include RAW JSON data in the table to display additional information about the transaction. Check out the [XRP Ledger Explorer](https://livenet.xrpl.org/) to see how the transaction details are displayed.

### Step 5: Running the App

1. Set up vite bundler 

After completing the code for the app, you'll need to run and test it. Before you start, make sure you have Node.js installed on your system and a basic understanding of bundlers like Vite or Webpack.

We've used Vite to bundle the app, a fast and lightweight bundler that uses native ES modules and provides a rich plugin ecosystem. It's built on top of the native ES module system and leverages native browser APIs to provide a fast and efficient development experience. If you're unfamiliar with Vite, you can check out the [Vite documentation](https://vitejs.dev/guide/) to learn more.

To customize the Vite configuration, you can modify the vite.config.js file to suit your needs. For example, you can change the port number or the polyfills that are used. There are many other options available, so be sure to explore the [Vite documentation](https://vitejs.dev/config/) to learn more about the available configuration options.

xrpl.js requires some configuration to run with the vite bundler. You can find the configuration in the code below or you can check out the [xrpl.js documentation](https://github.com/XRPLF/xrpl.js/blob/main/UNIQUE_SETUPS.md#using-xrpljs-with-vite-react) to learn more about the configuration options.

{{ include_code("_code-samples/build-a-wallet/js/vite.config.js", language="js") }}

2. Add scripts to package.json

In your package.json file, add:

```json
"scripts": {
    "dev": "vite", // For running the app in development mode
    // "build": "vite build", -- If you want to build the app for production
    // "serve": "vite preview" -- If you want to serve the app
}
```

Now our app is ready to run. To run the app, open a terminal and run the following command:

```bash
yarn dev ## or npm run dev
```

This will start the app in development mode. Check your terminal for the URL where the app is running. Click the URL to open the app in your browser. You should see the home page of the app. 

If you're having trouble running the app, go through the steps again and make sure you've completed all the steps correctly. If still the app doesn't run, join the XRPL community on [Discord](https://discord.com/invite/KTNmhJDXqa) and ask for suggestions.

## Next Steps

Now that you have a functional wallet, you can take it in several new directions. The following are a few ideas:

- You could support more of the XRP Ledger's [transaction types](transaction-types.html) including [tokens](issued-currencies.html) and [cross-currency payments](cross-currency-payments.html)
- You could add support for multiple currencies, including IOUs.
- Allow the user to trade in the [decentralized exchange](decentralized-exchange.html)
- Add a way to request payments, such as with QR codes or URIs that open in your wallet.
- Support better account security including [regular key pairs](cryptographic-keys.html#regular-key-pair) or [multi-signing](multi-signing.html).

<!--{## common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}
{% include '_snippets/tx-type-links.md' %}
{% include '_snippets/rippled_versions.md' %}