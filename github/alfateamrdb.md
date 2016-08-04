# alfateam/rdb

[Original URL](https://github.com/alfateam/rdb/blob/master/docs/docs.md)

> All examples below are found at npmjs.org/package/rdb-demo. Documentation and examples Connecting connect to postgres connect to mySql pool size end pool end all pools

All examples below are found at [npmjs.org/package/rdb-demo](https://npmjs.org/package/rdb-demo).

## [](https://github.com/alfateam/rdb/blob/master/docs/docs.md#documentation-and-examples)_Documentation and examples_

**Connecting**<br>
[connect to postgres](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_connecttopostgres)<br>
[connect to mySql](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_connecttomysql)<br>
[pool size](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_poolsize)<br>
[end pool](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_endpool)<br>
[end all pools](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_endallpools)<br>
[logging](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_logging)<br>
**Basic querying**<br>
[getById](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_getbyid)<br>
[tryGetById](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_trygetbyid)<br>
[tryGetFirst](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_trygetfirst)<br>
[join](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_join)<br>
[hasMany](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_hasmany)<br>
[hasOne](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_hasone)<br>
[composite keys](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_compositekeys)<br>
[getById eagerly](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_getbyideager)<br>
[tryGetFirst eagerly](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_trygetfirsteager)<br>
[toDto](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_todto)<br>
[toDto with strategy](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_todtowithstrategy)<br>
[toDto with orderBy](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_todtowithorderby)<br>
[toDto with orderBy descending](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_todtowithorderbydesc)<br>
[toDto ignoring columns](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_serializable)<br>
[toJSON](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_tojson)<br>
[toJSON with strategy](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_tojsonwithstrategy)<br>
[getMany](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_getmany)<br>
[getMany lazily](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_getmanylazy)<br>
[getMany eagerly](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_getmanyeager)<br>
[(many)ToDto](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_manytodto)<br>
[(many)ToDto with strategy](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_manytodtowithstrategy)<br>
[(many)ToJSON](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_manytojson)<br>
[(many)ToJSON with strategy](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_manytojsonwithstrategy)<br>
[Raw SQL query](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_rawsqlquery)<br>
[Raw SQL Query With Parameters](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_rawsqlquerywithparameters)<br>
**Streaming**<br>
[streaming rows](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_streameager)<br>
[streaming json](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_streamjsoneager)<br>
**Persistence**<br>
[update](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_update)<br>
[insert](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_insert)<br>
[delete](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_delete) [cascade delete](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_cascadedelete)<br>
[bulk delete](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_bulkdelete)<br>
[bulk cascade delete](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_bulkcascadedelete)<br>
[default values](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_defaultvalues)<br>
[conventions](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_conventions)<br>
[update a join-relation](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_updatejoin)<br>
[update a hasOne-relation](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_updatehasone)<br>
[update a hasMany-relation](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_updatehasmany)<br>
[locking](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_locking)

**Filters**<br>
[equal](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_equal)<br>
[notEqual](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_notequal)<br>
[not](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_not)<br>
[lessThan](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_lessthan)<br>
[lessThanOrEqual](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_lessthanorequal)<br>
[greaterThan](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_greaterthan)<br>
[greaterThanOrEqual](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_greaterthanorequal)<br>
[between](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_between)<br>
[in](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_in)<br>
[startsWith](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_startswith)<br>
[endsWith](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_endswith)<br>
[contains](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_contains)<br>
[iEqual](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_iequal)<br>
[iStartsWith](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_istartswith)<br>
[iEndsWith](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_iendswith)<br>
[iContains](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_icontains)<br>
[exists](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_exists)<br>
[or](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_or)<br>
[and](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_and)<br>
[or alternative syntax](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_oralternative)<br>
[and alternative syntax](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_andalternative)<br>
[sub filter](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_subfilter)<br>
[composite filter](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_compositefilter)<br>
[raw sql filter](https://github.com/alfateam/rdb/blob/master/docs/docs.md#_rawsqlfilter)

## [](https://github.com/alfateam/rdb/blob/master/docs/docs.md#contents)_Contents_

[]() [connect to postgres](https://github.com/alfateam/rdb-demo/blob/master/connect.js)

```
var rdb = require('rdb');
var db = rdb('postgres://postgres:postgres@localhost/test');
//will use pool with 10 connections by default
//alternatively: var db = rdb.pg('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [connect to mySql](https://github.com/alfateam/rdb-demo/blob/master/mySql/connect.js)

```
var rdb = require('rdb');
var db = rdb.mySql('mysql://root@localhost/rdbDemo?multipleStatements=true');
//will use pool with 10 connections by default

db.transaction()
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [pool size](https://github.com/alfateam/rdb-demo/blob/master/poolOptions.js)

```
var rdb = require('rdb');

var poolOptions = {size: 20};
var db = rdb('postgres://postgres:postgres@localhost/test', poolOptions);

db.transaction()
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function onOk() {
 console.log('Success. Created pool with max 20 connections.');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [end pool](https://github.com/alfateam/rdb-demo/blob/master/endPool.js)

```
var rdb = require('rdb');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(db.end)
 .then(onOk, onFailed);

function onOk() {
 console.log('Pool ended.');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [end all pools](https://github.com/alfateam/rdb-demo/blob/master/endAllPools.js)

```
var rdb = require('rdb');

var dbPg = rdb('postgres://postgres:postgres@localhost/test');
var dbMySql = rdb.mySql('mysql://root@localhost/rdbDemo?multipleStatements=true');

connectPg()
 .then(connectMySql)
 .then(rdb.end)
 .then(onOk, onFailed);


function connectPg() {
 return dbPg.transaction()
 .then(rdb.commit)
 .then(null, rdb.rollback);
}

function connectMySql() {
 return dbMySql.transaction()
 .then(rdb.commit)
 .then(null, rdb.rollback);
}

function onOk() {
 console.log('Pools ended.');
}

function onFailed(err) {
 console.log(err.stack);
}
```

[]() [logging](https://github.com/alfateam/rdb-demo/blob/master/logging.js)

```
var rdb = require('rdb');
rdb.log(console.log); //will log sql and parameters

var Customer = rdb.table('_customer');
Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getById)
 .then(update)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getById() {
 return Customer.getById('a0000000-0000-0000-0000-000000000000');
}

function update(customer) {
 customer.name = 'Ringo'; 
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [getById](https://github.com/alfateam/rdb-demo/blob/master/getById.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');
Customer.column('cBalance').numeric().as('balance');
Customer.column('cRegdate').date().as('registeredDate');
Customer.column('cIsActive').boolean().as('isActive');
Customer.column('cPicture').binary().as('picture');
Customer.column('cDocument').json().as('document');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getById)
 .then(printCustomer)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getById() {
 return Customer.getById('a0000000-0000-0000-0000-000000000000');
}

function printCustomer(customer) {
 var format = 'Customer Id: %s, name: %s, Balance: %s, Registered Date: %s, Is Active: %s, Picture: %s, , Document: %s'; 
 var args = [format, customer.id, customer.name, customer.balance, customer.registeredDate, customer.isActive, customer.picture, JSON.stringify(customer.document)];
 console.log.apply(null,args);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [tryGetById](https://github.com/alfateam/rdb-demo/blob/master/tryGetById.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');
Customer.column('cBalance').numeric().as('balance');
Customer.column('cRegdate').date().as('registeredDate');
Customer.column('cIsActive').boolean().as('isActive');
Customer.column('cPicture').binary().as('picture');
Customer.column('cDocument').json().as('document');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(tryGetById)
 .then(printCustomer)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function tryGetById() {
 return Customer.tryGetById('a0000000-0000-0000-0000-000000000000');
}

function printCustomer(customer) {
 if (customer) {
 var format = 'Customer Id: %s, name: %s, Balance: %s, Registered Date: %s, Is Active: %s, Picture: %s, , Document: %s'; 
 var args = [format, customer.id, customer.name, customer.balance, customer.registeredDate, customer.isActive, customer.picture, JSON.stringify(customer.document)];
 console.log.apply(null,args);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [tryGetFirst](https://github.com/alfateam/rdb-demo/blob/master/tryGetFirst.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(tryGetFirst)
 .then(printCustomer)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function tryGetFirst() {
 var filter = Customer.name.equal('John');
 return Customer.tryGetFirst(filter);
}

function printCustomer(customer) {
 if (customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);;
 }
 else
 console.log('customer not found');
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [join](https://github.com/alfateam/rdb-demo/blob/master/join.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');
var Order = rdb.table('_order');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').guid().as('customerId');
Order.join(Customer).by('oCustomerId').as('customer');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrder)
 .then(printOrder)
 .then(printCustomer)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrder() {
 return Order.getById('a0000000-a000-0000-0000-000000000000');
}

function printOrder(order) {
 var format = 'Order Id: %s, Order No: %s, Customer Id: %s'; 
 var args = [format, order.id, order.orderNo, order.customerId];
 console.log.apply(null,args);
 return order.customer; //this is a promise
}

function printCustomer(customer) {
 if (!customer)
 return;
 var format = 'Customer Id: %s, name: %s'; 
 var args = [format, customer.id, customer.name];
 console.log.apply(null,args);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [hasMany](https://github.com/alfateam/rdb-demo/blob/master/hasMany.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var OrderLine = rdb.table('_orderLine');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');
OrderLine.column('lProduct').string().as('product');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrder)
 .then(printOrder)
 .then(printLines)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrder() {
 return Order.getById('b0000000-b000-0000-0000-000000000000');
}

function printOrder(order) {
 var format = 'Order Id: %s, Order No: %s'; 
 var args = [format, order.id, order.orderNo];
 console.log.apply(null,args);
 return order.lines; //this is a promise
}

function printLines(lines) {
 lines.forEach(printLine);

 function printLine(line) {
 var format = 'Line Id: %s, Order Id: %s, Product: %s'; 
 var args = [format, line.id, line.orderId, line.product];
 console.log.apply(null,args);
 } 
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [hasOne](https://github.com/alfateam/rdb-demo/blob/master/hasOne.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var DeliveryAddress = rdb.table('_deliveryAddress');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');

DeliveryAddress.primaryColumn('dId').guid().as('id');
DeliveryAddress.column('dOrderId').string().as('orderId');
DeliveryAddress.column('dName').string().as('name');
DeliveryAddress.column('dStreet').string().as('street');

var deliveryAddress_order_relation = DeliveryAddress.join(Order).by('dOrderId').as('order');
Order.hasOne(deliveryAddress_order_relation).as('deliveryAddress');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrder)
 .then(printOrder)
 .then(printDeliveryAddress)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrder() {
 return Order.getById('b0000000-b000-0000-0000-000000000000');
}

function printOrder(order) {
 var format = 'Order Id: %s, Order No: %s'; 
 var args = [format, order.id, order.orderNo];
 console.log.apply(null,args);
 return order.deliveryAddress; //this is a promise
}

function printDeliveryAddress(address) {
 var format = 'DeliveryAddress Id: %s, Order Id: %s, %s'; 
 var args = [format, address.id, address.orderId, address.name, address.street];
 console.log.apply(null,args);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [composite keys](https://github.com/alfateam/rdb-demo/blob/master/compositeKeys.js)

```
var rdb = require('rdb');

var Order = rdb.table('_compositeOrder');
var OrderLine = rdb.table('_compositeOrderLine');

Order.primaryColumn('oCompanyId').numeric().as('companyId');
Order.primaryColumn('oOrderNo').numeric().as('orderNo');

OrderLine.primaryColumn('lCompanyId').numeric().as('companyId');
OrderLine.primaryColumn('lOrderNo').numeric().as('orderNo');
OrderLine.primaryColumn('lLineNo').numeric().as('lineNo');
OrderLine.column('lProduct').string().as('product');

var line_order_relation = OrderLine.join(Order).by('lCompanyId', 'lOrderNo').as('order');
Order.hasMany(line_order_relation).as('lines');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrder)
 .then(printOrder)
 .then(printLines)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrder() {
 var companyId = 1,
 orderId = 1001;
 return Order.getById(companyId, orderId);
}

function printOrder(order) {
 console.log('Company Id: %s, Order No: %s', order.companyId, order.orderNo)
 return order.lines; //this is a promise
}

function printLines(lines) {
 lines.forEach(printLine);

 function printLine(line) {
 var format = 'Company Id: %s, Order No: %s, Line No: %s, Product: %s'; 
 var args = [format, line.companyId, line.orderNo, line.lineNo, line.product];
 console.log.apply(null,args);
 } 
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [getById eagerly](https://github.com/alfateam/rdb-demo/blob/master/getByIdEager.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');
var Order = rdb.table('_order');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').guid().as('customerId');
Order.join(Customer).by('oCustomerId').as('customer');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrderWithCustomer)
 .then(printOrder)
 .then(printCustomer)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrderWithCustomer() {
 var fetchingStrategy = {customer : null}; //alternatively: {customer : {}} 
 return Order.getById('a0000000-a000-0000-0000-000000000000', fetchingStrategy);
}

function printOrder(order) {
 var format = 'Order Id: %s, Order No: %s, Customer Id: %s'; 
 var args = [format, order.id, order.orderNo, order.customerId];
 console.log.apply(null,args);
 return order.customer; //this is a promise
}

function printCustomer(customer) {
 if (!customer)
 return;
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [tryGetFirst eagerly](https://github.com/alfateam/rdb-demo/blob/master/tryGetFirstEager.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');
var Order = rdb.table('_order');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').guid().as('customerId');

Order.join(Customer).by('oCustomerId').as('customer');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(tryGetFirstOrderWithCustomer)
 .then(printOrder)
 .then(printCustomer)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function tryGetFirstOrderWithCustomer() {
 var filter = Order.customer.name.equal('John');
 var strategy = {customer : null};
 return Order.tryGetFirst(filter, strategy);
}

function printOrder(order) {
 if (!order) {
 console.log('order not found');
 return;
 }
 var format = 'Order Id: %s, Order No: %s, Customer Id: %s'; 
 var args = [format, order.id, order.orderNo, order.customerId];
 console.log.apply(null,args);
 return order.customer;
}

function printCustomer(customer) {
 if (!customer) 
 return;
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [toDto](https://github.com/alfateam/rdb-demo/blob/master/toDto.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var Customer = rdb.table('_customer');
var OrderLine = rdb.table('_orderLine');
var DeliveryAddress = rdb.table('_deliveryAddress');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').string().as('customerId');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').string().as('orderId');
OrderLine.column('lProduct').string().as('product');

DeliveryAddress.primaryColumn('dId').guid().as('id');
DeliveryAddress.column('dOrderId').string().as('orderId');
DeliveryAddress.column('dName').string().as('name');
DeliveryAddress.column('dStreet').string().as('street');

var order_customer_relation = Order.join(Customer).by('oCustomerId').as('customer');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var deliveryAddress_order_relation = DeliveryAddress.join(Order).by('dOrderId').as('order');
Order.hasOne(deliveryAddress_order_relation).as('deliveryAddress');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrder)
 .then(toDto)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrder() {
 return Order.getById('b0000000-b000-0000-0000-000000000000');
}

function toDto(order) {
 return order.toDto(/*strategy*/);
 //default strategy, expand all hasOne and hasMany relations
}

function print(dto) {
 console.log(dto);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [toDto with strategy](https://github.com/alfateam/rdb-demo/blob/master/toDtoWithStrategy.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var Customer = rdb.table('_customer');
var OrderLine = rdb.table('_orderLine');
var DeliveryAddress = rdb.table('_deliveryAddress');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').string().as('customerId');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');
OrderLine.column('lProduct').string().as('product');

DeliveryAddress.primaryColumn('dId').guid().as('id');
DeliveryAddress.column('dOrderId').string().as('orderId');
DeliveryAddress.column('dName').string().as('name');
DeliveryAddress.column('dStreet').string().as('street');

var order_customer_relation = Order.join(Customer).by('oCustomerId').as('customer');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var deliveryAddress_order_relation = DeliveryAddress.join(Order).by('dOrderId').as('order');
Order.hasOne(deliveryAddress_order_relation).as('deliveryAddress');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrder)
 .then(toDto)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrder() {
 return Order.getById('b0000000-b000-0000-0000-000000000000');
}

function toDto(order) {
 var strategy = {customer : null, lines : null, deliveryAddress : null};
 return order.toDto(strategy);
}

function print(dto) {
 console.log(dto);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [toDto with orderBy](https://github.com/alfateam/rdb-demo/blob/master/toDtoWithOrderBy.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var OrderLine = rdb.table('_orderLine');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').string().as('orderId');
OrderLine.column('lProduct').string().as('product');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrder)
 .then(toDto)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrder() {
 return Order.getById('b0000000-b000-0000-0000-000000000000');
}

function toDto(order) {
 var strategy = {
 lines: {
 orderBy: ['product'] 
 //alternative: orderBy: ['product asc']
 }
 };
 return order.toDto(strategy);
}

function print(dto) {
 console.log(dto);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [toDto with orderBy descending](https://github.com/alfateam/rdb-demo/blob/master/toDtoWithOrderByDesc.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var OrderLine = rdb.table('_orderLine');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').string().as('orderId');
OrderLine.column('lProduct').string().as('product');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrder)
 .then(toDto)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrder() {
 return Order.getById('b0000000-b000-0000-0000-000000000000');
}

function toDto(order) {
 var strategy = {
 lines: {
 orderBy: ['product desc'] 
 }
 };
 return order.toDto(strategy);
}

function print(dto) {
 console.log(dto);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [toDto ignoring columns](https://github.com/alfateam/rdb-demo/blob/master/serializable.js)

```
var rdb = require('rdb');

var User = rdb.table('_User');
User.primaryColumn('uId').guid().as('id');
User.column('uUserId').string().as('userId');
User.column('uPassword').string().as('password').serializable(false);
User.column('uEmail').string().as('email');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getUser)
 .then(toDto)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getUser() {
 return User.getById('87654400-0000-0000-0000-000000000000');
}

function toDto(user) {
 return user.toDto().then(console.log);
 //will print all properties except password
 //because it is not serializable
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [toJSON](https://github.com/alfateam/rdb-demo/blob/master/toJSON.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var Customer = rdb.table('_customer');
var OrderLine = rdb.table('_orderLine');
var DeliveryAddress = rdb.table('_deliveryAddress');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').string().as('customerId');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').string().as('orderId');
OrderLine.column('lProduct').string().as('product');

DeliveryAddress.primaryColumn('dId').guid().as('id');
DeliveryAddress.column('dOrderId').string().as('orderId');
DeliveryAddress.column('dName').string().as('name');
DeliveryAddress.column('dStreet').string().as('street');

var order_customer_relation = Order.join(Customer).by('oCustomerId').as('customer');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var deliveryAddress_order_relation = DeliveryAddress.join(Order).by('dOrderId').as('order');
Order.hasOne(deliveryAddress_order_relation).as('deliveryAddress');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrder)
 .then(toJSON)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrder() {
 return Order.getById('b0000000-b000-0000-0000-000000000000');
}

function toJSON(order) {
 return order.toJSON(/*strategy*/);
 //default strategy, expand all hasOne and hasMany relations
}

function print(json) {
 console.log(json);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [toJSON with strategy](https://github.com/alfateam/rdb-demo/blob/master/toJSONWithStrategy.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var Customer = rdb.table('_customer');
var OrderLine = rdb.table('_orderLine');
var DeliveryAddress = rdb.table('_deliveryAddress');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').string().as('customerId');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');
OrderLine.column('lProduct').string().as('product');

DeliveryAddress.primaryColumn('dId').guid().as('id');
DeliveryAddress.column('dOrderId').string().as('orderId');
DeliveryAddress.column('dName').string().as('name');
DeliveryAddress.column('dStreet').string().as('street');

var order_customer_relation = Order.join(Customer).by('oCustomerId').as('customer');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var deliveryAddress_order_relation = DeliveryAddress.join(Order).by('dOrderId').as('order');
Order.hasOne(deliveryAddress_order_relation).as('deliveryAddress');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrder)
 .then(toJSON)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrder() {
 return Order.getById('b0000000-b000-0000-0000-000000000000');
}

function toJSON(order) {
 var strategy = {customer : null, lines : null, deliveryAddress : null};
 return order.toJSON(strategy);
}

function print(json) {
 console.log(json);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [getMany](https://github.com/alfateam/rdb-demo/blob/master/getMany.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getAllCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getAllCustomers() {
 return Customer.getMany();
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function print(json) {
 console.log(json);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [getMany lazily](https://github.com/alfateam/rdb-demo/blob/master/getManyLazy.js)

```
var rdb = require('rdb'),
 promise = require('promise');

var Order = rdb.table('_order');
var OrderLine = rdb.table('_orderLine');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');
OrderLine.column('lProduct').string().as('product');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getAllOrders)
 .then(printOrders)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getAllOrders() {
 return Order.getMany();
}

function printOrders(orders) {
 var printAllLines = [];
 orders.forEach(printOrder);

 function printOrder(order) {
 var format = 'Order Id: %s, Order No: %s'; 
 var args = [format, order.id, order.orderNo];
 console.log.apply(null,args);
 printAllLines.push(order.lines.then(printLines));
 }
 return promise.all(printAllLines);
}

function printLines(lines) {
 lines.forEach(printLine);

 function printLine(line) {
 var format = 'Line Id: %s, Order Id: %s, Product: %s'; 
 var args = [format, line.id, line.orderId, line.product];
 console.log.apply(null,args);
 } 
}

function onOk() {
 console.log('success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [getMany eager](https://github.com/alfateam/rdb-demo/blob/master/getManyEager.js)

```
var rdb = require('rdb'),
 promise = require('promise');

var Order = rdb.table('_order');
var OrderLine = rdb.table('_orderLine');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');
OrderLine.column('lProduct').string().as('product');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getAllOrders)
 .then(printOrders)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getAllOrders() {
 var emptyFilter;
 var strategy = {lines : null};
 return Order.getMany(emptyFilter, strategy);
}

function printOrders(orders) {
 var printAllLines = [];
 orders.forEach(printOrder);

 function printOrder(order) {
 var format = 'Order Id: %s, Order No: %s'; 
 var args = [format, order.id, order.orderNo];
 console.log.apply(null,args);
 printAllLines.push(order.lines.then(printLines));
 }
 return promise.all(printAllLines);
}

function printLines(lines) {
 lines.forEach(printLine);

 function printLine(line) {
 var format = 'Line Id: %s, Order Id: %s, Product: %s'; 
 var args = [format, line.id, line.orderId, line.product];
 console.log.apply(null,args);
 } 
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [(many)ToDto](https://github.com/alfateam/rdb-demo/blob/master/manyToDto.js)

```
var rdb = require('rdb'),
 inspect = require('util').inspect;

var Order = rdb.table('_order');
var Customer = rdb.table('_customer');
var OrderLine = rdb.table('_orderLine');
var DeliveryAddress = rdb.table('_deliveryAddress');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').string().as('customerId');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');
OrderLine.column('lProduct').string().as('product');

DeliveryAddress.primaryColumn('dId').guid().as('id');
DeliveryAddress.column('dOrderId').string().as('orderId');
DeliveryAddress.column('dName').string().as('name');
DeliveryAddress.column('dStreet').string().as('street');

var order_customer_relation = Order.join(Customer).by('oCustomerId').as('customer');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var deliveryAddress_order_relation = DeliveryAddress.join(Order).by('dOrderId').as('order');
Order.hasOne(deliveryAddress_order_relation).as('deliveryAddress');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrders)
 .then(toDto)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrders() {
 return Order.getMany();
}

function toDto(orders) {
 return orders.toDto(/*strategy*/);
 //default strategy, expand all hasOne and hasMany relations
}

function print(dto) {
 console.log(inspect(dto,false,10));
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [(many)ToDto with strategy](https://github.com/alfateam/rdb-demo/blob/master/manyToDtoWithStrategy.js)

```
var rdb = require('rdb'),
 inspect = require('util').inspect;

var Order = rdb.table('_order');
var Customer = rdb.table('_customer');
var OrderLine = rdb.table('_orderLine');
var DeliveryAddress = rdb.table('_deliveryAddress');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').string().as('customerId');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');
OrderLine.column('lProduct').string().as('product');

DeliveryAddress.primaryColumn('dId').guid().as('id');
DeliveryAddress.column('dOrderId').string().as('orderId');
DeliveryAddress.column('dName').string().as('name');
DeliveryAddress.column('dStreet').string().as('street');

var order_customer_relation = Order.join(Customer).by('oCustomerId').as('customer');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var deliveryAddress_order_relation = DeliveryAddress.join(Order).by('dOrderId').as('order');
Order.hasOne(deliveryAddress_order_relation).as('deliveryAddress');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrders)
 .then(toDto)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrders() {
 return Order.getMany();
}

function toDto(orders) {
 var strategy = {customer : null, lines : null, deliveryAddress : null};
 return orders.toDto(strategy);
}

function print(dto) {
 console.log(inspect(dto,false,10));
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [(many)ToJSON](https://github.com/alfateam/rdb-demo/blob/master/manyToJSON.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var Customer = rdb.table('_customer');
var OrderLine = rdb.table('_orderLine');
var DeliveryAddress = rdb.table('_deliveryAddress');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').string().as('customerId');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');
OrderLine.column('lProduct').string().as('product');

DeliveryAddress.primaryColumn('dId').guid().as('id');
DeliveryAddress.column('dOrderId').string().as('orderId');
DeliveryAddress.column('dName').string().as('name');
DeliveryAddress.column('dStreet').string().as('street');

var order_customer_relation = Order.join(Customer).by('oCustomerId').as('customer');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var deliveryAddress_order_relation = DeliveryAddress.join(Order).by('dOrderId').as('order');
Order.hasOne(deliveryAddress_order_relation).as('deliveryAddress');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrders)
 .then(toJSON)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrders() {
 return Order.getMany();
}

function toJSON(orders) {
 return orders.toJSON(/*strategy*/);
 //default strategy, expand all hasOne and hasMany relations
}

function print(json) {
 console.log(json);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [(many)ToJSON with strategy](https://github.com/alfateam/rdb-demo/blob/master/manyToJSONWithStrategy.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var Customer = rdb.table('_customer');
var OrderLine = rdb.table('_orderLine');
var DeliveryAddress = rdb.table('_deliveryAddress');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').string().as('customerId');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');
OrderLine.column('lProduct').string().as('product');

DeliveryAddress.primaryColumn('dId').guid().as('id');
DeliveryAddress.column('dOrderId').string().as('orderId');
DeliveryAddress.column('dName').string().as('name');
DeliveryAddress.column('dStreet').string().as('street');

var order_customer_relation = Order.join(Customer).by('oCustomerId').as('customer');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var deliveryAddress_order_relation = DeliveryAddress.join(Order).by('dOrderId').as('order');
Order.hasOne(deliveryAddress_order_relation).as('deliveryAddress');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrders)
 .then(toJSON)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrders() {
 return Order.getMany();
}

function toJSON(orders) {
 var strategy = {customer : null, lines : null, deliveryAddress : null};
 return orders.toJSON(strategy);
}

function print(json) {
 console.log(json);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [Raw SQL Query](https://github.com/alfateam/rdb-demo/blob/master/rawSqlQuery.js)

```
var rdb = require('rdb');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getUniqueCustomerIds)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getUniqueCustomerIds() {
 return rdb.query('SELECT DISTINCT oCustomerId AS "customerId" FROM _order');
}

function print(rows) {
 console.log(rows);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [Raw SQL Query With Parameters](https://github.com/alfateam/rdb-demo/blob/master/rawSqlQueryWithParameters.js)

```
var rdb = require('rdb');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrderNos)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrderNos() {
 return rdb.query({
 sql: 'SELECT oOrderNo AS "orderNo" FROM _order WHERE oOrderNo LIKE ?',
 parameters: ['%04']
 });
}

function print(rows) {
 console.log(rows);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [streaming rows](https://github.com/alfateam/rdb-demo/blob/master/streamEager.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var OrderLine = rdb.table('_orderLine');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');
OrderLine.column('lProduct').string().as('product');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var db = rdb('postgres://postgres:postgres@localhost/test');

var emptyFilter;
var strategy = {
 lines: {
 orderBy: ['product']
 },
 orderBy: ['orderNo'],
 limit: 5,
};

Order.createReadStream(db, emptyFilter, strategy).on('data', printOrder);

function printOrder(order) {
 var format = 'Order Id: %s, Order No: %s';
 console.log(format, order.id, order.orderNo);
 order.lines.forEach(printLine);
}

function printLine(line) {
 var format = 'Line Id: %s, Order Id: %s, Product: %s';
 console.log(format, line.id, line.orderId, line.product);
}
```

[]() [streaming json](https://github.com/alfateam/rdb-demo/blob/master/streamJSONEager.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var OrderLine = rdb.table('_orderLine');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');
OrderLine.column('lProduct').string().as('product');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var db = rdb('postgres://postgres:postgres@localhost/test');

var emptyFilter;
var strategy = {
 lines: {
 orderBy: ['product']
 },
 orderBy: ['orderNo'],
 limit: 5,
};

Order.createJSONReadStream(db, emptyFilter, strategy).pipe(process.stdout);
```

[]() [update](https://github.com/alfateam/rdb-demo/blob/master/update.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getById)
 .then(update)
 .then(getById) //will use cache
 .then(verifyUpdated)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getById() {
 return Customer.getById('a0000000-0000-0000-0000-000000000000');
}

function update(customer) {
 customer.name = 'Ringo'; 
}

function verifyUpdated(customer) {
 if (customer.name !== 'Ringo')
 throw new Error('this will not happen');
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [insert](https://github.com/alfateam/rdb-demo/blob/master/insert.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(insert)
 .then(getById) //will use cache
 .then(verifyInserted)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function insert() {
 var customer = Customer.insert('abcdef00-0000-0000-0000-000000000000')
 customer.name = 'Paul';
}

function getById() {
 return Customer.getById('abcdef00-0000-0000-0000-000000000000');
}

function verifyInserted(customer) {
 if (customer.name !== 'Paul')
 throw new Error('this will not happen');
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [delete](https://github.com/alfateam/rdb-demo/blob/master/delete.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');
Customer.column('cBalance').numeric().as('balance');
Customer.column('cRegdate').date().as('registeredDate');
Customer.column('cIsActive').boolean().as('isActive');
Customer.column('cPicture').binary().as('picture');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getById)
 .then(deleteCustomer)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getById() { 
 return Customer.getById('87654321-0000-0000-0000-000000000000');
}

function deleteCustomer(customer) {
 customer.delete();
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err.stack);
}
```

[]() [cascadeDelete](https://github.com/alfateam/rdb-demo/blob/master/cascadeDelete.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');
var Order = rdb.table('_order');
var OrderLine = rdb.table('_orderLine');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').guid().as('customerId');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');

var orderToCustomer = Order.join(Customer).by('oCustomerId').as('customer');
Customer.hasMany(orderToCustomer).as('orders');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getById)
 .then(deleteCustomer)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getById() {
 return Customer.getById('87654399-0000-0000-0000-000000000000');
}

function deleteCustomer(customer) {
 customer.cascadeDelete();
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err.stack);
}
```

[]() [bulk delete](https://github.com/alfateam/rdb-demo/blob/master/bulkDelete.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');
Customer.column('cBalance').numeric().as('balance');
Customer.column('cRegdate').date().as('registeredDate');
Customer.column('cIsActive').boolean().as('isActive');
Customer.column('cPicture').binary().as('picture');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(deleteCustomer)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function deleteCustomer() { 
 var filter = Customer.id.eq('87654321-0000-0000-0000-000000000000');
 return Customer.delete(filter);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err.stack);
}
```

[]() [bulk cascadeDelete](https://github.com/alfateam/rdb-demo/blob/master/bulkCascadeDelete.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');
var Order = rdb.table('_order');
var OrderLine = rdb.table('_orderLine');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').guid().as('customerId');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');

var orderToCustomer = Order.join(Customer).by('oCustomerId').as('customer');
Customer.hasMany(orderToCustomer).as('orders');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(deleteCustomer)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function deleteCustomer() {
 var filter = Customer.id.eq('87654399-0000-0000-0000-000000000000');
 Customer.cascadeDelete(filter);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err.stack);
}
```

[]() [default values](https://github.com/alfateam/rdb-demo/blob/master/defaultValues.js)

```
var rdb = require('rdb');

var buf = new Buffer(10);
buf.write('\u00bd + \u00bc = \u00be', 0);

var Customer = rdb.table('_customer');

/*unless overridden, numeric is default 0, 
string is default null, 
guid is default null,
date is default null,
binary is default null,
boolean is default false,
json is default null
*/ 

Customer.primaryColumn('cId').guid().as('id').default(null);
Customer.column('cName').string().as('name').default('default name');
Customer.column('cBalance').numeric().as('balance').default(2000);
Customer.column('cRegdate').date().as('registeredDate').default(new Date());
Customer.column('cIsActive').boolean().as('isActive').default(true);
Customer.column('cPicture').binary().as('picture').default(buf);
Customer.column('cDocument').json().as('document').default({foo: true});

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(insert)
 .then(print) 
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function insert() {
 var customer = Customer.insert('abcdef02-0000-0000-0000-000000000000')
 return customer.toJSON();
}

function print(json) {
 console.log(json);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [conventions](https://github.com/alfateam/rdb-demo/blob/master/conventions.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid(); //property name will also be cId
Customer.column('cName').string(); //property name will also be cName

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(insert)
 .then(print) 
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function insert() {
 var customer = Customer.insert('abcdef01-0000-0000-0000-000000000000')
 customer.cName = 'Paul';
 return customer.toJSON();
}

function print(json) {
 console.log(json);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [update a join-relation](https://github.com/alfateam/rdb-demo/blob/master/updateJoin.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');
var Order = rdb.table('_order');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').guid().as('customerId');

Order.join(Customer).by('oCustomerId').as('customer');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getById)
 .then(update)
 .then(verifyUpdated)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getById() {
 return Order.getById('b0000000-b000-0000-0000-000000000000');
}

function update(order) {
 var yokoId = '12345678-0000-0000-0000-000000000000';
 order.customerId = yokoId;
 return order.customer; 
}

function verifyUpdated(customer) {
 if (customer.name !== 'Yoko')
 throw new Error('this will not happen');
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [update a hasOne-relation](https://github.com/alfateam/rdb-demo/blob/master/updateHasOne.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var DeliveryAddress = rdb.table('_deliveryAddress');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');

DeliveryAddress.primaryColumn('dId').guid().as('id');
DeliveryAddress.column('dOrderId').string().as('orderId');
DeliveryAddress.column('dName').string().as('name');
DeliveryAddress.column('dStreet').string().as('street');

var deliveryAddress_order_relation = DeliveryAddress.hasone(Order).by('dOrderId').as('order');
Order.hasOne(deliveryAddress_order_relation).as('deliveryAddress')
```
;

```
var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(insertDeliveryAddress)
 .then(verifyUpdated)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function insertDeliveryAddress() {
 var address = DeliveryAddress.insert('eeeeeeee-0000-0000-0000-000000000000');
 address.orderId = 'a0000000-a000-0000-0000-000000000000';
 address.name = 'Sgt. Pepper';
 address.street = 'L18 Penny Lane';
 return address.order;
}

function verifyUpdated(order) {
 return order.deliveryAddress.then(verifyUpdatedAddress);;
}

function verifyUpdatedAddress(deliveryAddress) {
 if (deliveryAddress.street !== 'L18 Penny Lane')
 throw new Error('this will not happen');
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [update a hasMany-relation](https://github.com/alfateam/rdb-demo/blob/master/updateHasMany.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var OrderLine = rdb.table('_orderLine');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');
OrderLine.column('lProduct').string().as('product');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');

var db = rdb('postgres://postgres:postgres@localhost/test');
var orderIdWithNoLines = 'c0000000-c000-0000-0000-000000000000';

db.transaction()
 .then(insertOrderLine1)
 .then(insertOrderLine2)
 .then(verifyUpdated)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function insertOrderLine1() {
 var line = OrderLine.insert('eeeeeeee-0001-0000-0000-000000000000');
 line.orderId = orderIdWithNoLines;
 line.product = 'Roller blades';
 return line.order;
}

function insertOrderLine2() {
 var line = OrderLine.insert('eeeeeeee-0002-0000-0000-000000000000');
 line.orderId = orderIdWithNoLines;
 line.product = 'Helmet';
 return line.order;
}

function verifyUpdated(order) {
 return order.lines.then(verifyUpdatedLines);
}

function verifyUpdatedLines(lines) {
 if (lines.length !== 2)
 throw new Error('this will not happen');
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [locking](https://github.com/alfateam/rdb-demo/blob/master/exclusive.js)

```
var rdb = require('rdb');
var promise = require('promise');

var Customer = rdb.table('_customer');
Customer.primaryColumn('cId').guid().as('id');
Customer.column('cBalance').numeric().as('balance');
Customer.exclusive();

var db = rdb('postgres://postgres:postgres@localhost/test');

showBalance()
 .then(updateConcurrently)
 .then(showBalance)
 .then(onOk, onFailed);

function showBalance() {
 return db.transaction()
 .then(getById)
 .then(printBalance)
 .then(rdb.commit)
 .then(null, rdb.rollback);

 function printBalance(customer) {
 console.log('Balance: ' + customer.balance)
 }
}

function updateConcurrently() {
 var concurrent1 = db.transaction()
 .then(getById)
 .then(increaseBalance)
 .then(rdb.commit)
 .then(null, rdb.rollback);

 var concurrent2 = db.transaction()
 .then(getById)
 .then(increaseBalance)
 .then(rdb.commit)
 .then(null, rdb.rollback);
 return promise.all([concurrent1, concurrent2]);
}

function getById() {
 console.log('....................');
 return Customer.getById('a0000000-0000-0000-0000-000000000000');
}

function increaseBalance(customer) {
 customer.balance += 100;
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [equal](https://github.com/alfateam/rdb-demo/blob/master/filtering/equal.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.name.equal('John');
 //same as Customer.name.eq('John'); 
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [notEqual](https://github.com/alfateam/rdb-demo/blob/master/filtering/notEqual.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.name.notEqual('John');
 //same as Customer.name.ne('John'); 
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [not](https://github.com/alfateam/rdb-demo/blob/master/filtering/not.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.name.equal('John').not();
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [lessThan](https://github.com/alfateam/rdb-demo/blob/master/filtering/lessThan.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cBalance').numeric().as('balance');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.balance.lessThan(5000);
 //same as Customer.balance.lt(5000); 
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s, balance : %s', customer.id, customer.name, customer.balance);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [lessThanOrEqual](https://github.com/alfateam/rdb-demo/blob/master/filtering/lessThanOrEqual.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cBalance').numeric().as('balance');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.balance.lessThanOrEqual(8123);
 //same as Customer.balance.le(8123); 
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s, balance : %s', customer.id, customer.name, customer.balance);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [greaterThan](https://github.com/alfateam/rdb-demo/blob/master/filtering/greaterThan.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cBalance').numeric().as('balance');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.balance.greaterThan(5000);
 //same as Customer.balance.gt(5000); 
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s, balance : %s', customer.id, customer.name, customer.balance);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [greaterThanOrEqual](https://github.com/alfateam/rdb-demo/blob/master/filtering/greaterThanOrEqual.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cBalance').numeric().as('balance');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.balance.greaterThanOrEqual(8123);
 //same as Customer.balance.ge(8123); 
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s, balance : %s', customer.id, customer.name, customer.balance);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [between](https://github.com/alfateam/rdb-demo/blob/master/filtering/between.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cBalance').numeric().as('balance');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.balance.between(3000, 8123);
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s, balance : %s', customer.id, customer.name, customer.balance);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}

var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.name.in(['John', 'Yoko']);
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [startsWith](https://github.com/alfateam/rdb-demo/blob/master/filtering/startsWith.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.name.startsWith('Jo');
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [endsWith](https://github.com/alfateam/rdb-demo/blob/master/filtering/endsWith.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.name.endsWith('nny');
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [contains](https://github.com/alfateam/rdb-demo/blob/master/filtering/contains.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.name.contains('ohn');
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [contains](https://github.com/alfateam/rdb-demo/blob/master/filtering/contains.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.name.contains('ohn');
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [iEqual (postgres only)](https://github.com/alfateam/rdb-demo/blob/master/filtering/iEqual.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.name.iEqual('jOhN');
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [iStartsWith (postgres only)](https://github.com/alfateam/rdb-demo/blob/master/filtering/iStartsWith.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.name.iStartsWith('jo');
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [iEndsWith (postgres only)](https://github.com/alfateam/rdb-demo/blob/master/filtering/iEndsWith.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.name.iEndsWith('nNy');
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [iContains (postgres only)](https://github.com/alfateam/rdb-demo/blob/master/filtering/iContains.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var filter = Customer.name.iContains('oHn');
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [exists](https://github.com/alfateam/rdb-demo/blob/master/filtering/exists.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var DeliveryAddress = rdb.table('_deliveryAddress');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');

DeliveryAddress.primaryColumn('dId').guid().as('id');
DeliveryAddress.column('dOrderId').string().as('orderId');
DeliveryAddress.column('dName').string().as('name');
DeliveryAddress.column('dStreet').string().as('street');

var deliveryAddress_order_relation = DeliveryAddress.join(Order).by('dOrderId').as('order');
Order.hasOne(deliveryAddress_order_relation).as('deliveryAddress');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrders)
 .then(toJSON)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrders() {
 var filter = Order.deliveryAddress.exists();
 return Order.getMany(filter);
}

function toJSON(orders) {
 return orders.toJSON();
}

function print(json) {
 console.log(json);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}

var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var john = Customer.name.equal('John');
 var yoko = Customer.name.equal('Yoko');
 var filter = john.or(yoko);

 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [and](https://github.com/alfateam/rdb-demo/blob/master/filtering/and.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cIsActive').boolean().as('isActive');
Customer.column('cBalance').numeric().as('balance');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var isActive = Customer.isActive.equal(true);
 var highBalance = Customer.balance.greaterThan(8000);
 var filter = isActive.and(highBalance);
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [or alternative syntax](https://github.com/alfateam/rdb-demo/blob/master/filtering/orAlternative.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var john = Customer.name.equal('John');
 var yoko = Customer.name.equal('Yoko');
 var filter = rdb.filter.or(john).or(yoko);
 //alternatively rdb.filter.and(john).or(yoko);
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [and alternative syntax](https://github.com/alfateam/rdb-demo/blob/master/filtering/andAlternative.js)

```
var rdb = require('rdb');

var Customer = rdb.table('_customer');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cIsActive').boolean().as('isActive');
Customer.column('cBalance').numeric().as('balance');
Customer.column('cName').string().as('name');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getFilteredCustomers)
 .then(printCustomers)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getFilteredCustomers() {
 var isActive = Customer.isActive.equal(true);
 var highBalance = Customer.balance.greaterThan(8000);
 var filter = rdb.filter.and(isActive).and(highBalance);
 //alternatively rdb.filter.or(isActive).and(highBalance);
 return Customer.getMany(filter);
}

function printCustomers(customers) {
 customers.forEach(printCustomer);

 function printCustomer(customer) {
 console.log('Customer Id: %s, name: %s', customer.id, customer.name);
 }
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [sub filter](https://github.com/alfateam/rdb-demo/blob/master/filtering/subFilter.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var DeliveryAddress = rdb.table('_deliveryAddress');

Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');

DeliveryAddress.primaryColumn('dId').guid().as('id');
DeliveryAddress.column('dOrderId').string().as('orderId');
DeliveryAddress.column('dName').string().as('name');
DeliveryAddress.column('dStreet').string().as('street');

var deliveryAddress_order_relation = DeliveryAddress.join(Order).by('dOrderId').as('order');
Order.hasOne(deliveryAddress_order_relation).as('deliveryAddress');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrders)
 .then(toJSON)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrders() {
 var filter = Order.deliveryAddress.street.startsWith('Node');
 return Order.getMany(filter);
}

function toJSON(orders) {
 return orders.toJSON();
}

function print(json) {
 console.log(json);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [composite filter](https://github.com/alfateam/rdb-demo/blob/master/filtering/compositeFilter.js)

```
var rdb = require('rdb');

var Order = rdb.table('_order');
var Customer = rdb.table('_customer');
var OrderLine = rdb.table('_orderLine');

Order.primaryColumn('oId').guid().as('id');
Order.column('oCustomerId').guid().as('customerId');
Order.column('oOrderNo').string().as('orderNo');

Customer.primaryColumn('cId').guid().as('id');
Customer.column('cIsActive').boolean().as('isActive');
Customer.column('cBalance').numeric().as('balance');
Customer.column('cName').string().as('name');

OrderLine.primaryColumn('lId').guid().as('id');
OrderLine.column('lOrderId').guid().as('orderId');
OrderLine.column('lProduct').string().as('product');

Order.join(Customer).by('oCustomerId').as('customer');

var line_order_relation = OrderLine.join(Order).by('lOrderId').as('order');
Order.hasMany(line_order_relation).as('lines');



var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrders)
 .then(toJSON)
 .then(print)
 .then(rdb.commit)
 .then(null, rdb.rollback)
 .then(onOk, onFailed);

function getOrders() {
 var isActive = Order.customer.isActive.eq(true);
 var didOrderCar = Order.lines.product.contains('car');
 var filter = isActive.and(didOrderCar);
 //alternatively rdb.filter.and(isActive).and(didOrderCar);
 return Order.getMany(filter);
}

function toJSON(orders) {
 return orders.toJSON();
}

function print(json) {
 console.log(json);
}

function onOk() {
 console.log('Success');
 console.log('Waiting for connection pool to teardown....');
}

function onFailed(err) {
 console.log('Rollback');
 console.log(err);
}
```

[]() [raw sql filter](https://github.com/alfateam/rdb-demo/blob/master/filtering/rawSqlFilter.js)

```
var inspect = require('util').inspect;
var rdb = require('rdb');

var Order = rdb.table('_order');
Order.primaryColumn('oId').guid().as('id');
Order.column('oOrderNo').string().as('orderNo');
Order.column('oCustomerId').guid().as('customerId');

var Customer = rdb.table('_customer');
Customer.primaryColumn('cid').guid().as('id');
Customer.column('cName').string().as('name');
Customer.column('cBalance').string().as('balance');
Customer.column('cIsActive').boolean().as('isActive');

var orderCustomerJoin = Order.join(Customer).by('oCustomerId').as('customer');
Customer.hasMany(orderCustomerJoin).as('orders');

var db = rdb('postgres://postgres:postgres@localhost/test');

db.transaction()
 .then(getOrders)
 .then(printOrders)
 .then(db.commit)
 .then(null, db.rollback)
 .then(onOk, console.log);

function getOrders() {
 var filter = {
 sql: 'exists (select 1 from _customer where _customer.cId = oCustomerId and _customer.cBalance > 3000 and _customer.cName LIKE ?)',
 parameters: ['%o%']
 };
 return Order.getMany(filter);
}

function printOrders(orders) {
 var strategy = {customer: null}
 return orders.toDto(strategy).then(printDtos);
}

function printDtos(dtos) {
 console.log(inspect(dtos,false,10));
}

function onOk() {
 console.log('Done');
}
```
