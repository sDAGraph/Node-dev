# browserify

Browsers don't have the require method defined, but Node.js does. With Browserify you can write code that uses require in the same way that you would use it in Node. 
  - require('modules') in the browser
  - Use a node-style require() to organize your browser code and load modules installed by npm.
  - browserify will recursively analyze all the require() calls in your app in order to build a bundle you can serve up to the browser in a single <script> tag.

### First install node, which ships with npm. Then do:
> npm install -g browserify

### Create js file as main.js
```const Web3 = require('web3');
const web3 = new Web3('https://kovan.infura.io');
web3.eth.getBalance('0x9E632F36D8193a23ee76e7C14698aCF4b92869A2').then(console.log)
```
### Now recursively bundle up all the required modules starting at main.js into a single file called bundle.js with the browserify command:

> browserify main.js -o bundle.js
### Drop a single <script> tag into your html and you're done!
```
<script src="bundle.js"></script>
```

