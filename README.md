# Shoonya-Websocket
Shoonya websocket based on picows : https://github.com/tarasko/picows

It will automatically handle resubscription to all tokens after a temporary network failure

N.B -> Not supported disconnection > 10 minutes for now. 


# Example waiting for connection to be properly established before subscribing to tokens
```python
import asyncio
import logging
import platform
from shoonya_ticker import ShoonyaTicker

if platform.system() == "Windows":
    import winloop
    asyncio.set_event_loop_policy(winloop.EventLoopPolicy())
else:
    import uvloop
    asyncio.set_event_loop_policy(uvloop.EventLoopPolicy())

logging.basicConfig(level=logging.DEBUG)

async def on_tick(msg):
    print(f"tick : {msg}")

async def on_order_update(msg):
    print(f"order : {msg}")

async def on_error(msg):
    print(f"error : {msg}")

async def main(
            ticker:ShoonyaTicker,
            loop: asyncio.AbstractEventLoop,
            token_list: list
            )-> None:
    
    ticker.start_websocket(
                    subscribe_callback= on_tick,
                    order_update_callback= on_order_update,
                    error_callback= on_error
                    )
    await ticker.IS_CONNECTED.wait()
    loop.call_soon(ticker.subscribe, token_list)

if __name__ == "__main__":
    username = "Your username here."
    token = "Your session token here."
    ws_endpoint = "wss://api.shoonya.com/NorenWSTP/"

    ticker = ShoonyaTicker(ws_endpoint, username, token)

    loop = asyncio.get_event_loop()
    
    tokens_list = ["BSE|1", "BSE|12", "NSE|26000", "NSE|26009", "MCX|430106", "MCX|430107"]
    
    loop.create_task(main(ticker, loop, tokens_list))
    loop.run_forever()

```

**_Special Thanks to [tarasc](https://github.com/tarasko) for this excellent library and for his valuable guidance_** 
