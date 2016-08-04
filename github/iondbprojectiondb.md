# iondbproject/iondb

[Original URL](https://github.com/iondbproject/iondb)

> "What is this?" Currently in the Arduino world, there doesn't exist an associative array or map implementation that is both easy to use and performance competitive. There also is little support for...

## [](https://github.com/iondbproject/iondb#what-is-this)"What is this?"

Currently in the Arduino world, there doesn't exist an associative array or map implementation that is both easy to use _and_ performance competitive. There also is little support for disk based storage options that don't involve writing it yourself. IonDB is fast, functional, and offers disk based storage out of the box.

In general, IonDB supports:

- Storing arbitrary values associated to a key
- Duplicate key support
- Range and Equality queries
- Disk based persistent data storage

## [](https://github.com/iondbproject/iondb#preamble)Preamble

These inclusions are necessary for any IonDB usage:

```
#include <SD.h> //If using file based implementations
#include "dictionary.h"
```

Then include some (or all) necessary implementation handlers:

```
#include "slhandler.h"
#include "oadictionaryhandler.h"
#include "oafdictionaryhandler.h"
#include "ffdictionaryhandler.h"
#include "bpptreehandler.h"
```

In the setup() function, initialize a dictionary (Shown here, a skiplist):

```
void setup() {
 //Declare the dictionary and handler structs
 dictionary_handler_t handler;
 dictionary_t dictionary;

 //Initialize handler
 sldict_init(&handler);

 //Create dictionary: Given handler, dictionary, key type, key size, value size, dict size
 dictionary_create(&handler, &dictionary, key_type_numeric_signed, sizeof(int), 60, 10);
}
```

### [](https://github.com/iondbproject/iondb#implementation-handler-methods)Implementation handler methods:

Implementation         | Handler Method
---------------------- | --------------
Skiplist               | sldict_init
Open Address Hash      | oadict_init
Open Address File Hash | oafdict_init
Flat File              | ffdict_init
B+ Tree                | bpptree_init

### [](https://github.com/iondbproject/iondb#dictionary-size-meanings)Dictionary size meanings:

Implementation         | Dictionary Size
---------------------- | -----------------------
Skiplist               | Skiplist maximum height
Open Address Hash      | Number of records
Open Address File Hash | Number of records
Flat File              | Not used
B+ Tree                | Not used

### [](https://github.com/iondbproject/iondb#keys-and-values)Keys and values

Keys and values are **specific type agnostic**, there are only three categories of keys. Two macros are provided to bridge the gap between IonDB keys and concrete keys.

```
/* Key creation */

//Suppose the key is an int
int my_key = 64;
ion_key_t key = IONIZE(my_key); //A prepared key

//Inline is fine too:
ion_key_t key = IONIZE(64);

//Any type is supported
unsigned long long my_key = 2147483648ull;
ion_key_t key = IONIZE(my_key);

/* Key retrieval */

//Retrieve an int from a key
int my_key = NEUTRALIZE(int, key);

//Retrieve unsigned long long from a key
unsigned long long my_key = NEUTRALIZE(unsigned long long, key);
```

#### [](https://github.com/iondbproject/iondb#ionization-functions)Ionization functions

Function               | Type
---------------------- | --------------------------------
IONIZE(any)            | IONIZE :: any -> ion_key_t
NEUTRALIZE(atype, key) | NEUTRALIZE :: ion_key_t -> atype

#### [](https://github.com/iondbproject/iondb#key-categories)Key categories:

- key_type_numeric_signed
- key_type_numeric_unsigned
- key_type_char_array (String)

## [](https://github.com/iondbproject/iondb#file-based-implementations)File based implementations

An SD shield, and a FAT formatted SD card is required to work with IonDB. The Arduino ethernet shield is recommended. The following initialization is required when working with file bsaed implementations:

```
//Use pin 10 if using an Uno, pin 53 if Mega
pinMode(10, OUTPUT);
//Change depending on what SD shield is used
SD.begin(4);
```

## [](https://github.com/iondbproject/iondb#usage)Usage

### [](https://github.com/iondbproject/iondb#insert)Insert

```
ion_key_t key = IONIZE(some_key);
ion_value_t value = some_value;
dictionary_insert(&dictionary, key, value);
```

### [](https://github.com/iondbproject/iondb#delete)Delete

```
ion_key_t key = IONIZE(some_key);
dictionary_delete(&dictionary, key);
```

### [](https://github.com/iondbproject/iondb#query)Query

```
ion_key_t key = IONIZE(some_key);
ion_value_t my_value = malloc(value_size); // Create buffer to hold returned value
dictionary_get(&dictionary, key, my_value);
// Process data
//...
free(my_value);
```

### [](https://github.com/iondbproject/iondb#cursors)Cursors

A functional implementation exists for equality cursors (Multiple value query on same key) and range cursors (Query key-value pairs across a bound of keys), however a dictionary level interface for cursor access has yet to be finalized. Implementation level access is demonstrated in the Benchmark source.

## [](https://github.com/iondbproject/iondb#full-example)Full Example

Written in Arduino compliant wiring.

```
#include <SD.h>
#include "dictionary.h"
#include "slhandler.h"

void setup() {
 //Declare the dictionary and handler structs
 dictionary_handler_t handler;
 dictionary_t dictionary;

 //Initialize handler
 sldict_init(&handler);

 //Create dictionary: Given handler, dictionary, key type, key size, value size, dict size
 dictionary_create(&handler, &dictionary, key_type_numeric_signed, sizeof(int), 60, 10);

 ion_key_t key = IONIZE(42);
 ion_value_t value = (ion_value_t) "Hello IonDB";

 dictionary_insert(&dictionary, key, value);

 ion_value_t returned_value = (ion_value_t) malloc(60); //from value_size
 dictionary_get(&dictionary, key, returned_value);
 printf("Returned %s\n", returned_value);
 free(returned_value);
}

void loop() {}
```
