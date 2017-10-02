# `zap` :zap:

[![GitHub issues](https://img.shields.io/github/issues/oldaniel/zap.svg)](https://github.com/oldaniel/zap/issues)

The mission of `zap` is, to deliver a fast rust web framework that just supports the basics.

## About

**This code is based on tokio's minihttp project, so a big thanks to them.** ([source](https://github.com/tokio-rs/tokio-minihttp))

## How to use

Add the following to your `Cargo.toml`:
```toml
[dependencies]
zap = { git = "https://github.com/oldaniel/zap" }
```

## Speed

So `zap` is not only fast, it is wapping **x2,7** times faster than iron which is based on hyper. Benchmarks below:

### Benchmark Code

**Iron**

```rust
extern crate iron;

use iron::prelude::*;
use iron::status;

fn main() {
    fn hello_world(_: &mut Request) -> IronResult<Response> {
        Ok(Response::with((status::Ok, "Hello World!")))
    }

    let _server = Iron::new(hello_world).http("localhost:3000").unwrap();
    println!("On 3000");
}
```

**Zap**
```
extern crate zap;

use std::io;

use zap::{Server, Http, Handler, ZapResult, Request, Response};

struct HelloWorld;

impl Handler for HelloWorld {
    type Request = Request;
    type Response = Response;
    type Error = io::Error;
    type Future = ZapResult;

    fn call(&self, req: Request) -> ZapResult {
        let mut resp = Response::new();

        resp.body("Hello World!").ok()
    }
}

fn main() {
    let addr = "0.0.0.0:8080".parse().unwrap();
    let mut server = Server::new(Http, addr);
    server.threads(8);
    server.serve(|| Ok(HelloWorld));
}
```

### Benchmark Results

**Iron**

```
Running 10s test @ http://127.0.0.1:8080
  16 threads and 500 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   214.85us  622.27us  78.67ms   99.24%
    Req/Sec    77.20k    51.31k  191.91k    51.98%
  Latency Distribution
     50%  183.00us
     75%  219.00us
     90%  304.00us
     99%  746.00us
  3103506 requests in 10.09s, 337.41MB read
Requests/sec: 307581.17
Transfer/sec:     33.44MB
```

**Zap**

```
Running 10s test @ http://127.0.0.1:8080
  16 threads and 500 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     0.99ms    1.67ms  46.05ms   90.30%
    Req/Sec    53.85k    15.70k  110.61k    71.73%
  Latency Distribution
     50%  401.00us
     75%    1.27ms
     90%    2.61ms
     99%    6.66ms
  8622335 requests in 10.09s, 559.16MB read
Requests/sec: 854783.80
Transfer/sec:     55.43MB
```

## Credist & License

A project by [Daniel Oltmanns](https://github.com/oldaniel).

[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/oltmannsdaniel/zap/master/LICENSE)
