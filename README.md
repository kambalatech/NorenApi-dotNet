# NorenApi-dotnet

[INTRODUCTION](#md_introduction)

[Login and Session](#md_session)
- [Login](#md_login)
- [UserDetails](#md_userdetails)
- [ForgotPassword](#md_forgot)
- [ChangePassword](#md_changepwd)
- [Logout](#md_logout)
- [SetSession](#md_setsession)

[WatchLists](#md_watchlist)
- [UserDetails](#md_userdetails)
- [GetWatchLists](#md_getwatchlist)
- [AddScriptoWatchList](#md_addscripwatchlist)
- [DeleteScriptoWatchList](#_TOC_250031)

[Market](#md_market)
- [SearchScrips](#md_searchscrips)
- [GetSecurityInfo](#md_securityinfo)
- [GetQuote](#_TOC_250012)
- [GetTimePriceData(Chartdata)](#md_tpseries)
- [GetOptionChain](#_TOC_250007)
- [GetIndexList](#_TOC_250011)
- [ExchMsg](#_TOC_250019)

[Orders and Trades](#md_ordersntrades)
- [PlaceOrder](#_TOC_250028)
- [ModifyOrder](#_TOC_250027)
- [CancelOrder](#_TOC_250026)
- [ExitSNOOrder](#_TOC_250025)
- [ProductConversion](#_TOC_250016)
- [OrderMargin](#_TOC_250024)
- [OrderBook](#_TOC_250023)
- [TradeBook](#_TOC_250020)
- [SingleOrderHistory](#_TOC_250021)
- [MultiLegOrderBook](#_TOC_250022)
- [BasketOrderMargin](#_TOC_250024)
- [PositionsBook](#_TOC_250017)
- [Holdings](#_TOC_250014)
- [Limits](#_TOC_250013)

[Order and MarketData Update](#_TOC_250006)

- [Connect](#_TOC_250005)
- [SubscribeMarketData](#_TOC_250004)
- [UnSubscribeMarketData](#_TOC_250003)
- [SubscribeOrderUpdate](#_TOC_250000)


# History

| Date | Version | Changes | Details |
| --- | --- | --- | --- |
| 05-01-2021 | 1.1.0.0 | matches pyapi | pyapi v0.0.15 |
| 15-11-2021 | 1.0.11.0 | SearchScrips | search text is encoded for M&M etc  |
| 19-04-2021 | 1.0.0.1 | TouchlineBroker | TouchlineFeedadded  |
| 01-01-2021 | 1.0.0.0 | InitialRelease | Based on NorenRestAPI v1.10.0 |

# <a name="md_introduction"></a> INTRODUCTION: About the API

The Api is a dotNet wrapper of the NorenAPI which offers a combination of Rest calls and WebSocket for the purposes of Trading.

API is developed on VisualStudio2019 and uses .NetStandard 2.0 
The dependency libraries are 
  Newtonsoft.Json  9.0.1  
  
The namespace NorenRestApiWrapper and class NorenRestApi are of primary use and interest

### Initialize

To initialize the api the following are needed 

endPoint: The api end point as instructed by your Broker
Appkey  : The secretkey issued to you, donot append the userid to it.

### Making Requests

We will be creating an object of NorenRestApi to make requests, the callback is an argument of the request method.

```
LoginMessage loginMessage = new LoginMessage();
loginMessage.uid = uid;
loginMessage.pwd = pwd;
loginMessage.factor2 = pan;
loginMessage.imei = "134243434";
loginMessage.source = "API";
loginMessage.appkey = appkey;

nApi.SendLogin(Program.OnAppLoginResponse, endPoint, loginMessage);
```

In the above example we are sending the Loginrequest,this method takes three arguments

1. Callback: this is the function where the application will be handling the response
2. Endpoint: NorenOMS address
3. MessageData: parameters of the request being made.

The Callback is of signature

 ###### public delegate void OnResponse(NorenResponseMsg Response,bool ok)

A Typical callback will be handled as below

```
public static voidOnAppLoginResponse(NorenResponseMsg Response, bool ok)
{
   LoginResponse loginResp= Response as LoginResponse;

   if(loginResp.stat=="Ok")
   {
       //do all work here
   }
}
```

The Response is casted to expected DataType ie in this example being LoginResponse, stat is checked to see if the request was successful.

# <a name="md_session"></a> Login and Session

##  <a name="md_login"></a> Login

###### public bool SendLogin(OnResponse response,string endPoint,LoginMessage login)
connect to the broker, only once this function has returned successfully can any other operations be performed

##### RequestDetails: As Arguments
```
    LoginMessage loginMessage = new LoginMessage();
    loginMessage.apkversion = "1.0.0";
    loginMessage.uid = uid;
    loginMessage.pwd = pwd;
    loginMessage.factor2 = factor2;
    loginMessage.imei = imei;
    loginMessage.vc = vc;
    loginMessage.source = "API";
    loginMessage.appkey = appkey;
    nApi.SendLogin(Handlers.OnAppLoginResponse, endPoint, loginMessage);
```
##### ResponseDetails:LoginResponse
| Json Fields| Possible value| Description| 
| --- | --- | --- |
| stat  |  Ok or Not_Ok | Login Success Or failure status | 
| susertoken  |   | session id, avilable subsequently on login success with method UserToken | 
| lastaccesstime  |   | lastaccesstime | 
| spasswordreset  | Y  | If Y Mandatory password reset to be enforced. Otherwise the field will be absent. | 
| exarr  |   | array of strings with enabled exchange names | 
| uname  |   | User name | 
| prarr  |   | array of Product Obj with enabled products, as defined below | 
| actid  |   | Account Id | 
| email  |   | Email Id | 
| brkid  |   | Broker Id | 
| emsg  |   | This will be present only if Login fails.(Redirect to force change password if message is “Invalid Input : Password Expired” or “Invalid Input : Change Password”) | 


## <a name="md_userdetails"></a> UserDetails

###### public bool SendGetUserDetails(OnResponse response)

##### RequestDetails:NoParams
##### ResponseDetails:UserDetailsResponse
| Json Fields| Possible value| Description| 
| --- | --- | --- |
| exarr  |   | list of exchanges enabled | 
| orarr  |   | ordertypes enabled for the user| 
| prarr  |   | list of product object | 
| brkname |  | Region Category | 
| brnchid  |   |  Branch Category | 
| actid  |   | Account Id | 
| email  |   | Email Id | 


## <a name="md_logout"></a> Logout

###### public bool SendLogout(OnResponse response)
Closes the session opened with the server.

##### RequestDetails:NoParams
```
    napi.SendLogout();
```
##### ResponseDetails:LogoutResponse
| Json Fields| Possible value| Description| 
| --- | --- | --- |
| stat  |  Ok or Not_Ok | Login Success Or failure status | 

## <a name="md_forgot"></a> ForgotPassword

###### public bool SendForgotPassword(OnResponse response,string endpoint,string user,string pan,string dob)

##### RequestDetails: As Arguments
| Json Fields| Possible value| Description| 
| --- | --- | --- |
| uid* |   | User Id | 
| pan* |   | pan of the user |
| dob* |   | Date of birth |

##### ResponseDetails:ForgotPasswordResponse
| Json Fields| Possible value| Description| 
| --- | --- | --- |
| stat  |  Ok or Not_Ok | Success Or failure status | 
| emsg  |   | This will be present only if request fails. |

## <a name="md_changepwd"></a> ChangePassword

###### public bool Changepwd(OnResponse response,Changepwd changepwd)

##### RequestDetails:Changepwd
| Json Fields| Possible value| Description| 
| --- | --- | --- |
| uid* |   | User Id | 
| oldpwd* |   | old password of the user |
| pwd* |   | new password of the user |

##### ResponseDetails:ChangepwdResponse
| Json Fields| Possible value| Description| 
| --- | --- | --- |
| stat  |  Ok or Not_Ok | Success Or failure status | 
| emsg  |   | This will be present only if request fails. |

## <a name="md_setsession"></a> SetSession
This method initializes the api with an existing session instead of creating a new session with SendLogin.

###### public bool SetSession(string endpoint, string uid, string pwd, string usertoken)

##### RequestDetails:
       endpoint : server endpoint
       uid      : user     
       pwd      : password
       usertoken: session id from loginresponse. 

##### ResponseDetails:True/False


# <a name="md_watchlist"></a> WatchLists

## <a name="md_getwatchlistnames"></a> GetWatchListNames

###### public boolSendGetMWList(OnResponseresponse)

##### Request Details : No Params
##### ResponseDetails:MWListResponse 


## <a name="md_getwatchlist"></a> GetWatchList

###### public bool SendGetMarketWatch(OnResponse response,string wlname)

##### RequestDetails:NoParams
##### ResponseDetails:MarketWatchResponse

##  <a name="md_addscripwatchlist"></a> AddScriptoWatchList

###### public bool SendAddMultiScripsToMW(OnResponse response,string watchlist,string scrips)

##### RequestDetails: As Arguments
##### ResponseDetails:StandardResponse

## DeleteScriptoWatchList

###### public bool SendDeleteMultiMWScrips( OnResponse response,string watchlist,string  scrips)

##### RequestDetails: As Arguments
##### ResponseDetails:StandardResponse

## <a name="md_searchscrips"></a>  SearchScrips

###### public bool SendSearchScrip(OnResponse response,string exch,string searchtxt)

The call can be made to get the exchange provided token for a scrip or alternately can search for a partial string to get a list of matching scrips

Multiple criteria can be specified for the search with space

Trading Symbol:
- SymbolName + ExpDate + 'F' for all data having InstrumentName starting with FUT
- SymbolName + ExpDate + 'P' + StrikePrice for all data having InstrumentName starting with OPT and with OptionType PE
- SymbolName + ExpDate + 'C' + StrikePrice for all data having InstrumentName starting with OPT and with OptionType C
- For MCX, F to be ignored for FUT instruments

###### Request
```
api.SendSearchScrip(Program.OnResponse, 'NSE', 'REL');
```
###### ResponseDetails:SearchScripResponse

list of a ScripItem, which is as follows,

| Json Fields| Possible value| Description| 
| --- | --- | --- |
| exch | | Exchange |
| tsym | | Trading Symbol |
| token | | Exchange specified Token  |
| pp | | Price Precision |
| ti | | Tick Size |
| ls | | Lot Size |

## <a name="md_securityinfo"></a> GetSecurityInfo

###### public bool SendGetSecurityInfo( OnResponse response,string exch,string token)

##### RequestDetails:

| Json Fields| Possible value| Description| 
| --- | --- | --- |
| exch | | Exchange |
| token | | Exchange specified Token  |

##### ResponseDetails:GetSecurityInfoResponse
| Param | Type | Optional |Description |
| --- | --- | --- | ---|
| stat | ```string``` | True | ok or Not_ok |
| values | ```string``` | True | properties of the scrip |
| emsg | ```string``` | False | Error Message |

| Param | Type | Optional |Description |
| --- | --- | --- | ---|
| exch | ```string``` | True | Exchange NSE  / NFO / BSE / CDS |
| tsym | ```string``` | True | Trading Symbol is the readable Unique id of contract/scrip |
| cname| ```string``` | True | Company Name |
| symnam| ```string``` | True | Symbol Name  |
| seg| ```string``` | True | Segment |
| exd| ```string``` | True | Expiry Date |
| instname| ```string``` | True | Instrument Name |
| strprc| ```string``` | True | Strike Price |
| optt| ```string``` | True | Option Type |
| isin| ```string``` | True | ISIN |
| ti | ```string``` | True | Tick Size |
| ls| ```string``` | True | Lot Size |
| pp| ```string``` | True | Price Precision |
| mult| ```string``` | True | Multiplier |
| gp_nd| ```string``` | True | GN/GD * PN/PD  |
| prcunt| ```string``` | True | Price Units |
| prcqqty| ```string``` | True | Price Quote Qty |
| trdunt| ```string``` | True | Trade Units |
| delunt| ```string``` | True | Delivery Units |
| frzqty| ```string``` | True | Freeze Qty |
| gsmind| ```string``` | True | GSM indicator |
| elmbmrg| ```string``` | True | ELM Buy Margin |
| elmsmrg| ```string``` | True | ELM Sell Margin |
| addbmrg| ```string``` | True | Additional Long Margin |
| addsmrg| ```string``` | True | Additional Short Margin |
| splbmrg| ```string``` | True | Special Long Margin |
| splsmrg| ```string``` | True | Special Short Margin |
| delmrg| ```string``` | True | Delivery Margin |
| tenmrg| ```string``` | True | Tender Margin |
| tenstrd| ```string``` | True | Tender Start Date  |
| tenendd| ```string``` | True | Tender End Date |
| exestrd| ```string``` | True | Exercise Start Date |
| exeendd| ```string``` | True | Exercise End Date |
| elmmrg| ```string``` | True | ELM Margin |
| varmrg| ```string``` | True | VAR Margin |
| expmrg| ```string``` | True | Exposure Margin |
| token| ```string``` | True | Contract Token |
| prcftr_d| ```string``` | True | ((GN / GD) * (PN/PD)) |


# Order and Trades

## PlaceOrder

###### public bool SendPlaceOrder( OnResponse response,PlaceOrder order  )

##### RequestDetails:PlaceOrder
Place an order as follows
```
    PlaceOrder order = new PlaceOrder();
    order.uid = uid;
    order.actid = actid;
    order.exch = "NSE";
    order.tsym = "M&M-EQ";
    order.qty = "10";
    order.dscqty = "0";
    order.prc = "100.5";
    order.prd = "I";
    order.trantype = "B";
    order.prctyp = "LMT";
    order.ret = "DAY";
    order.ordersource = "API";

    nApi.SendPlaceOrder(Handlers.OnResponseNOP, order);
```
Place a Cover Order as follows
```
    PlaceOrder order = new PlaceOrder();
    order.uid = uid;
    order.actid = actid;
    order.exch = "CDS";
    order.tsym = "USDINR27JAN21F";
    order.qty = "10";
    order.dscqty = "0";
    order.prc = "76.0025";
    order.blprc = "74.0025";
    order.prd = "H";
    order.trantype = "B";
    order.prctyp = "LMT";
    order.ret = "DAY";
    order.ordersource = "API";

    nApi.SendPlaceOrder(Handlers.OnResponseNOP, order);
```
Place a Bracket  Order as follows
```
    PlaceOrder order = new PlaceOrder();
    order.uid = uid;
    order.actid = actid;
    order.exch = "NSE";
    order.tsym = "INFY-EQ";
    order.qty = "10";
    order.dscqty = "0";
    order.prc = "2800";
    order.blprc = "2780";
    order.bpprc = "2820";
    order.prd = "B";
    order.trantype = "B";
    order.prctyp = "LMT";
    order.ret = "DAY";
    order.ordersource = "API";

    nApi.SendPlaceOrder(Handlers.OnResponseNOP, order);
```
##### ResponseDetails:PlaceOrderResponse
This is an acknowledgement of the order received by OMS.  NorenOrdNo is the unique identifier for the same.

```
    public class PlaceOrderResponse : NorenResponseMsg
    {
        public string request_time;
        public string norenordno;
    }
```


## ModifyOrder

###### bool SendModifyOrder( OnResponse response,ModifyOrder order  )
To Modify an order, use the OrderNumber returned in place order, you can only modify an open order(Status: New). 

##### RequestDetails:ModifyOrder
```
    ModifyOrder order = new ModifyOrder();
    order.norenordno = ordno;
    order.exch = "NSE";
    order.tsym = "M&M-EQ";
    order.qty = "15";
    order.prc = "100.5";
    order.prd = "I";    
    order.prctyp = "LMT";
    order.ret = "DAY";
    
    nApi.SendModifyOrder(Handlers.OnResponseNOP, order);
```
##### ResponseDetails:ModifyOrderResponse
an acknowlegment is returned
```
    public class ModifyOrderResponse : NorenResponseMsg
    {
        public string request_time;
        public string norenordno;
    }
```
## CancelOrder

###### public bool SendCancelOrder( OnResponse response,string norenordno)
To cancel an order, send the OrderNumber returned by  PlaceOrder

##### RequestDetails:
```
    nApi.SendCancelOrder(Handlers.OnResponseNOP, order);
```

##### ResponseDetails:CancelOrderResponse
an acknowlegment is returned
```
    public class CancelOrderResponse : NorenResponseMsg
    {
        public string request_time;
        public string norenordno;
    }
```
## ExitSNOOrder
###### public bool SendExitSNOOrder( OnResponse response,string norenordno,string product)

##### RequestDetails:
```
    public class ExitSNOOrder : NorenMessage
    {
        public string norenordno;
        public string prd;
        public string uid;
    }
```
##### ResponseDetails:ExitSNOOrderResponse
an acknowledgement is returned

## OrderMargin

###### public bool SendGetOrderMargin( OnResponse response,OrderMargin ordermargin)

##### RequestDetails:OrderMargin
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

##### ResponseDetails:OrderMarginResponse

| Param | Type | Optional |Description |
| --- | --- | --- | ---|

## OrderBook

###### public bool SendGetOrderBook( OnResponse response,string product)

##### RequestDetails:
| Param | Type | Optional |Description |
| --- | --- | --- | ---|
|  No Parameters  |

##### ResponseDetails:OrderBookResponselistofOrderBookItem

## MultiLegOrderBook

###### public bool SendGetMultiLegOrderBook( OnResponse response,string product)

##### RequestDetails:
| Param | Type | Optional |Description |
| --- | --- | --- | ---|
|  No Parameters  |

##### ResponseDetails: MultiLegOrderBookResponse - list of MultiLegOrderBookItem
list of MultiLegOrderBookItem

## SingleOrderHistory

###### public bool SendGetOrderHistory( OnResponse response,string norenordno)

##### RequestDetails:
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

##### ResponseDetails: list of SingleOrdHistItem


## TradeBook

###### public bool SendGetTradeBook( OnResponse response,string account)

##### RequestDetails:
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

##### ResponseDetails: TradeBookResponse - list of TradeBookItem

## ExchMsg

###### public bool SendGetExchMsg( OnResponse response,ExchMsg exchmsg)

##### RequestDetails:ExchMsg
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

##### ResponseDetails:ExchMsgResponselistofExchMsgItem
| Param | Type | Optional |Description |
| --- | --- | --- | ---|


## PositionsBook

###### public bool SendGetPositionBook( OnResponse response,string account)

##### RequestDetails:
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

##### ResponseDetails: PositionBookResponse - list of PositionBookItem which is as follows,

| Param | Type | Optional |Description |
| --- | --- | --- | ---|
|stat| ```string``` | False |Position book success or failure indication.|
|exch| ```string``` | False |Exchange segment|
|tsym| ```string``` | False |Trading symbol / contract.|
|token| ```string``` | False |Contract Token|
|uid| ```string``` | False |User Id|
|actid|```string``` | False | Account Id|
|prd| ```string``` | False | Product name|
|netqty| ```string``` | False | Net Position Quantity|
|netavgprc| ```string``` | False | Net Position Average Price|
|daybuyqty| ```string``` | False | Day Buy Quantity|
|daysellqty| ```string``` | False | Day Sell Quantity|
|daybuyavgprc| ```string``` | False | Day Buy Average Price|
|daysellavgprc| ```string``` | False | Day Sell Average Price|
|daybuyamt| ```string``` | False | Day Buy Amount|
|daysellamt| ```string``` | False | Day Sell Amount|
|cfbuyqty| ```string``` | False | Carry Forward Sell Quantity|
|cforgavgprc| ```string``` | False | Original Average Price|
|cfsellqty| ```string``` | False | Carry Forward Sell Quantity|
|cfbuyavgprc| ```string``` | False | Carry Forward Buy Average Price|
|cfsellavgprc| ```string``` | False | Carry Forward Sell Average Price|
|cfbuyamt| ```string``` | False | Carry Forward Buy Amount|
|cfsellamt| ```string``` | False | Carry Forward Sell Amount|
|lp| ```string``` | False | LTP|
|rpnl| ```string``` | False | Realized Profit and Loss|
|urmtom| ```string``` | False | UnRealized Mark To Market (Can be recalculated in LTP update : = netqty * (lp from web socket - netavgprc) * prcftr |
|bep| ```string``` | False | Breakeven Price|
|openbuyqty| ```string``` | False | Open Buy Order Quantity |
|opensellqty| ```string``` | False | Open Sell Order Quantity |
|openbuyamt| ```string``` | False | Open Buy Order Amount |
|opensellamt| ```string``` | False | Open Sell Order Amount|
|openbuyavgprc| ```string``` | False ||
|opensellavgprc| ```string``` | False ||
|mult| ```string``` | False ||
|pp| ```string``` | False ||
|prcftr| ```string``` | False ||
|ti| ```string``` | False ||
|ls| ```string``` | False ||
|request_time| ```string``` | False ||

## ProductConversion

###### public bool SendProductConversion( OnResponse response,ProductConversion prdConv)

##### RequestDetails: ProductConversion
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

##### ResponseDetails: ProductConversionResponse
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

# Holdings and Limits

## Holdings

###### public bool SendGetHoldings( OnResponse response,string account,string product)

##### RequestDetails:
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

##### ResponseDetails:HoldingsResponselistofHoldingsItem
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

## Limits

###### public bool SendGetLimits(OnResponse response,string account,string product = "", string segment = "";  string exchange = "")

##### RequestDetails:
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

##### ResponseDetails:LimitsResponse
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

# MarketInfo

## GetIndexList

##### RequestDetails:
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

##### ResponseDetails:
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

## GetTopListNames

##### RequestDetails:
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

##### ResponseDetails:
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

## GetTopList

##### RequestDetails:
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

##### ResponseDetails:
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

##  <a name="md_tpseries"></a> GetTimePriceData /ChartData

##### RequestDetails: public bool SendGetTPSeries(OnResponse response, string exch, string token, string starttime = null, string endtime = null, string interval = null)
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

##### ResponseDetails: list of TPSeriesItem
| Param | Type | Optional |Description |
| --- | --- | --- | ---|

## GetOptionChain
gets the chart date for the symbol

##### RequestDetails:
| Param | Type | Optional |Description |
| --- | --- | --- | ---|
| exchange | ```string``` | True | Exchange NSE  / NFO / BSE / CDS |
| token | ```string``` | True | token number of the contract|
| starttime | ```string``` | True | Start time (seconds since 1 jan 1970) |
| endtime | ```string``` | True | End Time (seconds since 1 jan 1970)|
| interval | ```integer``` | True | Candle size in minutes (1,3,5,10,15,30,60,120,240)|

##### ResponseDetails: the response is as follows,

| Param | Type | Optional |Description |
| --- | --- | --- | ---|
| stat | ```string``` | True | ok or Not_ok |
| values | ```string``` | True | properties of the scrip |
| emsg | ```string``` | False | Error Message |

| Param | Type | Optional |Description |
| --- | --- | --- | ---|
| time | ```string``` | True | DD/MM/CCYY hh:mm:ss |
| into | ```string``` | True | Interval Open |
| inth | ```string``` | True | Interval High |
| intl | ```string``` | True | Interval Low  |
| intc | ```string``` | True | Interval Close  |
| intvwap | ```string``` | True | Interval vwap  |
| intv | ```string``` | True | Interval volume  |
| v | ```string``` | True | volume  |
| inoi | ```string``` | True | Interval oi change  |
| oi | ```string``` | True | oi  |

# OrderUpdates and MarketDataUpdate

This Api allows you to receive updates receivethe marketdata and order updates in the application callbacks as an option, to do so connect as follows.

Api.OnFeedCallback  += Application.OnFeedHandler;
Api.OnOrderCallback += Application.OnOrderHandler;

## Connect

###### public bool ConnectWatcher(string uri,OnFeed marketdata Handler,OnOrderFeed orderHandler)
starts the websocket, WebSocket feed has 2 types of ticks( t=touchline d=depth)and 2 stages (k=acknowledgement, f=further change in tick). 


## SubscribeMarketData

###### public bool SubscribeToken(string exch,string token)
t='tk' is sent once on subscription for each instrument. this will have all the fields with the most recent value
thereon t='tf' is sent for fields that have changed.
```
For example
quote event: 03-12-2021 11:54:44{'t': 'tk', 'e': 'NSE', 'tk': '11630', 'ts': 'NTPC-EQ', 'pp': '2', 'ls': '1', 'ti': '0.05', 'lp': '118.55', 'h': '118.65', 'l': '118.10', 'ap': '118.39', 'v': '162220', 'bp1': '118.45', 'sp1': '118.50', 'bq1': '26', 'sq1': '6325'}
quote event: 03-12-2021 11:54:45{'t': 'tf', 'e': 'NSE', 'tk': '11630', 'lp': '118.45', 'ap': '118.40', 'v': '166637', 'sp1': '118.55', 'bq1': '3135', 'sq1': '30'}
quote event: 03-12-2021 11:54:46{'t': 'tf', 'e': 'NSE', 'tk': '11630', 'lp': '118.60'}
```
in the example above we see first message t='tk' with all the values, 2nd message has lasttradeprice avg price and few other fields with value changed.. note bp1 isnt sent as its still 118.45
in the next tick ( 3rd message) only last price is changed to 118.6

##### Request:
|Fields |Possible  value| Description |
| --- | --- | --- |
| exch | NSE,BSE,NFO... | Exchange |
| token |
| ScripToken |

##### MarketDataUpdates:

Accept for t, e,and tk other fields may/may not be present.

|Fields |Possible  value| Description |
| --- | --- | --- |
| t | tf | tf represents touchline feed |
| e | NSE,BSE,NFO.. | Exchangename |
| tk | 22 | ScripToken |
| lp || LTP |
| pc || Percentagechange |
| v || volume |
| o || Openprice |
| h || Highprice |
| l || Lowprice |
| c || Closeprice |
| ap || Averagetradeprice |
| oi || Open interest |
| poi || Previous day closing Open Interest |
| to1 || Total open interest for underlying |
| bq1 || Best Buy Quantity 1 |
| bp1 || Best Buy Price 1 |
| sq1 || Best Sell Quantity 1 |
| sp1 || Best Sell Price 1 |

## UnSubscribeMarketData

###### public bool UnSubscribeToken(string exch,string token)

##### Request:

|Fields |Possible  value| Description |
| --- | --- | --- |
| exch | NSE,BSE,NFO... | Exchange |
| token || ScripToken |

## SubscribeOrderUpdate

This is auto subscribed by the api

##### Order Updates : NorenOrderFeed

|Fields |Possible  value| Description |
| --- | --- | --- |
| t | om | "om" represents touchlinefeed |
| norenordno | | NorenOrderNumber |
| uid | | UserId |
| actid | | AccountID |
| exch | | Exchange |
| tsym | |  | | |  T |radingsymbol |
| qty | | Orderquantity |
| prc | | OrderPrice |
| prd | | Product |
| status | | Orderstatus(open,complete,rejectedetc) |
| reporttype | | Ordereventforwhichthismessageissentout.(fill,rejected,canceled) |
| trantype | | Ordertransactiontype,buyorsell |
| prctyp | | Orderpricetype(LMT,MKT,SL-LMT,SL-MKT) |
| ret | | Orderretentiontype(DAY,EOS,IOC,...) |
| fillshares | | Filledshares |
| avgprc | | Averagefillprice |
| rejreason | | Orderrejectionreason,ifrejected |
| exchordid | | ExchangeOrderID |
| cancelqty | | Canceledquantity,incaseofcanceledorder |
| remarks | | Useraddedtag,whileplacingorder |
| dscqty | | Disclosedquantity |
| trgprc | | TriggerpriceforSLorders |
