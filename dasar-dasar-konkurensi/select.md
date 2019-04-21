# Select

`Select` bekerja seperti `switch` statement, tetapi berlaku untuk `channel:`

* receive ke semua channel
* blocked, sampai bisa menerima dari salah satu channel
* jika bisa receive dari beberapa channel secara bersamaan, pilih salah satu channel secara random
* jika ada `default`, akan segera dieksekusi jika tidak ada `channel` yang siap

**Contoh**

\*\*\*\*[**https://gobyexample.com/select**](https://gobyexample.com/select)\*\*\*\*

**Contoh dengan `default`**

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	var (
		ch1 = make(chan struct{})
		ch2 = make(chan struct{})
	)
	go func() {
		ch1 <- struct{}{}
	}()
	go func() {
		time.Sleep(2 * time.Millisecond)
		ch2 <- struct{}{}
	}()
	
	// uncomment code dibawah untuk melihat perbedaan
	// time.Sleep(1 * time.Millisecond)
	
	select {
	case <-ch1:
		fmt.Println("dari ch1")
	case <-ch2:
		fmt.Println("dari ch2")
	default:
		fmt.Println("dari default")
	}

}

```

playground: [https://play.golang.org/p/RkoXOi77V3i](https://play.golang.org/p/RkoXOi77V3i)

Jika dieksekusi, kita belum tentu akan mendapatkan "dari ch1"  karena ada waktu yang diperlukan untuk membuat dan mengeksekusi goroutine. Dari percobaan penulis, "dari default" adalah hasil yang lebih sering didapat.

**Latihan**

Uncomment `time.Sleep(1 * time.Millisecond` di atas `select` untuk melihat perbedaan

