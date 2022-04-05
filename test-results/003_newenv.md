Test wykonany na commitcie: `d177e0a03b03c7947dc1892a12a287d51e947f43`, identycznym w konfiguracji i kodzie jak poprzedni test.

Zmianie uleglo srodowisko uruchamiania testow - z racji duzego generowania obciazenia, przygotowala zostala instancja AWS typu c6i.4xlarge, posiadająca 16CPU i 32GB RAM - zasoby z zalozenia zdecydowanie zbyt duże na generowane obciazenie (10 watkow w ab), ale dzieki przewymiarowaniu eliminujemy ewentualne waskie gardlo w sprzecie.

Test byl wykonywany na instancji Amazon Linux 2 prowizjonowanej terraformem wraz z instalacja docker, docker-compose, apache bench. Zastosowanie IaC pozwoli na osiagniecie maksymalnej powtarzalnosci testu oraz umozliwi sledzenie ewentualnych zmian (np. powiekszenie instancji).

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
| apache2 + mod\_php  | 0.00%        | 245.686s   | 407.02   |
| nginx + fpm         | 0.00%        | 364.361s   | 274.45   |

Uwagi:
- Zgodnie z oczekiwaniami, wykonanie testu na przygotowanej instancji testu zauwazalnie skrocilo czas trwania i zwiekszylo ilosc requestow na sekunde
- loadavg podczas testow nie przekroczył 6, co w zestawieniu z brakiem komunikacji z zewnetrznymi komponentami i dużą ilością wolnej pamięci, potwierdza że zasobów CPU na stacji testowej nie brakuje.

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
Time taken for tests:   245.686 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      30100000 bytes
HTML transferred:       1300000 bytes
Requests per second:    407.02 [#/sec] (mean)
Time per request:       24.569 [ms] (mean)
Time per request:       2.457 [ms] (mean, across all concurrent requests)
Transfer rate:          119.64 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       2
Processing:    19   24   7.5     25    2186
Waiting:       19   24   7.5     25    2186
Total:         19   24   7.5     25    2186

Percentage of the requests served within a certain time (ms)
  50%     25
  66%     26
  75%     27
  80%     28
  90%     28
  95%     28
  98%     29
  99%     30
 100%   2186 (longest request)
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
Time taken for tests:   364.361 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      27100000 bytes
HTML transferred:       1300000 bytes
Requests per second:    274.45 [#/sec] (mean)
Time per request:       36.436 [ms] (mean)
Time per request:       3.644 [ms] (mean, across all concurrent requests)
Transfer rate:          72.63 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:    19   36   3.9     36    1148
Waiting:       19   36   3.9     36    1148
Total:         19   36   3.9     36    1148

Percentage of the requests served within a certain time (ms)
  50%     36
  66%     36
  75%     36
  80%     36
  90%     37
  95%     37
  98%     37
  99%     39
 100%   1148 (longest request)
```
