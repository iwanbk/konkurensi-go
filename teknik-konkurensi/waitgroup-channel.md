# WaitGroup dengan Channel

Selain menggunakan `sync.WaitGroup` untuk menunggu selesainya eksekusi goroutines, kita juga bisa melakukannya dengan `channel`.

**Contoh**

{% code-tabs %}
{% code-tabs-item title="waitgroup-channel.go" %}
```go
package main

import (
	"fmt"
)

func main() {
	var (
		numGoroutines int
		errCh         = make(chan error)
	)

	numGoroutines++
	go func() {
		// call API 1
		var err error
		errCh <- err
	}()

	numGoroutines++
	go func() {
		// call API 2
		err := fmt.Errorf("err from API 2")
		errCh <- err
	}()

	numGoroutines++
	go func() {
		errCh <- fmt.Errorf("err from API 3")
	}()

	// iterasi sebanyak jumlah goroutine
	for i := 0; i < numGoroutines; i++ {
		err := <-errCh
		fmt.Printf("err : %v\n", err)

	}
}

```
{% endcode-tabs-item %}
{% endcode-tabs %}

Dalam contoh diatas, kita mensimulasikan kasus dimana kita perlu memanggil tiga API eksternal yang berbeda secara konkuren.

Berbeda dengan `WaitGroup` yang menggunakan `Wait` untuk menunggu, disini kita melakukan iterasi ke `errCh` sebanyak jumlah goroutine yang ada.

**Kelebihan** dari teknik ini adalah kita bisa mengirim data ke `goroutine` utama melalui `channel`, tidak seperti `WaitGroup`. Data yang dikirim juga tidak terbatas pada `error` channel, tapi bisa juga berupa `struct`

playground: [https://play.golang.org/p/kLpbY6nIkHa](https://play.golang.org/p/kLpbY6nIkHa)

