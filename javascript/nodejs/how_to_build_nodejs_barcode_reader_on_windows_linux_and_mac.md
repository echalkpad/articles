# How to Build Node.js Barcode Reader on Windows, Linux and Mac

[Original URL](http://www.codeproject.com/Articles/1078098/How-to-Build-Node-js-Barcode-Reader-on-Windows-Lin)

> More and more Web developers tend to choose Node.js to build their Website because it is convenient to build complex server-side Web applications using only JavaScript. To extend and empower the...

More and more Web developers tend to choose Node.js to build their Website because it is convenient to build complex server-side Web applications using only JavaScript. To extend and empower the functionalities of Node.js applications on different platforms, Node.js allows developers to create addons in C/C++. In this post, let's take a glimpse of how easy to build a custom Node module on Windows, Linux, and Mac by wrapping [Dynamsoft Barcode Reader SDK](http://www.dynamsoft.com/Products/Dynamic-Barcode-Reader.aspx?ccid=cp), as well as how to integrate the module to quickly implement an online barcode reader.

- [Download Node.js Barcode Demo - 526.1 KB](http://www.codeproject.com/KB/showcase/1078098/nodejs-barcode-for-win-linux-mac.zip)

## Introduction

Dynamsoft Barcode Reader provides C/C++ shared libraries for Windows, Linux, and Mac. The ultimate benefit is that any high-level programming languages, such as JavaScript, Python, Java, Ruby, PHP and so on, are capable of wrapping C/C++ APIs using an extension or an addon. No matter which programming language you are familiar with, the SDK will expedite the coding work of barcode reader app with a few lines of code.

## Supported 1D/2D Barcode Types

- Code 39, Code 93, Code 128, Codabar, Interleaved 2 of 5, EAN-8, EAN-13, UPC-A, UPC-E,Industrial 2 of 5
- QRCode
- DataMatrix
- PDF417

## Supported Image Types

- BMP, JPEG, PNG, GIF, TIFF, PDF

## Environment

## Node.js Barcode Addon

Node.js Addons are dynamically-linked shared objects written in C/C++. If you have never touched this part before, you'd better follow the [official tutorial](https://nodejs.org/dist/latest-v5.x/docs/api/addons.html) to get started.

### Create Addon

Create _dbr.cc_ and add a method `DecodeFile`:

```
#include <node.h>
#include <string.h>
#include "If_DBR.h"
#include "BarcodeFormat.h"
#include "BarcodeStructs.h"
#include "ErrorCode.h"

using namespace v8;

void DecodeFile(const FunctionCallbackInfo<Value>& args) {

}

void Init(Handle<Object> exports) {
 NODE_SET_METHOD(exports, "decodeFile", DecodeFile);
}

NODE_MODULE(dbr, Init)
```

Parse parameters that passed from JavaScript:

```
Isolate* isolate = Isolate::GetCurrent();
HandleScope scope(isolate);
String::Utf8Value license(args[0]->ToString());
String::Utf8Value fileName(args[1]->ToString());
char *pFileName = *fileName;
char *pszLicense = *license;
__int64 llFormat = args[2]->IntegerValue();
Local<Function> cb = Local<Function>::Cast(args[3]);
```

Decode barcode image:

```
int iMaxCount = 0x7FFFFFFF;
ReaderOptions ro = {0};
pBarcodeResultArray pResults = NULL;
ro.llBarcodeFormat = llFormat;
ro.iMaxBarcodesNumPerPage = iMaxCount;

DBR_InitLicense(pszLicense);
int ret = DBR_DecodeFile(pFileName, &ro, &pResults);
```

Convert barcode formats to String:

```
const char articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii GetFormatStr(__int64 format)
{
 if (format == CODE_39)
 return "CODE_39";
 if (format == CODE_128)
 return "CODE_128";
 if (format == CODE_93)
 return "CODE_93";
 if (format == CODABAR)
 return "CODABAR";
 if (format == ITF)
 return "ITF";
 if (format == UPC_A)
 return "UPC_A";
 if (format == UPC_E)
 return "UPC_E";
 if (format == EAN_13)
 return "EAN_13";
 if (format == EAN_8)
 return "EAN_8";
 if (format == INDUSTRIAL_25)
 return "INDUSTRIAL_25";
 if (format == QR_CODE)
 return "QR_CODE";
 if (format == PDF417)
 return "PDF417";
 if (format == DATAMATRIX)
 return "DATAMATRIX";

 return "UNKNOWN";
}
```

Convert results to v8 object:

```
Local<Array> barcodeResults = Array::New(isolate);

for (int i = 0; i < count; i++)
{
 tmp = ppBarcodes[i];

 Local<Object> result = Object::New(isolate);
 result->Set(String::NewFromUtf8(isolate, "format"), String::NewFromUtf8(isolate, GetFormatStr(tmp->llFormat)));
 result->Set(String::NewFromUtf8(isolate, "value"), String::NewFromUtf8(isolate, tmp->pBarcodeData));

 barcodeResults->Set(Number::New(isolate, i), result);
}
```

### Build Addon

Prerequisites:

Install node-gyp:

```
npm install -g node-gyp
```

Create **binding.gyp** for multiplatform compilation:

```
{
 "targets": [
 {
 'target_name': "dbr",
 'sources': [ "dbr.cc" ],
 'conditions': [
 ['OS=="linux"', {
 'defines': [
 'LINUX_DBR',
 ],
 'include_dirs': [
 "/home/xiao/Dynamsoft/BarcodeReader4.0/Include"
 ],
 'libraries': [
 "-lDynamsoftBarcodeReaderx64", "-L/home/xiao/Dynamsoft/BarcodeReader4.0/Redist"
 ],
 'copies': [
 {
 'destination': 'build/Release/',
 'files': [
 '/home/xiao/Dynamsoft/BarcodeReader4.0/Redist/libDynamsoftBarcodeReaderx64.so'
 ]
 }]
 }],
 ['OS=="win"', {
 'defines': [
 'WINDOWS_DBR',
 ],
 'include_dirs': [
 "F:\Program Files (x86)\Dynamsoft\Barcode Reader 4.1\Components\C_C++\Include"
 ],
 'libraries': [
 "-lF:\Program Files (x86)\Dynamsoft\Barcode Reader 4.1\Components\C_C++\Lib\DBRx64.lib"
 ],
 'copies': [
 {
 'destination': 'build/Release/',
 'files': [
 'F:\Program Files (x86)\Dynamsoft\Barcode Reader 4.1\Components\C_C++\Redist\DynamsoftBarcodeReaderx64.dll'
 ]
 }]
 }],
 ['OS=="mac"', {
 'defines': [
 'MAC_DBR',
 ],
 'include_dirs' : [
 "/Applications/Dynamsoft/Barcode\ Reader\ 4.1/Include"
 ],
 'libraries': [
 "-lDynamsoftBarcodeReader"
 ]
 }]
 ]
 }
 ]
}
```

Replace the DBR Installation Directory with yours.

Configure the building environment:

```
node-gyp configure
```

Probably you will see the following error when configuring the environment on **Mac**:

```
error: xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance
```

Here is the solution:

```
sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
```

Build the project:

```
node-gyp build
```

## Online Barcode Reader

You have successfully built the Node barcode reader module. Now it is time to create a simple barcode reader app.

Install **Express** and **Formidable**:

```
npm install express
npm install formidable
```

Create a simple Website using **Express**:

```
var formidable = require('formidable');
var util = require('util');
var express = require('express');
var fs = require('fs');
var app = express();
var path = require('path');
var dbr = require('./build/Release/dbr');
var http = require('http');

fs.readFile('./license.txt', 'utf8', function(err, data) {

 app.use(express.static(__dirname));
 app.use(function(req, res, next) {
 res.header("Access-Control-Allow-Origin", "*");
 res.header("Access-Control-Allow-Methods", "PUT, POST, GET, DELETE, OPTIONS");
 res.header("Access-Control-Allow-Headers", "X-Requested-With, content-type");
 res.header("Access-Control-Allow-Credentials", true);
 next();
 });

 var server = app.listen(2016, function() {
 var host = server.address().address;
 var port = server.address().port;
 console.log('listening at http://%s:%s', host, port);
 });
});
```

Use **Formidable** to extract the image data from **Form**.

```
app.post('/upload', function(req, res) {
 var form = new formidable.IncomingForm();
 form.parse(req, function(err, fields, files) {
 var dir = 'uploads';

 fs.mkdir(dir, function(err) {
 var flag = fields.uploadFlag;
 var barcodeType = parseInt(fields.barcodetype);

 console.log('flag: ' + flag);

 if (flag === '1') { fs.readFile(files.fileToUpload.path, function(err, data) {
 var fileName = path.join(__dirname, dir, files.fileToUpload.name);
 console.log(fileName);
 fs.writeFile(fileName, data, function(err) {
 if (err) throw err;

 });
 });

 } else { var tmpFileName = path.join(__dirname, dir, 'tmp.jpg');
 var tmp = fs.createWriteStream(tmpFileName);
 var url = fields.fileToDownload;
 console.log('url: ' + url);
 http.get(url, function(response) {
 response.pipe(tmp);
 tmp.on('finish', function() {
 tmp.close(function() {

 });
 });
 });
 }
 });

 });
 });
```

Import the barcode module to decode the image file.

```
decodeBarcode(res, license, tmpFileName, barcodeType);
```

Run the app:

```
node server.js
```

Visit _<http://localhost:2016/index.htm>_:

![](http://www.codeproject.com/KB/showcase/1078098/image01.png)

Do you want to build a barcode reader app on Windows, Linux or Mac? Download the sample code and get your hands dirty now. For more information about [Dynamsoft Barcode Reader](http://www.dynamsoft.com/Products/Dynamic-Barcode-Reader.aspx?ccid=cp), please contact [support@dynamsoft.com](mailto:support@dynamsoft.com).
