# tsuru-plugins

This repo contains the following plugins:

- [app-address](#app-address) - Display addresses of a given app
- [env-list](#env-list) - List environment variables of a given app
- [goreplay](#goreplay) - Sniff the network interface and replay requests on
  another app


## app-address

Display all the addresses of a given app:

```
»»»» tsuru app-address -a foo-bar
foo-bar.cloud.domain.com
foo-bar.baz.domain.com
foo-bar.baz.globo.com
```

For one address only:

```
»»»» tsuru app-address -a foo-bar -o
foo-bar.baz.globo.com
```

Or, if you need the *IP* address:

```
»»»» tsuru app-address -a foo-bar -i
foo-bar.cloud.domain.com
```


## env-list

List all the environment variables that were set (via `env-set`) for a given
app:

```
»»»» tsuru env-list -a foo-bar
BAR_ASYNC_TIMEOUT=359
LOG_LEVEL=DEBUG
MONGO_DATABASE=*** (private variable)
MONGO_PASSWORD=*** (private variable)
MONGO_USER=*** (private variable)
NODE_ENV=production
USE_PROXY=true
```

Display private variables too:

```
»»»» tsuru env-list -a foo-bar -p
BAR_ASYNC_TIMEOUT=359
LOG_LEVEL=DEBUG
MONGO_DATABASE=my_database
MONGO_PASSWORD=xptoxpto
MONGO_USER=my_user
NODE_ENV=production
USE_PROXY=true
```

Or specific ones:

```
»»»» tsuru env-list -a foo-bar -p MONGO_DATABASE MONGO_PASSWORD MONGO_USER
MONGO_DATABASE=my_database
MONGO_PASSWORD=xptoxpto
MONGO_USER=my_user
```


## goreplay

Sniff the network interface and replay requests on another app.

Display installation instructions:

```
»»»» tsuru goreplay install
To install, add the following to your tsuru.yaml and deploy your app:
hooks:
    build:
        - 'sudo apt-get -y install screen'
        - 'curl -sfSL -o goreplay.tar.gz https://github.com/buger/goreplay/releases/download/v0.16.1/gor_0.16.1_x64.tar.gz'
        - 'tar xvf goreplay.tar.gz'
```

Replay **ALL** (!CAUTION!) requests received by app `foo-bar-prod` on another
app (e.g. foo-bar-qa):

```
»»»» tsuru goreplay start -a foo-bar-prod '--output-http http://foo-bar-qa.domain.com'
Starting...
Done!
```

Replay 5% of requests:

```
»»»» tsuru goreplay start -a foo-bar-prod '--output-http "http://foo-bar-qa.domain.com|5%"'
Starting...
Done!
```

Replay 5% of `GET` requests:

```
»»»» tsuru goreplay start -a foo-bar-prod '--output-http "http://foo-bar-qa.domain.com|5%" --http-allow-method GET'
Starting...
Done!
```

Replay 5% of `GET` requests on `/foo/bar`:

```
»»»» tsuru goreplay start -a foo-bar-prod '--output-http "http://foo-bar-qa.domain.com|5%" --http-allow-method GET --http-allow-url /foo/bar'
Starting...
Done!
```

Once you're done, stop it:

```
»»»» tsuru goreplay stop -a foo-bar-prod
Stopping...
Done!
```

For GoReplay options:

```
»»»» tsuru goreplay options -a foo-bar-prod
Gor is a simple http traffic replication tool written in Go. Its main goal is to replay traffic from production servers to staging and dev environments.
Project page: https://github.com/buger/gor
Author: <Leonid Bugaev> leonsbox@gmail.com
Current Version: 0.16.1

  -cpuprofile string
    	write cpu profile to file
  -debug verbose
    	Turn on debug output, shows all intercepted traffic. Works only when with verbose flag
  -exit-after duration
    	exit after specified duration
  -http-allow-header value
    	A regexp to match a specific header against. Requests with non-matching headers will be dropped:
	 gor --input-raw :8080 --output-http staging.com --http-allow-header api-version:^v1 (default [])
  -http-allow-method value
    	Whitelist of HTTP methods to replay. Anything else will be dropped:
	gor --input-raw :8080 --output-http staging.com --http-allow-method GET --http-allow-method OPTIONS (default [])
  -http-allow-url value
    	A regexp to match requests against. Filter get matched against full url with domain. Anything else will be dropped:
	 gor --input-raw :8080 --output-http staging.com --http-allow-url ^www. (default [])

[snip]
```

Check GoReplay docs on https://github.com/buger/goreplay/wiki
