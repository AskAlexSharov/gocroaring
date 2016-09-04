# gocroaring[![GoDoc](https://godoc.org/github.com/RoaringBitmap/gocroaring?status.svg)](https://godoc.org/github.com/RoaringBitmap/gocroaring)
Go wrapper for CRoaring (a C/C++ implementation at https://github.com/RoaringBitmap/CRoaring)

Roaring is  used by Apache Spark (https://spark.apache.org/), Apache Kylin (http://kylin.io),
Druid.io (http://druid.io/), Whoosh (https://pypi.python.org/pypi/Whoosh/)
and  Apache Lucene (http://lucene.apache.org/) (as well as supporting systems
such as Solr and Elastic).

The original java version can be found at https://github.com/RoaringBitmap/RoaringBitmap

There is a native Go version at https://github.com/RoaringBitmap/roaring


This code is licensed under Apache License, Version 2.0 (ASL2.0).

Copyright 2016 by the authors.


### References

-  Samy Chambi, Daniel Lemire, Owen Kaser, Robert Godin,
Better bitmap performance with Roaring bitmaps,
Software: Practice and Experience Volume 46, Issue 5, pages 709–719, May 2016
http://arxiv.org/abs/1402.6407 This paper used data from http://lemire.me/data/realroaring2014.html
- Daniel Lemire, Gregory Ssi-Yan-Kai, Owen Kaser, Consistently faster and smaller compressed bitmaps with Roaring, Software: Practice and Experience (accepted in 2016, to appear) http://arxiv.org/abs/1603.06549



### Dependencies

None in particular.

Naturally, you also need to grab the roaring code itself:
  - go get github.com/RoaringBitmap/gocroaring


### Example

Here is a simplified but complete example:

```go
package main

import (
	"fmt"

	"github.com/RoaringBitmap/gocroaring"
)

func main() {
	// example inspired by https://github.com/fzandona/goroar
	fmt.Println("==roaring==")
	rb1 := gocroaring.NewBitmap(1, 2, 3, 4, 5, 100, 1000)
	rb1.RunOptimize() // improves compression
	fmt.Println("Cardinality: ", rb1.GetCardinality())
	fmt.Println("Contains 3? ", rb1.Contains(3))

	rb2 := gocroaring.NewBitmap()
	rb2.Add(3, 4, 1000)
	rb2.RunOptimize() // improves compression

	rb1.And(rb2)
	// prints {3,4,1000}
	fmt.Println(rb1)

	rb3 := gocroaring.NewBitmap(1, 5)
	rb3.Or(rb1)

	fmt.Println(rb3.ToArray())
	fmt.Println(rb3)

	rb4 := gocroaring.FastOr(rb1, rb2, rb3) // optimized way to compute unions between many bitmaps
	fmt.Println(rb4)

	// next we include an example of serialization
	buf := make([]byte, rb1.GetSerializedSizeInBytes())
	rb1.Write(buf) // we omit error handling
	newrb, _ := gocroaring.Read(buf)
	if rb1.Equals(newrb) {
		fmt.Println("I wrote the content to a byte stream and read it back.")
	}
}
```

### Documentation

Current documentation is available at http://godoc.org/github.com/RoaringBitmap/gocroaring

### Compatibility with Java RoaringBitmap library

You can read bitmaps in Go, Java, C, C++ that have been serialized in Java, Java, C, C++.
