# sync Package

### WaitGroup

WaitGroup digunakan untuk menunggu sekumpulan goroutine menyelesaikan eksekusinya. Proses yang perlu dilakukan:

* `goroutine` utama memanggil fungsi `Add`untuk mengeset jumlah `goroutine` yang perlu ditunggu. 
* Setiap goroutine memanggil fungsi `Done` ketika selesai melakukan eksekusi.
* `Wait` digunakan untuk blocking sampai semua goroutine selesai

**Contoh**

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	var (
		wg sync.WaitGroup
	)

	wg.Add(1)
	go func() {
		defer wg.Done()
		fmt.Println("call API 1")
		time.Sleep(500 * time.Millisecond)
	}()

	wg.Add(1)
	go func() {
		defer wg.Done()
		fmt.Println("call API 2")
		time.Sleep(500 * time.Millisecond)
	}()

	wg.Add(1)
	go func() {
		defer wg.Done()
		fmt.Println("call API 3")
		time.Sleep(500 * time.Millisecond)
	}()
	
	start := time.Now()
	wg.Wait()
	
	fmt.Printf("waktu tunggu : %v", time.Since(start).String())
}

```

Dalam contoh diatas, kita mensimulasikan kasus dimana kita perlu memanggil tiga API eksternal yang berbeda secara konkuren.

konkuren.playground: [https://play.golang.org/p/iOZv802ck5s](https://play.golang.org/p/iOZv802ck5s)

