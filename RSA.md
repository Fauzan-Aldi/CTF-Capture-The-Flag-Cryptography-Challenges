# CARA MENYELESAIKAN MASALAH RSA

Bagi yang belum tahu cara kerja RSA, berikut adalah dasar yang perlu Anda ketahui:

1. Tentukan *p* dan *q*, keduanya harus merupakan angka prima.
2. Hitung *n = p \* q*
3. Hitung *phi* = (*p*-1) \* (*q*-1)
4. Tentukan *e*, biasanya *e* berada di antara 3 hingga 65537. Kami akan membahas tantangan dengan *e* besar dan kerentanannya nanti.
5. Tentukan *d* sebagai invers perkalian modular dari *e* modulo *phi*, Anda tidak benar-benar perlu tahu bagaimana ini bekerja jika Anda seorang pemula, cukup cari skrip yang dapat melakukannya untuk Anda.

Untuk mengenkripsi pesan *M*, lakukan:
*C* = *M*^*e* % *n*

Untuk mendekripsi ciphertext *C*, lakukan:
*M* = *C*^*d* % *n*

(*n*, *e*) adalah kunci publik, sementara (*n*, *d*) adalah kunci privat. Nilai lainnya disimpan sebagai rahasia karena dapat digunakan untuk menghitung *d*.

## TANTANGAN

Kebanyakan Tantangan RSA memberikan Anda kunci publik dan ciphertext untuk didekripsi, namun kita tidak memiliki kunci privat sehingga tidak bisa mendekripsi langsung.
Jadi, bagaimana cara mendapatkan *d* hanya dengan *n* dan *e*?

Jika Anda ingat bagaimana RSA bekerja, kita hanya perlu *p* dan *q* untuk menghitung *phi* dan menggunakan *phi* dan *e* untuk mendapatkan *d*. Jadi, cara kita bisa menemukan *p* dan *q* adalah dengan memfaktorkan *n*. Sayangnya, terkadang *n* terlalu besar untuk difaktorkan dengan cara brute force. Oleh karena itu, kita harus mencari alternatif lain.
Ada sebuah [database](www.factordb.com) yang sangat berguna yang menyimpan faktor-faktor dari sebuah angka, penulis tantangan akan sengaja menyimpan faktor-faktor *n* di dalam tantangan agar kita, para pemain, bisa menemukannya.
Namun, bagaimana jika factordb tidak dapat menemukan faktor dalam database? Itu berarti penulis tantangan menginginkan Anda untuk menemukan kelemahan lain pada angka tersebut.
[alpetron](https://www.alpertron.com.ar/ECM.HTM) adalah situs web yang sangat baik untuk membantu Anda menemukan kelemahan pada angka tersebut.

Beberapa jenis kelemahan umum pada faktor-faktor *n* adalah:

1. *p* dan *q* sama, faktor dapat ditemukan dengan mengambil akar kuadrat dari *n*.
2. Nilai *p* dan *q* terlalu dekat, ini membuat *n* = (p)(p + x), brute forcing *x* tidak memerlukan waktu yang lama.
3. Alih-alih menggunakan hanya 2 angka prima, digunakan banyak angka prima kecil, ini bisa juga difaktorkan dengan mudah.
4. Salah satu dari *p* atau *q* terlalu kecil, misalnya *p* = 3 atau *q* = 5.

Itulah konsep dasar untuk mendekripsi ciphertext menggunakan RSA dengan memulihkan kunci privat.

## BAGAIMANA JIKA PEMFAKTORAN TIDAK BERHASIL

Hal pertama yang harus Anda lakukan adalah mengidentifikasi nilai yang Anda miliki, mari kita lihat contoh ini:

```
n : 3398743732304737633847385920138274382130284237583 // ini bukan modulo yang sah  
e : 3  
c : 903659261103750 // ini juga bukan ciphertext yang sah  
```

Seperti yang Anda lihat pada contoh di atas, *c* jauh lebih kecil dari *n*, *e* juga sangat kecil, oleh karena itu ketika kita mencoba *c* = *m*^*e* % *n*, ada kemungkinan bahwa *m*^*e* sangat kecil sehingga *n* tidak digunakan sama sekali. Jadi kita bisa memulihkan pesan dengan menghitung akar kubik dari *c*.
Ada banyak variasi untuk ini, misalnya penulis bisa sengaja mengenkripsi dengan kunci privat dan memberikan Anda kunci publik. Jadi Anda bisa melakukan *c*^*e* % *n* untuk mendekripsi ciphertext. Anda bisa mengidentifikasi jenis masalah ini dengan membaca deskripsi tantangan.

## BAGAIMANA JIKA RSA TERLIHAT AGAK Aneh

* Jika *e* adalah angka besar, maka *d* bisa kecil atau tertukar dengan *e*, Anda bisa mencoba mendekripsi dengan *d* = 65537, atau melakukan [Serangan Wiener](https://en.wikipedia.org/wiki/Wiener%27s_attack).
* Jika ada *x* pesan yang sama namun dipadankan dengan padding yang sedikit berbeda, maka [Serangan Coppersmith’s short-pad](https://en.wikipedia.org/wiki/Coppersmith%27s_attack#Coppersmith%E2%80%99s_short-pad_attack) bisa dilakukan.
* Jika ada *x* pesan yang sama yang dienkripsi dengan *n* yang berbeda, jika *x* > *e*, maka [Serangan siaran Håstad](https://en.wikipedia.org/wiki/Coppersmith%27s_attack#H%C3%A5stad%27s_broadcast_attack) mungkin terjadi.
* Jika tantangan meminta Anda untuk memfaktorkan *n* dengan eksponen privat *d*, Anda bisa menggunakan [Algoritma ini](https://www.di-mgt.com.au/rsa_factorize_n.html).
* Jika ada 2 kunci publik, Anda bisa melakukan *gcd(n1, n2)* untuk mencari tahu apakah mereka memiliki faktor yang sama.

## BAGAIMANA JIKA RSA TERLIHAT NORMAL

Mungkin Anda mendapatkan kode sumber dari generator tantangan, Anda perlu mencari kerentanannya di sana, coba periksa apa saja yang Anda miliki jika setelah membaca semua hal di halaman ini dan tidak ada yang berhasil.
