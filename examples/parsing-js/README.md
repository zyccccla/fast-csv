`@fast-csv/parse` javascript parsing examples.

## Usage

To run all examples

```sh
npm run all-examples
```

To see a list of all available examples

```sh
npm run list
```

To run a specific example

```sh
npm run example -- {example_name}
```

## Examples

* [Manual Write](#manual-write)
* [Alternate Delimiters](#alternate-delimiters)
* [First Row As Headers](#first-row-as-headers)
* [Custom Headers](#custom-headers)
* [Renaming Headers](#renaming-headers)
* [Transforming Headers](#transforming-headers)
* [Skipping Columns](#skipping-columns)
* [Ignoring Empty Rows](#ignoring-empty-rows)
* [Transforming Rows](#transforming)
* [Validating Rows](#validation)
* [Max Rows](#max-rows)
* [Skip Rows](#skip-rows)
* [Skip Lines](#skip-lines)

<a name="manual-write"></a>
### Manual Write

[`examples/manual_write.example.js`](./examples/manual_write.example.js)
```sh
npm run example -- manual_write
```
```javascript
const csv = require('fast-csv');

const stream = csv.parse({ headers: true })
    .on('error', error => console.error(error))
    .on('data', row => console.log(row))
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

stream.write('header1,header2\n');
stream.write('col1,col2');
stream.end();

```

Expected output

```
{ header1: 'col1', header2: 'col2' }
Parsed 1 rows
```
---
<a name="alternate-delimiters"></a>
### Alternate Delimiter

You can provide a `delimiter` option to change the delimiter from a `,` character.

[`examples/alternate_delimiter.example.js`](./examples/alternate_delimiter.example.js)
```sh
npm run example -- alternate_delimiter
```
```javascript
const CSV_STRING = [
    'a1\tb1',
    'a2\tb2',
].join(EOL);

const stream = csv
    .parse({ delimiter: '\t' })
    .on('error', error => console.error(error))
    .on('data', row => console.log(row))
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

stream.write(CSV_STRING);
stream.end();
```

Expected output

```
[ 'a1', 'b1' ]
[ 'a2', 'b2' ]
Parsed 2 rows
```
---
<a name="first-row-as-headers"></a>
### First Row As Headers

If you expect the first line your CSV to be headers you may pass in a `headers` option. 

Setting the `headers` option to `true` will cause change each row to an object rather than an array.

[`examples/first_row_as_headers.example.js`](./examples/first_row_as_headers.example.js)
```sh
npm run example -- first_row_as_headers
```
```javascript
const { EOL } = require('os');

const CSV_STRING = [
    'a,b',
    'a1,b1',
    'a2,b2',
].join(EOL);

const stream = csv
  .parse({ headers: true })
  .on('error', error => console.error(error))
  .on('data', row => console.log(row))
  .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

stream.write(CSV_STRING);
stream.end();
```

Expected output
```
{ a: 'a1', b: 'b1' }
{ a: 'a2', b: 'b2' }
Parsed 2 rows
```
---
<a name="custom-headers"></a>
### Custom Headers

You may alternatively pass an array of header names.

**NOTE** The order of the headers array will should match the order of fields in the CSV, otherwise the data columns will not match.

[`examples/custom_headers.example.js`](./examples/custom_headers.example.js)
```sh
npm run example -- custom_headers
```
```javascript
const { EOL } = require('os');

const CSV_STRING = [
    'a1,b1',
    'a2,b2',
].join(EOL);

const stream = csv
    .parse({ headers: [ 'a', 'b' ] })
    .on('error', error => console.error(error))
    .on('data', row => console.log(row))
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

stream.write(CSV_STRING);
stream.end();

```

Expected output

```
{ a: 'a1', b: 'b1' }
{ a: 'a2', b: 'b2' }
Parsed 2 rows
```
---
<a name="renaming-headers"></a>
### Renaming Headers

If the CSV contains a header row but you want to provide custom headers you can pass an array of headers, and set `renameHeaders` to true.

[`examples/rename_headers.example.js`](./examples/rename_headers.example.js)
```sh
npm run example -- rename_headers
```
```javascript
const { EOL } = require('os');

const CSV_STRING = [
    'header1,header2',
    'a1,b1',
    'a2,b2',
].join(EOL);

const stream = csv
    .parse({ headers: [ 'a', 'b' ], renameHeaders: true })
    .on('error', error => console.error(error))
    .on('data', row => console.log(row))
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

stream.write(CSV_STRING);
stream.end();

```

Expected output

```
{ a: 'a1', b: 'b1' }
{ a: 'a2', b: 'b2' }
Parsed 2 rows
```
---
<a name="transforming-headers"></a>
### Transforming Headers

If the CSV contains a header row but you want transform the headers you can provide a function to the `headers` option.

[`examples/transform_headers.example.js`](./examples/transform_headers.example.js)
```sh
npm run example -- transform_headers
```
```javascript
const { EOL } = require('os');

const CSV_STRING = ['header1,header2', 'a1,b1', 'a2,b2'].join(EOL);

const stream = csv
    .parse({
        headers: headers => headers.map(h => h.toUpperCase()),
    })
    .on('error', error => console.error(error))
    .on('data', row => console.log(row))
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

stream.write(CSV_STRING);
stream.end();

```

Expected output

```
{ HEADER1: 'a1', HEADER2: 'b1' }
{ HEADER1: 'a2', HEADER2: 'b2' }
Parsed 2 rows
```
---
<a name="skipping-columns"></a>
### Skipping Columns

To omit some of the data columns you may not need, pass a sparse array as `headers`.

[`examples/skipping_columns.example.js`](./examples/skipping_columns.example.js)
```sh
npm run example -- skipping_columns
```
```javascript
const CSV_STRING = [
    'a1,b1,c1',
    'a2,b2,c2',
].join(EOL);

const stream = csv
    .parse({ headers: [ 'a', undefined, 'c' ] })
    .on('error', error => console.error(error))
    .on('data', row => console.log(row))
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

stream.write(CSV_STRING);
stream.end();

```

Expected output

```
{ a: 'a1', c: 'c1' }
{ a: 'a2', c: 'c2' }
Parsed 2 rows
```
---
<a name="ignoring-empty-rows"></a>
### Ignoring Empty Rows

If your data includes empty rows, the sort Excel might include at the end of the file for instance, you can ignore these by including the `ignoreEmpty` option.

Any rows consisting of nothing but empty strings and/or commas will be skipped, without emitting a 'data', 'data-invalid', or 'error' event.

[`examples/ignore_empty_rows.example.js`](./examples/ignore_empty_rows.example.js)
```sh
npm run example -- ignore_empty_rows
```
```javascript
const CSV_STRING = [
    'a1,b1',
    ',',      //empty row empty colums
    'a2,b2',
    '   ,\t', //empty row columns with just white space
    '',       //empty last line
].join(EOL);

const stream = csv
    .parse({ ignoreEmpty: true })
    .on('error', error => console.error(error))
    .on('data', row => console.log(row))
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

stream.write(CSV_STRING);
stream.end();

```

Expected output

```
[ 'a1', 'b1' ]
[ 'a2', 'b2' ]
Parsed 2 rows
```
---
<a name="transforming"></a>
### Transforming

You can transform data by providing a transform function. What is returned from the transform function will be provided to validate and emitted as a row.

[`examples/transform.example.js`](./examples/transform.example.js)
```sh
npm run example -- transform
```
```javascript
const CSV_STRING = [
    'firstName,lastName',
    'bob,yukon',
    'sally,yukon',
    'timmy,yukon',
].join(EOL);

const stream = csv
    .parse({ headers: true })
    .transform(data => ({
        firstName: data.firstName.toUpperCase(),
        lastName: data.lastName.toUpperCase(),
        properName: `${data.firstName} ${data.lastName}`,
    }))
    .on('error', error => console.error(error))
    .on('data', row => console.log(JSON.stringify(row)))
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

stream.write(CSV_STRING);
stream.end();
```

Expected output

```
{"firstName":"BOB","lastName":"YUKON","properName":"bob yukon"}
{"firstName":"SALLY","lastName":"YUKON","properName":"sally yukon"}
{"firstName":"TIMMY","lastName":"YUKON","properName":"timmy yukon"}
Parsed 3 rows
```

`fast-csv` also supports async transformation with a callback.

[`examples/transform_async.example.js`](./examples/transform_async.example.js)
```sh
npm run example -- transform_async
```
```javascript
const CSV_STRING = [
    'firstName,lastName',
    'bob,yukon',
    'sally,yukon',
    'timmy,yukon',
].join(EOL);

const stream = csv
    .parse({ headers: true })
    .transform((data, cb) => {
        setImmediate(() => cb(null, {
            firstName: data.firstName.toUpperCase(),
            lastName: data.lastName.toUpperCase(),
            properName: `${data.firstName} ${data.lastName}`,
        }));
    })
    .on('error', error => console.error(error))
    .on('data', row => console.log(JSON.stringify(row)))
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

stream.write(CSV_STRING);
stream.end();
```

Expected output

```
{"firstName":"BOB","lastName":"YUKON","properName":"bob yukon"}
{"firstName":"SALLY","lastName":"YUKON","properName":"sally yukon"}
{"firstName":"TIMMY","lastName":"YUKON","properName":"timmy yukon"}
Parsed 3 rows
```

---
<a name="validation"></a>
### Validation

You can validate each row in the CSV by providing a validate handler. If a row is invalid then a `data-invalid` event will be emitted with the row and the index.

[`examples/validate.example.js`](./examples/validate.example.js)
```sh
npm run example -- validate
```
```javascript
const CSV_STRING = [
    'firstName,lastName',
    'bob,yukon',
    'sally,yukon',
    'timmy,yukon',
].join(EOL);

const stream = csv
    .parse({ headers: true })
    .validate(data => data.firstName !== 'bob')
    .on('error', error => console.error(error))
    .on('data', row => console.log(`Valid [row=${JSON.stringify(row)}]`))
    .on('data-invalid', (row, rowNumber) => console.log(`Invalid [rowNumber=${rowNumber}] [row=${JSON.stringify(row)}]`))
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

stream.write(CSV_STRING);
stream.end();
```

Expected output

```
Invalid [rowNumber=1] [row={"firstName":"bob","lastName":"yukon"}]
Valid [row={"firstName":"sally","lastName":"yukon"}]
Valid [row={"firstName":"timmy","lastName":"yukon"}]
Parsed 2 rows
```

`fast-csv` also supports async validation, with a callback.

[`examples/validate_async.example.js`](./examples/validate_async.example.js)
```sh
npm run example -- validate_async
```
```javascript
const CSV_STRING = [
    'firstName,lastName',
    'bob,yukon',
    'sally,yukon',
    'timmy,yukon',
].join(EOL);

const stream = csv.parse({ headers: true })
    .validate((row, cb) => {
        setImmediate(() => cb(null, row.firstName !== 'bob'));
    })
    .on('error', error => console.error(error))
    .on('data', row => console.log(`Valid [row=${JSON.stringify(row)}]`))
    .on('data-invalid', (row, rowNumber) => console.log(`Invalid [rowNumber=${rowNumber}] [row=${JSON.stringify(row)}]`))
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

stream.write(CSV_STRING);
stream.end();
```

Expected output

```
Invalid [row={"firstName":"bob","lastName":"yukon"}]
Valid [row={"firstName":"sally","lastName":"yukon"}]
Valid [row={"firstName":"timmy","lastName":"yukon"}]
Parsed 3 rows
```

Sometimes you may wish to provide a reason that the row was invalid, you can use the callback to provide additional info.

[`examples/validate_with_reason.example.js`](./examples/validate_with_reason.example.js)
```sh
npm run example -- validate_with_reason
```
```javascript
const CSV_STRING = [
    'firstName,lastName',
    'bob,yukon',
    'sally,yukon',
    'timmy,yukon',
].join(EOL);

const stream = csv.parse({ headers: true })
    .validate((row, cb) => {
        const isValid = row.firstName !== 'bob';
        if (!isValid) {
            return cb(null, false, 'Name is bob');
        }
        return cb(null, true);
    })
    .on('error', error => console.error(error))
    .on('data', row => console.log(`Valid [row=${JSON.stringify(row)}]`))
    .on('data-invalid', (row, rowNumber, reason) => {
        console.log(`Invalid [rowNumber=${rowNumber}] [row=${JSON.stringify(row)}] [reason=${reason}]`);
    })
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

stream.write(CSV_STRING);
stream.end();
```

Expected output

```
Invalid [rowNumber=1] [row={"firstName":"bob","lastName":"yukon"}] [reason=Name is bob]
Valid [row={"firstName":"sally","lastName":"yukon"}]
Valid [row={"firstName":"timmy","lastName":"yukon"}]
Parsed 2 rows
```
---
<a name="max-rows"></a>
### Max Rows

In the following example there are 10 rows, but only 5 will be parsed because of the `maxRows` option.

[`examples/max_rows.example.example.js`](./examples/max_rows.example.js)
```sh
npm run example -- max_rows
```
```javascript
const rows = [
    'header1,header2\n',
    'col1,col1\n',
    'col2,col2\n',
    'col3,col3\n',
    'col4,col4\n',
    'col5,col5\n',
    'col6,col6\n',
    'col7,col7\n',
    'col8,col8\n',
    'col9,col9\n',
    'col10,col10',
];

const stream = csv
    .parse({ headers: true, maxRows: 5 })
    .on('error', error => console.error(error))
    .on('data', row => console.log(row))
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

rows.forEach(row => stream.write(row));
stream.end();
```

Expected output

```
{ header1: 'col1', header2: 'col1' }
{ header1: 'col2', header2: 'col2' }
{ header1: 'col3', header2: 'col3' }
{ header1: 'col4', header2: 'col4' }
{ header1: 'col5', header2: 'col5' }
Parsed 5 rows
```
---
<a name="skip-rows"></a>
### Skip Rows

In the following example the first 2 rows are skipped.

**NOTE** Notice how the header row is not skipped, only the row.

[`examples/skip_rows.example.example.js`](./examples/skip_rows.example.js)
```sh
npm run example -- skip_rows
```
```javascript
const rows = [
    'header1,header2\n',
    'col1,col1\n',
    'col2,col2\n',
    'col3,col3\n',
    'col4,col4\n',
    'col5,col5\n',
    'col6,col6\n',
];

const stream = csv
    .parse({ headers: true, skipRows: 2 })
    .on('error', error => console.error(error))
    .on('data', row => console.log(row))
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

rows.forEach(row => stream.write(row));
stream.end();
```

Expected output

```
{ header1: 'col3', header2: 'col3' }
{ header1: 'col4', header2: 'col4' }
{ header1: 'col5', header2: 'col5' }
{ header1: 'col6', header2: 'col6' }
Parsed 4 rows
```
---
<a name="skip-lines"></a>
### Skip Lines

In the following example the first 2 lines are skipped.

**NOTE** Notice how the headers come from the third line because the first two are skipped.

[`examples/skip_lines.example.example.js`](./examples/skip_lines.example.js)
```sh
npm run example -- skip_lines
```
```javascript
const csv = require('../../');

const rows = [
    'skip1_header1,skip1_header2\n',
    'skip2_header1,skip2_header2\n',
    'header1,header2\n',
    'col1,col1\n',
    'col2,col2\n',
    'col3,col3\n',
    'col4,col4\n',
];

const stream = csv
    .parse({ headers: true, skipLines: 2 })
    .on('error', error => console.error(error))
    .on('data', row => console.log(row))
    .on('end', rowCount => console.log(`Parsed ${rowCount} rows`));

rows.forEach(row => stream.write(row));
stream.end();
```

Expected output

```
{ header1: 'col1', header2: 'col1' }
{ header1: 'col2', header2: 'col2' }
{ header1: 'col3', header2: 'col3' }
{ header1: 'col4', header2: 'col4' }
Parsed 4 rows
```

