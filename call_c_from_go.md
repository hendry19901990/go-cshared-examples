# Calling c from go

* First export the GOPATH
```shell
   export GOPATH=/opt/gopath
```

* Second let's create a folder for our project
```shell
mkdir $GOPATH/src/github.com/{your_user}/hello_test
cd $GOPATH/src/github.com/{your_user}/hello_test
```

compile to shared .so

* and last:
```shell
gcc -c greeter.c
gcc -shared -o libgreeter.so -fPIC greeter.c
```

**************************************************
* Create greeter.h 

```c
greeter.h 

struct Greetee {
    const char *name;
    int year;
};

int greet(struct Greetee *g, char *out);
```
**************************************************
* Create greeter.c

```c
greeter.c

int greet(struct Greetee *g, char *out) {
    int n;

    n = sprintf(out, "Greetings, %s from %d! We come in peace :)", g->name, g->year);

    return n;
}
```
**************************************************
* Create main.go
```go
main.go

package main

// #cgo CFLAGS: -g -Wall
// #include <stdlib.h>
// #include "greeter.h"
import "C"
import (
	"fmt"
	"unsafe"
)

func main() {

        name := C.CString("Gopher")
	defer C.free(unsafe.Pointer(name))

	year := C.int(2018)

	g := C.struct_Greetee{
		name: name,
		year: year,
	}

	ptr := C.malloc(C.sizeof_char * 1024)
	defer C.free(unsafe.Pointer(ptr))

	size := C.greet(&g, (*C.char)(ptr))

	b := C.GoBytes(ptr, size)
	fmt.Println(string(b))

}
```

**************************************************
Run the following commands:
 * go install
 * $GOPATH/bin/hello

**************************************************

###Note: Everything was done in ubuntu

