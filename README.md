# Jargon-FP.HS

Jargon-jargon pemrograman fungsional serta contoh implementasinya di Haskell.

## Daftar Jargon

- [Arity](#arity)
- [Currying](#currying)
- [Higher Order Function](#higher-order-function)
- [Idempotent](#idempotent)
- [Partial Application](#partial-application)
- [Pure Function](#pure-function)
- [Side Effect](#side-effect)
- [Type Signature](#type-signature)
- [Variable](#variable)

## Arity

Banyaknya jumlah *argument* dari suatu fungsi.

### Contoh

```haskell
fib :: Int -> Int
fib 0 = 0
fib 1 = 1
fib n = fib (n - 1) + fib (n - 2)
```

Fungsi `fib` di atas memiliki satu *arity* yaitu *argument* `n`.

```haskell
add :: Num a => a -> a -> a
add x y = x + y
```

Fungsi `add` di atas memiliki dua *arity* yaitu *argument* `x` dan `y`.

## Currying

Suatu proses yang mentransformasikan fungsi dengan banyak *argument* menjadi
fungsi dengan satu *argument* saja. Apabila *argument* yang diperlukan masih
belum terpenuhi, apa yang menjadi keluaran fungsi tersebut adalah berupa
fungsi juga.

Dalam bahasa pemrograman Haskell, `f :: a -> b -> c` sebenarnya adalah
`f :: a -> (b -> c)`. Terlihat bahwasannya setiap fungsi di Haskell hanya
memiliki satu *argument*. Untuk lebih jelas, `f x y` sebenarnya diproses
menjadi `(f x) y`.

### Contoh

```haskell
-- (+) :: Num a => a -> a -> a
f = uncurry (+)
-- > f :: Num c => (c, c) -> c
g = curry f
-- > g :: Num c => c -> c -> c

main :: IO ()
main = do
  print $ f (1, 2)
-- > 3
  print $ g 1 2
-- > 3
```

Di Haskell, `f (1, 2)` merupakan fungsi dengan "dua *argument*." Kenapa
`g 1 2` disebut memiliki satu *argument*? Hal ini berhubungan dengan Lambda
Calculus. Dengan Lambda Calculus, `g 1 2` sama saja dengan
`(\x -> (\y -> x + y)) 1 2`. Terlihat untuk setiap fungsi yang ada di dalam
fungsi lambda tersebut hanya memiliki satu *argument*. Masing-masingnya yaitu
`\x` dan `\y`.

## Higher Order Function

Higher Order Function biasanya disingkat menjadi HOF.

Sebuah fungsi yang mengambil fungsi lainnya sebagai *argument* dan/atau yang
mengembalikan sebuah fungsi.

### Contoh

```haskell
add :: Num a => a -> a -> a
add x y = x + y

map' :: (a -> b) -> [a] -> [b]
map' _ []     = []
map' f (x:xs) = f x : map' f xs

main :: IO ()
main = print $ map' (add 1) [1..5]
-- > [2,3,4,5,6]
```

Fungsi pertama, `add`, mengambil dua *argument* yaitu `x` dan `y`.

Fungsi kedua, `map`, mengambil dua *argument* yaitu `f` dan `(x:xs)` dimana
`f` merupakan sebuah fungsi dan `(x:xs)` merupakan sebuah `List`.

Lalu, di dalam fungsi `main`, kita melakukan implementasi
`map' (add 1) [1..5]`. Fungsi `add` yang seharusnya diberikan dua *argument*,
kita berikan hanya satu *argument*. Ini merujuk ke
[Partial Application](#partial-application).

Karena dalam fungsi `map'` *argument* pertamanya adalah `f`, yang mana
merupakan sebuah fungsi, maka anggap saja `f = add 1`. Karena di dalam fungsi
`map'` `f` diberikan satu *argument*, yaitu `x`, maka `f` akan menjadi
`f x = add 1` dan dapat ditulis menjadi `f = add 1 x`. Sekarang, terlihat
bahwa `add` sudah diberikan dua *argument* di dalam fungsi `map'`.

Terlihat jelas bahwasannya kita melakukan *passing* fungsi (sebagai *argument*)
ke fungsi yang lain. Untuk penjelasan mengenai pengembalian fungsi yang berupa
fungsi juga, silakan lihat [Partial Application](#partial-application).

## Idempotent

Suatu fungsi dikatakan *idempotent* jika nilai kembalinya selalu sama ketika
diaplikasikan kembali ke fungsinya.

### Contoh

```haskell
f :: a -> a
f x = x

sort :: Ord a => [a] -> [a]
sort [] = []
sort (x:xs) = sort left ++ (x : sort right)
  where
    left = [y | y <- xs, y <= x]
    right = [y | y <- xs, y > x]
```

Fungsi `f` merupakan fungsi identitas yang akan selalu mengembalikan dirinya
sendiri. Maka, `f(f(f(x)))` akan selalu bernilai `x`.

Fungsi `sort` merupakan fungsi yang akan melakukan pengurutan dari nilai
terkecil ke terbesar. Anggap saja terdapat `sort([1,4,2,13,5])` yang memiliki
keluaran `[1,2,4,5,13]`. Ketika nilai ini dimasukkan kembali ke fungsi `sort`,
hasilnya tidak akan berubah. Untuk itu, `sort(sort([1,4,2,13,5]))` akan selalu
sama dengan `[1,2,4,5,13]`.

## Partial Application

Melakukan *passing argument* secara parsial. Maksudnya, apabila suatu fungsi
memiliki dua *argument*, kita melakukan *passing* satu *argument* terlebih
dahulu. Sisa *argument* yang masih diperlukan akan dilengkapi nanti.

### Contoh

```haskell
add :: Num a => a -> a -> a
add x y = x + y

add5 :: Num a => a -> a
add5 = add 5 -- dengan kata lain, `add5 z = add 5 z`

main :: IO ()
main = do
  print (add 5 10)
-- > 15
  print (add5 10)
-- > 15
```

Fungsi `add` memiliki dua *argument* yaitu `x` dan `y`. Namun, dalam fungsi
`add5`, kita hanya memberikan satu *argument* ke `add`, yaitu `5`. Fungsi
`add5` akan membawa fungsi `add 5` yang kemudian apabila `add5` diberikan
satu *argument*, anggap saja `z`, "bentuk"-nya akan menjadi `add 5 z`.

## Pure Function

Fungsi disebut *pure* ketika fungsi tersebut tidak "mengambil" pengubah
(*variable*) luar ke dalam fungsinya kecuali *argument* dari fungsi itu
sendiri. *Pure function* juga tidak melakukan aktivitas `IO` di dalamnya.

### Contoh

```haskell
addPure :: Int -> Int -> Int
addPure x y = x + y

addImpure :: Int -> Int -> IO Int
addImpure x y = do
  putStrLn "Ooopsie!"
  return (x + y)

externalVar :: Int
externalVar = 10

anotherImpureFn :: Int -> Int
anotherImpureFn x = x + externalVar

main :: IO ()
main = do
  print $ addPure 5 10
-- > 15
  impure <- addImpure 5 10
-- > Ooopsie!
  print impure
-- > 15
  print $ anotherImpureFn 5
-- > 15
```

## Side Effect

Istilah *side effect* merujuk ke suatu fungsi yang menggunakan aktivitas `IO`
di dalamnya. Hal tersebut disebut *side effect* karena pemetaan fungsi dari
domain ke kodomainnya tidak tetap. *Random number generator* juga merupakan
*side effect* karena kodomainnya selalu berubah-ubah.

### Contoh

```haskell
main :: IO ()
main = do
  putStrLn "Halo, namanya siapa?"
  putStr "Nama: "

  name <- getLine

  putStrLn $ "Halo, " ++ name ++ "!"
-- > Tergantung dari masukan pengguna. Misal, Haskell.
-- > Halo, Haskell!
```

## Type Signature

Sebuah ekspresi yang mendeklarasikan jenis-jenis tipe data dari suatu fungsi
atau pengubah.

### Contoh

```haskell
add :: Num a => a -> a -> a
note :: String
```

Pada kode di atas, `::` menunjukkan deklarasi *type signature*. Fungsi `add`
memiliki *type signature* `Num a => a -> a -> a` dimana `Num a` merupakan
sebuah *context* dan `a -> a -> a` merupakan tipe datanya. Nilai kembali atau
*return value* dari fungsi tersebut adalah tipe data yang paling kanan. Contoh,
pada `Int -> Int -> Bool`, yang menjadi *return value* adalah `Bool`.
Sementara sisanya merupakan *argument* yang diterima.

Contoh lainnya yaitu `note`. Berhubung hanya satu *type*, yaitu `String`, maka
`String` merupakan tipe datanya. `note` dapat disebut sebagai fungsi yang tidak
memiliki *argument* sehingga `String` dapat kita sebut sebagai *return value*.
`note` dapat disebut sebagai *variable* ketika ada suatu *value* yang
dipasangkan dengannya. Istilah *variable* di Haskell tidak sama dengan istilah
di bahasa pemrograman lain. Baca [Variable](#variable) untuk mengetahui lebih
jauh.

## Variable

*Variable* di Haskell merupakan sebuah nama untuk suatu ekspresi yang absah.
Sama seperti *variable* pada umumnya, operator `=` merupakan penanda yang
memasangkan nama *variable* dengan suatu *value* dimana *value* merupakan
bentuk ekspresi yang absah. Jangkauan atau *scope* dari *variable* dapat diatur
dengan menggunakan klausa `let` atau `where`.

Pengertian lain dari *variable* adalah nama dari suatu *argument* fungsi. Hal
ini merujuk ke Lambda Calculus.

### Contoh

```haskell
main :: IO ()
main = do
  let phi = 1.618
      compute x = x * phi
  in print $ compute 10
```

`phi` merupakan bentuk aplikasi dari pengertian di paragraf pertama. Sementara
`compute x`, ambil `x` saja, merupakan pengertian dari paragraf kedua.

## Pemelihara

- [wisn](https://github.com/wisn)

## Lisensi

Penjelasan mengenai lisensi (berbahasa Inggris) ada di [LICENSE](LICENSE).
