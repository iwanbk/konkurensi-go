---
description: Func yang me-return channel
---

# Generator

Generator adalah pattern dimana suatu `func` membuat `goroutine` yang melakukan suatu komputasi dan me-return receive-only `channel`yang dapat digunakan oleh pemanggil `func` untuk menerima value yang dihasilkan oleh `goroutine`tersebut.

Contoh

```go
package main

import (
	"fmt"
)

// produsen mem-produce string sejumlah `num` yang diawali oleh `msgPrefix`.
func produsen(msgPrefix string, num int) <-chan string { // Returns receive-only channel of strings.

	// create channel yang akan digunakan mengirim data
	// dari goroutine ke pemanggil func
	c := make(chan string)

	go func() { // buat goroutine dari dalam func

		// close channel ketika goroutine sudah selesai
		// sehingga pemanggil func tahu bahwa
		// goroutine telah selesai dan tidak ada lagi 
		// data yang akan dihasilkan
		defer close(c)

		for i := 0; i < num; i++ {
			// buat string dengan format [msg_prefix]_[urutan]
			// kemudian kirim ke channel c
			c <- fmt.Sprintf("%s_%d", msgPrefix, i)
		}

	}()
	// Return channel ke pemanggil
	// sehingga bisa digunakan pemanggil untuk menerima 
	// data dari goroutine
	return c
}

func main() {
	// panggil func `produsen` dan simpan channel hasil
	// ke `ch`
	ch := produsen("mystring", 5)
	
	// terima data dari produsen dengan for-loop ke
	// channel hasil
	for msg := range ch {
		fmt.Printf("msg = %v\n", msg)
	}
}

```

playground: [https://play.golang.org/p/rdmMwMzpQMS](https://play.golang.org/p/rdmMwMzpQMS)



