Test wykonany na commitcie: `307fd9bcd601dd4839d070d7c68575bb7f1f6178`.

Zmianie ulegla konfiguracja nginx i php-fpm. Wczesniej połączenie pomiędzy nimi było realizowane za pomocą TCP, w tej wersji jest realizowane za pomocą socketu.

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
| apache2 + mod\_php  | 0.00%        | 243.816s   | 410.15   |
| nginx + fpm         | 0.00%        | 362.846s   | 275.60   |

Uwagi:
- Roznica w wyniku nginx+fpm minimalna, prawdopodobnie wynika z optymalizacji ktore stosuje kernel przy połączeniach lokalnych TCP.

# Surowe wyniki testów

## apache2

```
[ec2-user@ip-172-31-37-54 ~]$ ab -l -n 100000 -c 10 http://127.0.0.1:8080/benchmark/helloworld
This is ApacheBench, Version 2.3 <$Revision: 1901567 $>
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


Server Software:        Apache/2.4.54
Server Hostname:        127.0.0.1
Server Port:            8080

Document Path:          /benchmark/helloworld
Document Length:        Variable

Concurrency Level:      10
Time taken for tests:   243.816 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      30100000 bytes
HTML transferred:       1300000 bytes
Requests per second:    410.15 [#/sec] (mean)
Time per request:       24.382 [ms] (mean)
Time per request:       2.438 [ms] (mean, across all concurrent requests)
Transfer rate:          120.56 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:    19   24   3.2     25     118
Waiting:       19   24   3.2     25     118
Total:         19   24   3.2     25     118

Percentage of the requests served within a certain time (ms)
  50%     25
  66%     26
  75%     27
  80%     28
  90%     28
  95%     28
  98%     30
  99%     30
 100%    118 (longest request)
```

## nginx + fpm

```
[ec2-user@ip-172-31-37-54 ~]$ ab -l -n 100000 -c 10 http://127.0.0.1:8080/benchmark/helloworld
This is ApacheBench, Version 2.3 <$Revision: 1901567 $>
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


Server Software:        nginx/1.23.2
Server Hostname:        127.0.0.1
Server Port:            8080

Document Path:          /benchmark/helloworld
Document Length:        Variable

Concurrency Level:      10
Time taken for tests:   362.846 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      27100000 bytes
HTML transferred:       1300000 bytes
Requests per second:    275.60 [#/sec] (mean)
Time per request:       36.285 [ms] (mean)
Time per request:       3.628 [ms] (mean, across all concurrent requests)
Transfer rate:          72.94 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:    18   36   2.1     36      91
Waiting:       18   36   2.1     36      91
Total:         18   36   2.1     36      91

Percentage of the requests served within a certain time (ms)
  50%     36
  66%     36
  75%     36
  80%     36
  90%     37
  95%     37
  98%     37
  99%     39
 100%     91 (longest request)
```
