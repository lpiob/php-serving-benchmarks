Test wykonany na commitcie: `3a1e76da45ed0eaeafccd249a966612e895ebffe`

W repozytoriach dwa docker-compose ze stockowymi obrazami, bez zadnych customizacji:
- php:7-apache
- nginx:latest + php:7-fpm

Konfiguracja jest całkowicie domyślna, wyniki na pewno będzie można tuningować, co też będzie robione w kolejnych pomiarach.
Ten został wykonany jako poc i dla referencji do przyszłych testów.

Zmiany w stosunku do poprzedniej iteracji:
- kod testowy został wymieniony z prostego `phpinfo();` na *symfony hello world* przygotowany przez <http://www.phpbenchmarks.com/en/>. Instruckcja instalacji dodana w folderze `/code/`.

# Sposób testowania
```
ab -l -n 100000 -c 10 http://127.0.0.1:8080/benchmark/helloworld
```

100k requestów wysyłanych w 10 wątkach.
Parametr `-l` odpowiada za akceptowanie o różnych długościach (obecny kod zwraca output różny o kilka bajtów i bez tej flagi ab traktuje to jako błąd).
Założeniem testu i dokonywanych ustawień jest, aby stopa błędów zawsze wynosiła <0.01%

# Podsumowanie testów

| Sposób serwowania   | Stopa błędów | Czas testu | rps      |
| -----------------   | ------------ | ---------- | ---      |
| apache2 + mod\_php  | 0.00%        | 444.684s   | 224.88   |
| nginx + fpm         | 0.00%        | 573.518s   | 174.36   |

Uwagi:
- test referencyjny, wykonany na stacji roboczej posiadającej uruchomione inne aplikacje, należy traktować go tylko jako pomiar przygotowawczy
- podczas testu włączyły się wszystkie możliwe wiatraki

# Surowe wyniki testów

## apache2

```
-> ab -l -n 100000 -c 10 http://127.0.0.1:8080/benchmark/helloworld
This is ApacheBench, Version 2.3 <$Revision: 1879490 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 127.0.0.1 (be patient)
Completed 10000 requests
Completed 20000 requests
Completed 30000 requests
Completed 40000 requests
Completed 50000 requests
Completed 60000 requests
Completed 70000 requests
Completed 80000 requests
Completed 90000 requests
Completed 100000 requests
Finished 100000 requests


Server Software:        Apache/2.4.53
Server Hostname:        127.0.0.1
Server Port:            8080

Document Path:          /benchmark/helloworld
Document Length:        Variable

Concurrency Level:      10
Time taken for tests:   444.684 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      30100000 bytes
HTML transferred:       1300000 bytes
Requests per second:    224.88 [#/sec] (mean)
Time per request:       44.468 [ms] (mean)
Time per request:       4.447 [ms] (mean, across all concurrent requests)
Transfer rate:          66.10 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.1      0       8
Processing:    24   44   7.8     44     170
Waiting:       24   44   7.8     44     170
Total:         24   44   7.9     44     170

Percentage of the requests served within a certain time (ms)
  50%     44
  66%     46
  75%     47
  80%     48
  90%     52
  95%     57
  98%     65
  99%     73
 100%    170 (longest request)

```

## nginx + fpm

```
-> ab -l -n 100000 -c 10 http://127.0.0.1:8080/benchmark/helloworld
This is ApacheBench, Version 2.3 <$Revision: 1879490 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 127.0.0.1 (be patient)
Completed 10000 requests
Completed 20000 requests
Completed 30000 requests
Completed 40000 requests
Completed 50000 requests
Completed 60000 requests
Completed 70000 requests
Completed 80000 requests
Completed 90000 requests
Completed 100000 requests
Finished 100000 requests


Server Software:        nginx/1.21.6
Server Hostname:        127.0.0.1
Server Port:            8080

Document Path:          /benchmark/helloworld
Document Length:        Variable

Concurrency Level:      10
Time taken for tests:   573.518 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      27100000 bytes
HTML transferred:       1300000 bytes
Requests per second:    174.36 [#/sec] (mean)
Time per request:       57.352 [ms] (mean)
Time per request:       5.735 [ms] (mean, across all concurrent requests)
Transfer rate:          46.14 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       6
Processing:    23   57   8.7     55     117
Waiting:       23   57   8.7     55     117
Total:         23   57   8.7     55     117

Percentage of the requests served within a certain time (ms)
  50%     55
  66%     58
  75%     60
  80%     62
  90%     70
  95%     76
  98%     82
  99%     84
 100%    117 (longest request)
```
