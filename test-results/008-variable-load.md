Test wykonany na commitcie: `a188608895b55b2284c4caf70eb9d53b7810020e`.

Test jest powtorzeniem ostatniego testu, ale z roznymi ustawieniami dotyczacymi intensywnosci.

# Sposób testowania
```
ab -l -n 100000 -c <C> http://127.0.0.1:8080/benchmark/helloworld
```

100k requestów wysyłanych w <C> wątkach.
Parametr `-l` odpowiada za akceptowanie o różnych długościach (obecny kod zwraca output różny o kilka bajtów i bez tej flagi ab traktuje to jako błąd).
Założeniem testu i dokonywanych ustawień jest, aby stopa błędów zawsze wynosiła <0.01%

# Podsumowanie testów

| Sposób serwowania   | Ilość wątków (<C>) | Stopa błędów | Czas testu | rps      |
| -----------------   | ------------------ | ------------ | ---------- | ---      |
| apache2 + mod\_php  | 5                  | 0.00%        | 429.523s   | 232.82   |
| nginx + fpm         | 5                  | 0.00%        | 408.541s   | 244.77   |
| apache2 + fpm       | 5                  | 0.00%        | 415.104s   | 240.90   |
| apache2 + mod\_php  | 10                 | 0.00%        | 249.534s   | 400.75   | 
| nginx + fpm         | 10                 | 0.00%        | 237.590s   | 420.89   | 
| apache2 + fpm       | 10                 | 0.00%        | 241.659s   | 413.81   | 
| apache2 + mod\_php  | 16                 | 0.00%        | 201.165s   | 497.10   | 
| nginx + fpm         | 16                 | 0.00%        | 189.677s   | 527.21   | 
| apache2 + fpm       | 16                 | 0.00%        | 193.269s   | 517.41   |
| apache2 + mod\_php  | 24                 | 0.00%        | 194.595s   | 513.89   |
| nginx + fpm         | 24                 | 0.00%        | 181.822s   | 549.99   |
| apache2 + fpm       | 24                 | 0.00%        | 182.564s   | 547.75   |
| apache2 + mod\_php  | 32                 | 0.00%        | 191.703s   | 521.64   |
| nginx + fpm         | 32                 | 0.00%        | 180.954s   | 552.63   |
| apache2 + fpm       | 32                 | 0.00%        | 182.049s   | 549.30   |

Uwagi:
- wykresy w pliku `008-graphs.png`
