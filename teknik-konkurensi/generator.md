---
description: Func yang me-return channel
---

# Generator

Generator adalah pattern dimana suatu `func` melakukan hal-hal berikut:

* membuat `goroutine` yang melakukan suatu komputasi 
* me-return `channel` untuk menampung hasil komputasi tersebut

Kemudian channel yang di-return  dapat digunakan oleh pemanggil `func` untuk menerima value yang dihasilkan oleh `goroutine`tersebut.

ref: [https://talks.golang.org/2012/concurrency.slide\#25](https://talks.golang.org/2012/concurrency.slide#25)

#### contoh

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

#### Contoh Penggunaan

Contoh penggunaan dalam dunia nyata adalah dengan memodifikasi `func` dalam goroutine untuk melakukan hal-hal sebagai berikut:

* melakukan operasi database secara berulang-ulang, misalkan `select` 20 rows sebanyak 20 kali
* memanggil REST API yang menerapkan pagination beberapa kali

Dengan melakukan hal-hal tersebut didalam `goroutine`, pemanggil dapat melakukan pemrosesan lebih lanjut bersamaan dengan goroutine melakukan pekerjaannya. 





