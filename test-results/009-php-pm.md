Dodatkowy wariant testu wykonany z wykorzystaniem php-pm <https://github.com/php-pm/php-pm>.

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
| nginx + php-pm      | 0.00%        | 19.759s    | 5061.00  |

Uwagi:
- php-pm w wersjach w oficjalnych obrazach dockerhub korzysta z bardzo starej wersji php, wymagane było przebudowanie
- wynik deklasyfikuje poprzednie podejscia z mod\_php i php-fpm 

# Surowe wyniki testów

## nginx + php-pm

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


Server Software:        nginx/1.20.2
Server Hostname:        127.0.0.1
Server Port:            8080

Document Path:          /benchmark/helloworld
Document Length:        Variable

Concurrency Level:      10
Time taken for tests:   19.857 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      20402600 bytes
HTML transferred:       1300000 bytes
Requests per second:    5036.02 [#/sec] (mean)
Time per request:       1.986 [ms] (mean)
Time per request:       0.199 [ms] (mean, across all concurrent requests)
Transfer rate:          1003.40 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:     1    2   1.2      2      50
Waiting:        1    2   1.2      2      50
Total:          1    2   1.2      2      50

Percentage of the requests served within a certain time (ms)
  50%      2
  66%      2
  75%      2
  80%      2
  90%      2
  95%      2
  98%      3
  99%      3
 100%     50 (longest request)
```

