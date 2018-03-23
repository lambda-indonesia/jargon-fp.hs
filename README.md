# Jargon-FP.HS

Jargon-jargon pemrograman fungsional serta contoh implementasinya di Haskell.

## Daftar Jargon

- [Arity](#arity)
- [Currying](#currying)
- [Higher Order Function](#higher-order-function)

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

Suatu proses yang mengambil *argument* fungsi secara "terpisah". Maksudnya,
semua *argument* yang dibutuhkan oleh suatu fungsi tidak diambil secara
langsung atau bersamaan.

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

Proses tersebut dinamakan **currying**.

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
kita berikan hanya satu *argument*. Ini merujuk ke (Currying)[#currying].

Karena dalam fungsi `map'` *argument* pertamanya adalah `f`, yang mana
merupakan sebuah fungsi, maka anggap saja `f = add 1`. Karena di dalam fungsi
`map'` `f` diberikan satu *argument*, yaitu `x`, maka `f` akan menjadi
`f x = add 1` dan dapat ditulis menjadi `f = add 1 x`. Sekarang, terlihat
bahwa `add` sudah diberikan dua *argument* di dalam fungsi `map'`.

Terlihat jelas bahwasannya kita melakukan *passing* fungsi (sebagai *argument*)
ke fungsi yang lain. Untuk penjelasan mengenai pengembalian fungsi yang berupa
fungsi juga, silakan lihat (Currying)[#currying].

## Pemelihara

- [wisn](https://github.com/wisn)

## Lisensi

Penjelasan mengenai lisensi (berbahasa Inggris) ada di [LICENSE](LICENSE).