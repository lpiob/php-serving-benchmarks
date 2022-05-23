Test wykonany na commitcie: `0e621e905a72901200f59369580346137d93f43d`

Tak jak php-fpm mozna ustawic na sztywno na ilosc procesow, tak samo mozna zrobic w mpm prefork w apache2. Ten test zostal wykonany po takiej samej zmianie. Ilosc procesow obslugujacych requesty zostala ustawiona - podobnie jak w przypadku php-fpm - na 16.

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
| apache2 + mod\_php  | 0.00%        | 244.248s   | 409.42   |
| nginx + fpm         | 0.00%        | 230.051s   | 434.69   |

Uwagi:
- Brak znaczacego wplywu na wynik testu
- Domyslne ustawienia apache2 (sprzed zmiany) byly wystarczajce do obslugi ruchu

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
Time taken for tests:   244.248 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      30100000 bytes
HTML transferred:       1300000 bytes
Requests per second:    409.42 [#/sec] (mean)
Time per request:       24.425 [ms] (mean)
Time per request:       2.442 [ms] (mean, across all concurrent requests)
Transfer rate:          120.35 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:    19   24   3.1     25     117
Waiting:       19   24   3.1     25     117
Total:         19   24   3.1     25     117

Percentage of the requests served within a certain time (ms)
  50%     25
  66%     26
  75%     27
  80%     28
  90%     28
  95%     28
  98%     29
  99%     30
 100%    117 (longest request)
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
Time taken for tests:   230.051 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      27100000 bytes
HTML transferred:       1300000 bytes
Requests per second:    434.69 [#/sec] (mean)
Time per request:       23.005 [ms] (mean)
Time per request:       2.301 [ms] (mean, across all concurrent requests)
Transfer rate:          115.04 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:    18   23   2.6     23     111
Waiting:       18   23   2.6     23     111
Total:         18   23   2.6     23     111

Percentage of the requests served within a certain time (ms)
  50%     23
  66%     24
  75%     25
  80%     26
  90%     26
  95%     27
  98%     27
  99%     28
 100%    111 (longest request)

```
