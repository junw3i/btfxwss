BTFXWSS[![Build Status](https://travis-ci.org/nlsdfnbch/btfxwss.svg?branch=release-1.1.2)](https://travis-ci.org/nlsdfnbch/btfxwss)
=======

Client for Bitfinex Websocket API written in Python

Currently supports all public endpoints; authenticated channels are a
work in progress, but are supported.

Offers graceful exception handling of common server errors. Make sure you
check the log messages and have proper logging enabled, as there are no
exceptions thrown.

Data is stored within `BtfxWss` as `Queue`s. There are convenience
methods available to retrieve a queue for a given type. Consult
the code for more documentation.

Please note that you must take care of handling data in the queues yourself!
Not doing so will eventually result in `MemoryError`s, since the queues
do not have a maximum length defined.

Installation
============

Via pip:
```
pip install btfxwss
```

Usage
=====

```python
    import logging

    import time

    from btfxwss import BtfxWss
    
    log = logging.getLogger(__name__)

    fh = logging.FileHandler('test.log')
    fh.setLevel(logging.DEBUG)
    sh = logging.StreamHandler(sys.stdout)
    sh.setLevel(logging.DEBUG)

    log.addHandler(sh)
    log.addHandler(fh)
    logging.basicConfig(level=logging.DEBUG, handlers=[fh, sh])
    
    wss = BtfxWss()
    wss.start()

    while not wss.conn.connected.is_set():
        time.sleep(1)
    
    # Subscribe to some channels
    wss.subscribe_to_ticker('BTCUSD')
    wss.subscribe_to_order_book('BTCUSD')

    # Do something else
    t = time.time()
    while time.time() - t < 10:
        pass

    # Accessing data stored in BtfxWss:
    ticker_q = wss.tickers('BTCUSD')  # returns a Queue object for the pair.
    while not ticker_q.empty():
        print(ticker_q.get())

    # Unsubscribing from channels:
    wss.unsubscribe_from_ticker('BTCUSD')
    wss.unsubscribe_from_order_book('BTCUSD')

    # Shutting down the client:
    wss.stop()
```



