Test wykonany na commitcie: `307fd9bcd601dd4839d070d7c68575bb7f1f6178`.

Zmianie ulegla konfiguracja php-fpm. Process manager został przełączony z domyślnego `dynamic` na `static`. Ustawiono na sztywno `max_children` na ilość równą ilości dostępnych procesorów (16) i przekraczającą ilość waktów ab.

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
| apache2 + mod\_php  | 0.00%        | 242.683s   | 412.06   |
| nginx + fpm         | 0.00%        | 229.974s   | 434.83   |

Uwagi:
- Przepustowosc php-fpm drastycznie wzrosla, przekraczająć ilość requestów na sekundę obsługiwaną dotychczas przez apache2.

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
Time taken for tests:   242.683 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      30100000 bytes
HTML transferred:       1300000 bytes
Requests per second:    412.06 [#/sec] (mean)
Time per request:       24.268 [ms] (mean)
Time per request:       2.427 [ms] (mean, across all concurrent requests)
Transfer rate:          121.12 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:    19   24   3.0     24     116
Waiting:       19   24   3.0     24     116
Total:         19   24   3.1     24     116

Percentage of the requests served within a certain time (ms)
  50%     24
  66%     26
  75%     27
  80%     27
  90%     28
  95%     28
  98%     29
  99%     29
 100%    116 (longest request)
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
Time taken for tests:   229.974 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      27100000 bytes
HTML transferred:       1300000 bytes
Requests per second:    434.83 [#/sec] (mean)
Time per request:       22.997 [ms] (mean)
Time per request:       2.300 [ms] (mean, across all concurrent requests)
Transfer rate:          115.08 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:    18   23   2.6     23      57
Waiting:       18   23   2.6     23      57
Total:         18   23   2.6     23      57

Percentage of the requests served within a certain time (ms)
  50%     23
  66%     24
  75%     25
  80%     26
  90%     26
  95%     27
  98%     28
  99%     28
 100%     57 (longest request)
```
