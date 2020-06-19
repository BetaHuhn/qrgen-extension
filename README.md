# qrgen-extension  - Browser extension for qrgen.cc website

> qrgen.cc is a free service that lets you create QR-Codes and shortened URLs from any link quickly and easily.

## Introduction

This repo contains the browser extension for both Chrome and Firefox. It is build with Vue.js and uses the qrgen.cc API to create short URLs. The code for the website and API can be found [here](https://github.com/BetaHuhn/qrgen).

## Installation

Download the repo:

```
git clone https://github.com/BetaHuhn/qrgen-extension
```

Change directory:

```
cd qrgen-extension
```

Install  dependencies:

```
npm install
```

Build extension:

```
npm run build
```

Then, go to [chrome://extensions/](chrome://extensions/) in your browser and enable developer mode. Click on "Load unpacked" and select the dist folder.


## Authors
* **Maximilian Schiller** ([@BetaHuhn](https://github.com/BetaHuhn)) - *Initial work*

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details
