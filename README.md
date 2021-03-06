# MT5 to ZuluTrade

This lib aims to provide an wrapper to [ZuluTrade's REST API](https://www.zulutrade.com/restapi-reference) in MetaTrader5, while native support still isn't added. This is a work in progress and while this version is fully functional, it for sure could use some sugar to make it easier to use. So far I rely on generic `GetAttribute` and `SetAttribute` functions for most of the work, and the response payload is made as a `JSON` by the `CAJVal` class implementation. There shouldn't be a problem to obtain the necessary data and in the correct format by following ZuluTrade's examples of responses.

## Dependencies

Both dependencies are included, and they are [`jason`](https://www.mql5.com/en/code/13663) and a slightly modified version of [`Base64`](https://www.mql5.com/en/articles/359). I don't know the licenses to those codes, however since they are both available publicly it should not be a problem to redistribute them.

## Usage

### Initialization

At the top of your EA, include:

```
#include <CZuluTradeAPI.mqh>
```

Then initialize the `CZuluTradeAPI` instance with your ZuluTrade account and password:

```
CZuluTradeAPI ztapi( zulutrade_account , zulutrade_password );
```
This is necessary to generate the `Authorization` header in every request made to ZuluTrade. All requests are instances of `CZuluTradeAPIRequest` and its attributes are set by versions of `CZuluTradeAPIRequest::SetAttribute`.

Requests are sent using `CZuluTradeAPI::SendRequest`, which wraps the request data with user info. It also needs a `CZuluTradeAPIResponse` so it can return the response properly.

### Open Market

Opens a market trade:

```
CZuluTradeAPIResponse response;
CZuluTradeAPIRequest req_open_market(ZT_OPEN_MARKET);
req_open_market.SetAttribute(ZT_CURRENCY_NAME,"EURUSD");
req_open_market.SetAttribute(ZT_BUY,true);
req_open_market.SetAttribute(ZT_LOTS,0.1);
req_open_market.SetAttribute(ZT_UNIQUE_ID,"OPEN MARKET EURUSD 15468464");
ztapi.SendRequest(req_open_market,response);
```

### Open Pending

Opens a pending order:

```
CZuluTradeAPIResponse response;
CZuluTradeAPIRequest req_open_pending(ZT_OPEN_PENDING);
req_open_pending.SetAttribute(ZT_CURRENCY_NAME,"EURUSD");
req_open_pending.SetAttribute(ZT_BUY,true);
req_open_pending.SetAttribute(ZT_LOTS,0.1);
req_open_pending.SetAttribute(ZT_REQUESTED_PRICE,2.5);
req_open_pending.SetAttribute(ZT_UNIQUE_ID,"OPEN PENDING EURUSD 15468464");
ztapi.SendRequest(req_open_pending,response);
```

### Close Market

Closes a market trade based on `providerTicket` and `brokerTicket`:

```
CZuluTradeAPIResponse response;
CZuluTradeAPIRequest req_close_market(ZT_CLOSE_MARKET);
req_close_market.SetAttribute(ZT_CURRENCY_NAME,"EURUSD");
req_close_market.SetAttribute(ZT_BUY,true);
req_close_market.SetAttribute(ZT_LOTS,0.1);
req_close_market.SetAttribute(ZT_BROKER_TICKET,"BROKERTICKET");
req_close_market.SetAttribute(ZT_PROVIDER_TICKET,"PROVIDERTICKET");
ztapi.SendRequest(req_close_market,response);
```

### Close Pending

Cancels a pending order based on `providerTicket` and `brokerTicket`:

```
CZuluTradeAPIResponse response;
CZuluTradeAPIRequest req_close_pending(ZT_CLOSE_PENDING);
req_close_pending.SetAttribute(ZT_CURRENCY_NAME,"EURUSD");
req_close_pending.SetAttribute(ZT_BUY,true);
req_close_pending.SetAttribute(ZT_LOTS,0.1);
req_close_pending.SetAttribute(ZT_BROKER_TICKET,"BROKERTICKET");
req_close_pending.SetAttribute(ZT_PROVIDER_TICKET,"PROVIDERTICKET");
ztapi.SendRequest(req_close_pending,response);
```

### Update Stop

Updates the stop value of a pending order/market trade based on `providerTicket` and `brokerTicket`. Notice that `stopValue` should be in pips.

```
CZuluTradeAPIResponse response;
CZuluTradeAPIRequest req_update_stop(ZT_UPDATE_STOP);
req_update_stop.SetAttribute(ZT_CURRENCY_NAME,"EURUSD");
req_update_stop.SetAttribute(ZT_BUY,true);
req_update_stop.SetAttribute(ZT_PROVIDER_TICKET ,"PROVIDERTICKET");
req_update_stop.SetAttribute(ZT_BROKER_TICKET,"BROKERTICKET");
req_update_stop.SetAttribute(ZT_STOP_VALUE,50);
ztapi.SendRequest(req_update_stop,response);
```

### Update Limit

Updates the limit value of a pending order/market trade based on `providerTicket` and `brokerTicket`.

```
CZuluTradeAPIResponse response;
CZuluTradeAPIRequest req_update_limit(ZT_UPDATE_LIMIT);
req_update_limit.SetAttribute(ZT_CURRENCY_NAME,"EURUSD");
req_update_limit.SetAttribute(ZT_BUY,true);
req_update_limit.SetAttribute(ZT_PROVIDER_TICKET ,"PROVIDERTICKET");
req_update_limit.SetAttribute(ZT_BROKER_TICKET,"BROKERTICKET");
req_update_limit.SetAttribute(ZT_LIMIT_VALUE,1.5);
ztapi.SendRequest(req_update_limit,response);
```

### Update Entry

Updates the entry of a pending order based on `providerTicket` and `brokerTicket`.

```
CZuluTradeAPIResponse response;
CZuluTradeAPIRequest req_update_entry(ZT_UPDATE_ENTRY);
req_update_entry.SetAttribute(ZT_CURRENCY_NAME,"EURUSD");
req_update_entry.SetAttribute(ZT_BUY,true);
req_update_entry.SetAttribute(ZT_PROVIDER_TICKET ,"PROVIDERTICKET");
req_update_entry.SetAttribute(ZT_BROKER_TICKET,"BROKERTICKET");
req_update_entry.SetAttribute(ZT_ENTRY_VALUE,1.5);
ztapi.SendRequest(req_update_entry,response);
```

### Update Trailing Stop

Updates the trailing stop of a pending order based on `providerTicket` and `brokerTicket`. Notice that `trailingStopValue` should be in pips.

```
CZuluTradeAPIResponse response;
CZuluTradeAPIRequest req_update_trailing_stop(ZT_UPDATE_TRAILING_STOP);
req_update_trailing_stop.SetAttribute(ZT_CURRENCY_NAME,"EURUSD");
req_update_trailing_stop.SetAttribute(ZT_BUY,true);
req_update_trailing_stop.SetAttribute(ZT_PROVIDER_TICKET ,"PROVIDERTICKET");
req_update_trailing_stop.SetAttribute(ZT_BROKER_TICKET,"BROKERTICKET");
req_update_trailing_stop.SetAttribute(ZT_TRAILING_STOP_VALUE,50);
ztapi.SendRequest(req_update_trailing_stop,response);
```

### Update Conditional Trailing Stop

Updates the trailing stop of a pending order based on `providerTicket` and `brokerTicket`. Notice that `trailingStopValue` and `trailingStopConditionPipsValue` should be in pips.

```
CZuluTradeAPIResponse response;
CZuluTradeAPIRequest req_update_conditional_trailing_stop(ZT_UPDATE_CONDITIONAL_STOP);
req_update_conditional_trailing_stop.SetAttribute(ZT_CURRENCY_NAME,"EURUSD");
req_update_conditional_trailing_stop.SetAttribute(ZT_BUY,true);
req_update_conditional_trailing_stop.SetAttribute(ZT_PROVIDER_TICKET ,"PROVIDERTICKET");
req_update_conditional_trailing_stop.SetAttribute(ZT_BROKER_TICKET,"BROKERTICKET");
req_update_conditional_trailing_stop.SetAttribute(ZT_TRAILING_STOP_VALUE,50);
req_update_conditional_trailing_stop.SetAttribute(ZT_TRAILING_STOP_CONDITIONAL_VALUE,50);
ztapi.SendRequest(req_update_conditional_trailing_stop,response);
```

### Get Open Trades

Gets the open trades of a user

```
CZuluTradeAPIResponse response;
CZuluTradeAPIRequest req_get_open_trades(ZT_GET_OPEN_TRADES);
ztapi.SendRequest(req_get_open_trades,response);
```

### Get Historic Trades/Orders

Gets trades/pending orders that got closed between the given dates. Notice that `startDate` and `endDate` should be MQL `datetime`

```
CZuluTradeAPIResponse response;
CZuluTradeAPIRequest req_get_historic_trades_orders(ZT_GET_HISTORIC_TRADES_ORDERS);
req_get_historic_trades_orders.SetAttribute(ZT_START_DATE,D"2015.06.08 00:00");
req_get_historic_trades_orders.SetAttribute(ZT_END_DATE,TimeCurrent());
ztapi.SendRequest(req_get_historic_trades_orders,response);
```

### Get Provider Statistics

Gets the provider's performance page statistics, if the user is a provider

```
CZuluTradeAPIResponse response;
CZuluTradeAPIRequest req_get_provider_statistics(ZT_GET_PROVIDER_STATISTICS);
ztapi.SendRequest(req_get_provider_statistics,response);
```

### Get Trading Condiguration

Gets the minimum amount and minimum amount step available for trading of a user.

```
CZuluTradeAPIResponse response;
CZuluTradeAPIRequest req_get_provider_statistics(ZT_GET_TRADING_CONFIGURATION);
ztapi.SendRequest(req_get_provider_statistics,response);
```

### Response

Every response is an instance of `CZuluTradeAPIResponse`, holding its status code and a `CJAVal` with the request payload. Use the `To{Int,Dbl,Str,Bool}` to fetch its values. As an example, say Get Trading Configuration were performed:

```
CZuluTradeAPIResponse response;
CZuluTradeAPIRequest req_get_provider_statistics(ZT_GET_TRADING_CONFIGURATION);
ztapi.SendRequest(req_get_provider_statistics,response);
/*
Example response from ZuluTrade:
{
         "success":    true,
         "minAmount":    0.01,
         "minAmountStep":    0.02
}
*/
Print(response.GetPayload()["minAmount"].ToDbl()); // 0.01
Print(response.GetPayload()["minAmountStep"].ToDbl()); // 0.02
```


## CONTRIBUTING

Please don't hesitate to report issues, pull requests or send me an e-mail at `altisiviero@gmail.com`

## LICENSE

Code is licensed under the [LGPLv3](LICENSE)
