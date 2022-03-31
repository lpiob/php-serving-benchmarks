Test wykonany na commitcie: `91a5676d131eb92292b93bc7dbcfce2d964c4373`

W repozytoriach dwa docker-compose ze stockowymi obrazami, bez zadnych customizacji:
- php:7-apache
- nginx:latest + php:7-fpm

Konfiguracja jest całkowicie domyślna, wyniki na pewno będzie można tuningować, co też będzie robione w kolejnych pomiarach.
Ten został wykonany jako poc i dla referencji do przyszłych testów.

# Sposób testowania
```
ab -l -n 100000 -c 10 http://127.0.0.1:8080/
```

100k requestów wysyłanych w 10 wątkach.
Parametr `-l` odpowiada za akceptowanie o różnych długościach (obecny kod zwraca output różny o kilka bajtów i bez tej flagi ab traktuje to jako błąd).
Założeniem testu i dokonywanych ustawień jest, aby stopa błędów zawsze wynosiła <0.01%

# Podsumowanie testów

| Sposób serwowania   | Stopa błędów | Czas testu | rps      |
| -----------------   | ------------ | ---------- | ---      |
| apache2 + mod\_php  | 0.00%        | 14.058s    | 7113.58  |
| nginx + fpm         | 0.00%        | 18.314s    | 5460.24  |

Uwagi:
- test referencyjny, wykonany na stacji roboczej posiadającej uruchomione inne aplikacje, należy traktować go tylko jako pomiar przygotowawczy
- kod testowy to zwykłe `phpinfo();`. W kolejnej iteracji, jeszcze przed właściwym fine-tuningiem ustawień należy wymienic go na bardziej reprezentatywny przykład.

# Surowe wyniki testów

## apache2

```
-> ab -l -n 100000 -c 10 http://127.0.0.1:8080/ 
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

Document Path:          /
Document Length:        Variable

Concurrency Level:      10
Time taken for tests:   14.058 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      7211788609 bytes
HTML transferred:       7192188609 bytes
Requests per second:    7113.58 [#/sec] (mean)
Time per request:       1.406 [ms] (mean)
Time per request:       0.141 [ms] (mean, across all concurrent requests)
Transfer rate:          500992.72 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.1      0       2
Processing:     1    1   0.4      1      11
Waiting:        0    1   0.4      1      11
Total:          1    1   0.4      1      11

Percentage of the requests served within a certain time (ms)
  50%      1
  66%      1
  75%      2
  80%      2
  90%      2
  95%      2
  98%      2
  99%      3
 100%     11 (longest request)

```

## nginx + fpm

```
-> ab -l -n 100000 -c 10 http://127.0.0.1:8080/ 
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

Document Path:          /
Document Length:        Variable

Concurrency Level:      10
Time taken for tests:   18.314 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      7228278168 bytes
HTML transferred:       7211978168 bytes
Requests per second:    5460.24 [#/sec] (mean)
Time per request:       1.831 [ms] (mean)
Time per request:       0.183 [ms] (mean, across all concurrent requests)
Transfer rate:          385430.70 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       1
Processing:     1    2   0.5      2      26
Waiting:        1    2   0.5      1      26
Total:          1    2   0.5      2      26
WARNING: The median and mean for the waiting time are not within a normal deviation
        These results are probably not that reliable.

Percentage of the requests served within a certain time (ms)
  50%      2
  66%      2
  75%      2
  80%      2
  90%      2
  95%      3
  98%      3
  99%      3
 100%     26 (longest request)
```
