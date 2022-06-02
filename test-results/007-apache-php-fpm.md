Test wykonany na commitcie: `a188608895b55b2284c4caf70eb9d53b7810020e`.

Dla pelnego porownania przygotowany zostal trzeci wariant testu - apache2+php-fpm. Ostateczna lista wariantow to:
- apache2 + mod-php
- nginx + php-fpm
- apache2 + php-fpm

Dodatkowo, przejscie z mod-php na php-fpm pozwolilo na zmianę workera w Apache2 z prefork (wymaganego dla apache2) na prefork.

Niniejszy dokument prezentuje ponowne przetestowanie apache2 + mod-php i nginx + php-fpm z poprzednimi parametrami, oraz zestawienie nowego wariantu apache2 + php-fpm.

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
| apache2 + mod\_php  | 0.00%        | 249.938s   | 400.10   |
| nginx + fpm         | 0.00%        | 237.620s   | 420.84   |
| apache2 + fpm       | 0.00%        | 242.096s   | 413.06   |

Uwagi:
- apache2 z fpm dziala minimalnie (+3%) szybciej niż apache2 z mod\_php
- wszystkie 3 wyniki są w zakresie 20rps
- kolejne testy powinny być wykonywane z większą intensywnością aby uwypuklić różnice

# Surowe wyniki testów

## apache2 + mod\_php

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
Time taken for tests:   249.938 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      30100000 bytes
HTML transferred:       1300000 bytes
Requests per second:    400.10 [#/sec] (mean)
Time per request:       24.994 [ms] (mean)
Time per request:       2.499 [ms] (mean, across all concurrent requests)
Transfer rate:          117.61 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:    20   25   3.1     25      37
Waiting:       20   25   3.1     25      37
Total:         20   25   3.1     25      37

Percentage of the requests served within a certain time (ms)
  50%     25
  66%     27
  75%     28
  80%     28
  90%     29
  95%     29
  98%     30
  99%     30
 100%     37 (longest request)
```

## nginx + php-fpm

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
Time taken for tests:   237.620 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      27100000 bytes
HTML transferred:       1300000 bytes
Requests per second:    420.84 [#/sec] (mean)
Time per request:       23.762 [ms] (mean)
Time per request:       2.376 [ms] (mean, across all concurrent requests)
Transfer rate:          111.37 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:    19   24   2.8     24     231
Waiting:       19   24   2.8     24     231
Total:         19   24   2.8     24     231

Percentage of the requests served within a certain time (ms)
  50%     24
  66%     25
  75%     26
  80%     26
  90%     27
  95%     28
  98%     28
  99%     29
 100%    231 (longest request)
```

## apache2 + php-fpm

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
Time taken for tests:   242.096 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      27900000 bytes
HTML transferred:       1300000 bytes
Requests per second:    413.06 [#/sec] (mean)
Time per request:       24.210 [ms] (mean)
Time per request:       2.421 [ms] (mean, across all concurrent requests)
Transfer rate:          112.54 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:    19   24   2.7     24      77
Waiting:       19   24   2.7     24      77
Total:         19   24   2.7     24      77

Percentage of the requests served within a certain time (ms)
  50%     24
  66%     26
  75%     27
  80%     27
  90%     28
  95%     28
  98%     29
  99%     29
 100%     77 (longest request)


```
