<p align="center"><a href="https://petersoj.github.io/alpaca-java/" target="_blank"><img src="https://i.imgur.com/QNzjpub.png"></a></p>
<p align="center">
<a href="https://search.maven.org/artifact/net.jacobpeterson/alpaca-java" target="_blank"><img alt="Maven Central" src="https://img.shields.io/maven-central/v/net.jacobpeterson/alpaca-java"></a> <a href="https://javadoc.io/doc/net.jacobpeterson/alpaca-java" target="_blank"><img src="https://javadoc.io/badge/net.jacobpeterson/alpaca-java.svg" alt="Javadocs"></a> <a href="https://travis-ci.com/github/Petersoj/alpaca-java" target="_blank"><img src="https://travis-ci.com/Petersoj/alpaca-java.svg?branch=master" alt="Build Status"></a> <a href="https://codecov.io/gh/petersoj/alpaca-java" target="_blank"><img src="https://codecov.io/gh/petersoj/alpaca-java/branch/unittesting/graph/badge.svg" />
</a> <a href="https://opensource.org/licenses/MIT" target="_blank"><img alt="GitHub" src="https://img.shields.io/github/license/petersoj/alpaca-java"></a>    
</p>

## Overview
This is a Java implementation for the <a href="https://alpaca.markets/">Alpaca</a> API and the <a href="https://polygon.io/">Polygon</a> API. Alpaca lets you trade with algorithms, connect with apps, and build services all with a commission-free stock trading API and Polygon provides real-time and historical market data for free. This library is community developed and if you have any questions, please ask them on [Github Discussions](https://github.com/Petersoj/alpaca-java/discussions), the [Alpaca Slack #dev-alpaca-java channel](https://alpaca.markets/slack), or on the [Alpaca Forums](https://forum.alpaca.markets/).

## Table of Contents
1. [Gradle and Maven Integration](#Gradle-and-Maven-Integration)
1. [Configuration](#Configuration)
1. [Logger](#Logger)
1. [AlpacaAPI](#AlpacaAPI)
    - [AlpacaAPIRequestException](#AlpacaAPIRequestException)
    - [Account](#Account)
    - [AccountActivity](#AccountActivity)
    - [AccountConfiguration](#AccountConfiguration)
    - [Orders](#Orders)
    - [Positions](#Positions)
    - [Assets](#Assets)
    - [Watchlist](#Watchlist)
    - [PortfolioHistory](#PortfolioHistory)
    - [Calendar](#Calendar)
    - [Clock](#Clock)
    - [Bars](#Bars)
    - [LastTrade](#LastTrade)
    - [LastQuote](#LastQuote)
    - [Alpaca Streaming](#Alpaca-Streaming)
    - [Alpaca Market Data Streaming](#Alpaca-Market-Data-Streaming)
1. [PolygonAPI](#polygonapi)
    - [PolygonAPIRequestException](#PolygonAPIRequestException)
    - [Usage](#Usage)
    - [Basic Example](#Basic-Example)
1. [Building](#building)

## Gradle and Maven Integration
If you are using Gradle as your build tool, add the following dependency to your `build.gradle` file:

```
dependencies {
    implementation group: 'net.jacobpeterson', name: 'alpaca-java', version: '6.1'
}
```

If you are using Maven as your build tool, add the following dependency to your `pom.xml` file:

```
<dependency>
    <groupId>net.jacobpeterson</groupId>
    <artifactId>alpaca-java</artifactId>
    <version>6.1</version>
    <scope>compile</scope>
</dependency>
```

Note that you don't have to use the Maven Central artifacts and instead can just install a clone of this project to your local Maven repository as shown in the [Building](#building) section.

## Configuration
Creating an `alpaca.properties` file on the classpath with the following format allows you to easily load properties using the `AlpacaAPI()` default constructor:
```
key_id = <YOUR KEY>
secret = <YOUR SECRET>
```
The default values for `alpaca.properties` can be found [here](https://github.com/Petersoj/alpaca-java/tree/master/src/main/resources).

Similarly, creating a `polygon.properties` file on the classpath with the following format allows you to easily load properties using the `PolygonAPI()` default constructor:
```
key_id = <YOUR KEY> (Note that this will default to what is set in 'alpaca.properties')
```
The default values for `polygon.properties` can be found [here](https://github.com/Petersoj/alpaca-java/tree/master/src/main/resources).

## Logger
For logging, this library uses [SLF4j](http://www.slf4j.org/) which serves as an interface for various logging frameworks. This enables you to use whatever logging framework you would like. However, if you do not add a logging framework as a dependency in your project, the console will output a message stating that SLF4j is defaulting to a no-operation (NOP) logger implementation. To enable logging, add a logging framework of your choice as a dependency to your project such as [Log4j 2](http://logging.apache.org/log4j/2.x/index.html), [SLF4j-simple](http://www.slf4j.org/manual.html), or [Apache Commons Logging](https://commons.apache.org/proper/commons-logging/).

## AlpacaAPI
The `AlpacaAPI` class contains several methods to interface with Alpaca. You will generally only need one instance of it in your application. Note that many methods allow `null` to be passed in as a parameter if it is optional. The Alpaca API specification is located [here](https://docs.alpaca.markets/api-documentation/api-v2/) and the `AlpacaAPI` Javadoc is located [here](https://javadoc.io/doc/net.jacobpeterson/alpaca-java/latest/net/jacobpeterson/alpaca/AlpacaAPI.html). 

Example usage:
```java
// This constructor uses the 'alpaca.properties' file on the classpath for configuration
AlpacaAPI alpacaAPI = new AlpacaAPI();

// This constructor passes in a 'keyID' and 'secret' String
String keyID = "<some key ID>";
String secret = "<some secret>";
AlpacaAPI alpacaAPI = new AlpacaAPI(keyID, secret);

// This constructor is for OAuth tokens
String oAuthToken = "<some OAuth token>";
AlpacaAPI alpacaAPI = new AlpacaAPI(oAuthToken);
```

Note that the example uses of the `AlpacaAPI` class methods below are not exhaustive. Refer to the `AlpacaAPI` [Javadoc](https://javadoc.io/doc/net.jacobpeterson/alpaca-java/latest/net/jacobpeterson/alpaca/AlpacaAPI.html) for all method signatures.

### AlpacaAPIRequestException
The `AlpacaAPIRequestException` is thrown anytime an exception occurs when using various `AlpacaAPI` methods. It should be caught and handled within your trading algorithm application accordingly.

### Account
The Account API serves important information related to an account, including account status, funds available for trade, funds available for withdrawal, and various flags relevant to an account's ability to trade.

Example usage:
```java
try {
    // Get 'Account' information
    Account account = alpacaAPI.getAccount();
} catch (AlpacaAPIRequestException e) {
    e.printStackTrace();
}
```

### AccountActivity
The Account Activities API provides access to a historical record of transaction activities that have impacted your account.

Example usage:
```java
try {
    // Print all 'AccountActivity's on 12/23/2020
    List<AccountActivity> accountActivities = alpacaAPI.getAccountActivities(
            null,
            null,
            ZonedDateTime.of(2020, 12, 23, 0, 0, 0, 0, ZoneId.of("America/New_York")),
            Direction.ASCENDING,
            null,
            null,
            (ActivityType[]) null);
    for (AccountActivity accountActivity : accountActivities) {
        if (accountActivity instanceof TradeActivity) {
            System.out.println("TradeActivity: " + (TradeActivity) accountActivity);
        } else if (accountActivity instanceof NonTradeActivity) {
            System.out.println("NonTradeActivity: " + (NonTradeActivity) accountActivity);
        }
    }
} catch (AlpacaAPIRequestException e) {
    e.printStackTrace();
}
```

### AccountConfiguration
The Account Configuration API provides custom configurations about your trading account settings. These configurations control various allow you to modify settings to suit your trading needs.

Example usage:
```java
try {
    // Update the 'AccountConfiguration' to block new orders
    AccountConfiguration accountConfiguration = alpacaAPI.getAccountConfiguration();
    accountConfiguration.setSuspendTrade(true);
    
    alpacaAPI.setAccountConfiguration(accountConfiguration);
} catch (AlpacaAPIRequestException e) {
    e.printStackTrace();
}
```

### Orders
The Orders API allows a user to monitor, place and cancel their orders with Alpaca. Each order has a unique identifier provided by the client. This client-side unique order ID will be automatically generated by the system if not provided by the client, and will be returned as part of the order object along with the rest of the fields described below. Once an order is placed, it can be queried using the client-side order ID to check the status. Updates on open orders at Alpaca will also be sent over the streaming interface, which is the recommended method of maintaining order state.

Example usage:
```java
try {
    // Print all the 'Order's of AAPL and TSLA after 12/23/2020 
    // in ascending (oldest to newest) order
    List<Order> orders = alpacaAPI.getOrders(
            OrderStatus.ALL,
            null,
            ZonedDateTime.of(2020, 12, 23, 0, 0, 0, 0, ZoneId.of("America/New_York")),
            null,
            Direction.ASCENDING,
            true,
            Arrays.asList("AAPL", "TSLA"));
    orders.forEach(System.out::println);
    
    // Request a new buy limit order for TSLA for 100 shares at a limit price
    // of 600.00 and TIF of DAY.
    Order limitOrderTSLA = alpacaAPI.requestNewLimitOrder(
            "TSLA",
            100,
            OrderSide.BUY,
            OrderTimeInForce.DAY,
            600.00,
            false);
    
    // Check if my TSLA limit order has filled 5 seconds later and if it hasn't
    // replace it with a market order so it fills!
    Thread.sleep(5000);
    limitOrderTSLA = alpacaAPI.getOrder(limitOrderTSLA.getId(), false);
    if (limitOrderTSLA.getFilledAt() == null) {
        Order replacedOrder = alpacaAPI.replaceOrder(
                limitOrderTSLA.getId(),
                100,
                OrderTimeInForce.DAY,
                null,
                null,
                null,
                null);
    }
    
    // Cancel all open orders
    List<CancelledOrder> cancelledOrders = alpacaAPI.cancelAllOrders();
    for (CancelledOrder cancelledOrder : cancelledOrders) {
        System.out.println("Cancelled Order: " + cancelledOrder.getOrder());
    }
} catch (AlpacaAPIRequestException e) {
    e.printStackTrace();
}
```

### Positions
The Positions API provides information about an account's current open positions. The response will include information such as cost basis, shares traded, and market value, which will be updated live as price information is updated. Once a position is closed, it will no longer be queryable through this API.

Example usage:
```java
try {
    // Cancel TSLA and AAPL positions if they are open
    List<Position> openPositions = alpacaAPI.getOpenPositions();
    for (Position openPosition : openPositions) {
        if (openPosition.getSymbol().equals("TSLA") ||
                openPosition.getSymbol().equals("AAPL")) {
            Order closePositionOrder = alpacaAPI.closePosition(openPosition.getSymbol());
            System.out.println("Closing position: " + closePositionOrder);
        }
    }
} catch (AlpacaAPIRequestException e) {
    e.printStackTrace();
}
```

### Assets
The Assets API serves as the master list of assets available for trade and data consumption from Alpaca. Assets are sorted by asset class, exchange and symbol. Some assets are only available for data consumption via Polygon, and are not tradable with Alpaca. These assets will be marked with the flag `tradable=false`.

Example usage:
```java
try {
    // Print out a CSV of all active US equity 'Asset's
    List<Asset> activeUSEquities = alpacaAPI.getAssets(AssetStatus.ACTIVE, "us_equity");
    System.out.println(activeUSEquities
            .stream().map(Asset::getSymbol)
            .collect(Collectors.joining(", ")));
    
    // Print out TSLA 'Asset' information
    Asset tslaAsset = alpacaAPI.getAssetBySymbol("TSLA");
    System.out.println(tslaAsset);
} catch (AlpacaAPIRequestException e) {
    e.printStackTrace();
}
```

### Watchlist
The Watchlist API provides CRUD operation for the account's watchlist. An account can have multiple watchlists and each is uniquely identified by ID but can also be addressed by user-defined name. Each watchlist is an ordered list of assets.

Example usage:
```java
try {
    // Print out all 'Watchlist' names
    List<Watchlist> watchlists = alpacaAPI.getWatchlists();
    watchlists.forEach(watchlist -> System.out.println(watchlist.getName()));

    // Create a watchlist for day trading with TSLA and AAPL initially
    Watchlist dayTradeWatchlist = alpacaAPI.createWatchlist("Day Trade", "TSLA", "AAPL");

    // Remove TSLA and add MSFT to 'dayTradeWatchlist'
    alpacaAPI.removeSymbolFromWatchlist(dayTradeWatchlist.getId(), "TSLA");
    alpacaAPI.addWatchlistAsset(dayTradeWatchlist.getId(), "MSFT");

    // Set the updated watchlist variable
    dayTradeWatchlist = alpacaAPI.getWatchlist(dayTradeWatchlist.getId());

    // Print CSV of 'dayTradeWatchlist' 'Asset's
    System.out.println(dayTradeWatchlist.getAssets()
            .stream().map(Asset::getSymbol)
            .collect(Collectors.joining(", ")));

    // Delete the 'dayTradeWatchlist'
    alpacaAPI.deleteWatchlist(dayTradeWatchlist.getId());
} catch (AlpacaAPIRequestException e) {
    e.printStackTrace();
}
```

### PortfolioHistory
The portfolio history API returns the timeseries data for equity and profit loss information of the account.

Example usage:
```java
try {
    // Get the 'PortfolioHistory' and print out static information
    PortfolioHistory portfolioHistory = alpacaAPI.getPortfolioHistory(
            3,
            PortfolioPeriodUnit.DAY,
            PortfolioTimeFrame.ONE_HOUR,
            LocalDate.of(2020, 12, 23),
            false);
    System.out.printf("Timeframe: %s, Base value: %s \n",
            portfolioHistory.getTimeframe(),
            portfolioHistory.getBaseValue());

    // Loop through all indices and print the dynamic historical information uniformly
    int historyUnitSize = portfolioHistory.getTimestamp().size();
    for (int historyIndex = 0; historyIndex < historyUnitSize; historyIndex++) {
        System.out.printf("Timestamp: %s, Equity: %s, PnL: %s, PnL%%: %s \n",
                portfolioHistory.getTimestamp().get(historyIndex),
                portfolioHistory.getEquity().get(historyIndex),
                portfolioHistory.getProfitLoss().get(historyIndex),
                portfolioHistory.getProfitLossPct().get(historyIndex));
    }
} catch (AlpacaAPIRequestException e) {
    e.printStackTrace();
}
```

### Calendar
The calendar API serves the full list of market days from 1970 to 2029. It can also be queried by specifying a start and/or end time to narrow down the results. In addition to the dates, the response also contains the specific open and close times for the market days, taking into account early closures.

Example usage:
```java
try {
    // Get the 'Calendar' of week of Christmas and print out information
    List<Calendar> calendar = alpacaAPI.getCalendar(
            LocalDate.of(2020, 12, 20),
            LocalDate.of(2020, 12, 27));
    calendar.forEach(System.out::println);
} catch (AlpacaAPIRequestException e) {
    e.printStackTrace();
}
```

### Clock
The clock API serves the current market timestamp, whether the market is currently open, as well as the times of the next market open and close.

Example usage:
```java
try {
    // Get the market 'Clock' and print it out
    Clock clock = alpacaAPI.getClock();
    System.out.println(clock);
} catch (AlpacaAPIRequestException e) {
    e.printStackTrace();
}
```

### Bars
The bars API provides time-aggregated price and volume data from the IEX exchange.

Example usage:
```java
try {
    // Get 15min bars of AAPL and TSLA from 12/23/2020 at 9:30 AM to
    // 12/24/2020 at 4 PM and print them out
    Map<String, ArrayList<Bar>> bars = alpacaAPI.getBars(
            BarsTimeFrame.FIFTEEN_MINUTE,
            new String[]{"AAPL", "TSLA"},
            null,
            ZonedDateTime.of(2020, 12, 23, 9, 30, 0, 0, ZoneId.of("America/New_York")),
            ZonedDateTime.of(2020, 12, 24, 12 + 4, 0, 0, 0, ZoneId.of("America/New_York")),
            null,
            null);
    bars.entrySet().forEach(System.out::println);
} catch (AlpacaAPIRequestException e) {
    e.printStackTrace();
}
```

### LastTrade
The Last Trade API provides last trade details for a symbol.

Example usage:
```java
try {
    // Print out the last trade of AAPL
    System.out.println(alpacaAPI.getLastTrade("AAPL"));
} catch (AlpacaAPIRequestException e) {
    e.printStackTrace();
}
```

### LastQuote
The Last Quote API provides last quote details for a symbol.

Example usage:
```java
try {
    // Print out the last quote of AAPL
    System.out.println(alpacaAPI.getLastQuote("AAPL"));
} catch (AlpacaAPIRequestException e) {
    e.printStackTrace();
}
```

### Alpaca Streaming
Alpaca offers WebSocket streaming for account and order updates.

Example usage:
```java
try {
    // List to account updates and trade updates from Alpaca and print their outputs
    alpacaAPI.addAlpacaStreamListener(new AlpacaStreamListenerAdapter(
            AlpacaStreamMessageType.ACCOUNT_UPDATES,
            AlpacaStreamMessageType.TRADE_UPDATES) {
        @Override
        public void onStreamUpdate(AlpacaStreamMessageType streamMessageType,
                                   AlpacaStreamMessage streamMessage) {
            switch (streamMessageType) {
                case ACCOUNT_UPDATES:
                    System.out.println((AccountUpdateMessage) streamMessage);
                    break;
                case TRADE_UPDATES:
                    System.out.println((TradeUpdateMessage) streamMessage);
                    break;
            }
        }
    });
} catch (WebsocketException exception) {
    exception.printStackTrace();
}
```

### Alpaca Market Data Streaming
Alpaca's Data API provides websocket streaming for trades, quotes and minute bars. This helps receive the most up to date market information that could help your trading strategy to act upon certain market movement.

Example usage:
```java
try {
    // Print TSLA quotes, trades, and minute aggregates
    alpacaAPI.addMarketDataStreamListener(new MarketDataStreamListenerAdapter(
            "TSLA",
            MarketDataStreamMessageType.QUOTES,
            MarketDataStreamMessageType.TRADES,
            MarketDataStreamMessageType.AGGREGATE_MINUTE) {
        @Override
        public void onStreamUpdate(MarketDataStreamMessageType streamMessageType,
                                   MarketDataStreamMessage streamMessage) {
            switch (streamMessageType) {
                case QUOTES:
                    System.out.println((QuoteMessage) streamMessage);
                    break;
                case TRADES:
                    System.out.println((TradeMessage) streamMessage);
                    break;
                case AGGREGATE_MINUTE:
                    System.out.println((AggregateMinuteMessage) streamMessage);
                    break;
            }
        }
    });
} catch (WebsocketException exception) {
    exception.printStackTrace();
}
```

## PolygonAPI
The `PolygonAPI` class contains several methods to interface with Polygon. You will generally only need one instance of it in your application. The Polygon API specification is located [here](https://polygon.io/docs/) and the `PolygonAPI` javadoc is located [here](https://javadoc.io/doc/net.jacobpeterson/alpaca-java/latest/net/jacobpeterson/polygon/PolygonAPI.html).

Example usage:
```java
// This constructor uses the 'polygon.properties' file on the classpath for configuration
Polygon polygonAPI = new PolygonAPI();

// This constructor passes in a 'keyID' String
String keyID = "<some key ID>";
PolygonAPI polygonAPI = new PolygonAPI(keyID);
```

### PolygonAPIRequestException
The `PolygonAPIRequestException` is thrown anytime an exception occurs when using various `PolygonAPI` methods. It should be caught and handled within your trading algorithm application accordingly.

### Usage
There are many Polygon endpoints (too many to show here in the README), but only certain ones are available to Alpaca users. The usage of the `PolygonAPI` class is very similar to the `AlpacaAPI` class so refer to the `PolygonAPI` [Javadoc](https://javadoc.io/doc/net.jacobpeterson/alpaca-java/latest/net/jacobpeterson/polygon/PolygonAPI.html) for all the available methods and endpoints accessible by Alpaca users.

### Basic Example
Here is a very basic example for requesting aggregate data and streaming real-time market data from Polygon:
```java
try {
    // Get adjusted hour aggregates/bars on 12/23/2020 of AAPL and print them out
    AggregatesResponse aaplAggregates = polygonAPI.getAggregates(
            "AAPL",
            1,
            Timespan.HOUR,
            LocalDate.of(2020, 12, 23),
            LocalDate.of(2020, 12, 24),
            false);
    aaplAggregates.getResults().forEach(System.out::println);
} catch (PolygonAPIRequestException exception) {
    exception.printStackTrace();
}

try {
    // Add a Polygon stream listener to listen to "T.AAPL", "Q.AAPL",
    // "A.AAPL", "AM.AAPL", and status messages
    polygonAPI.addPolygonStreamListener(new PolygonStreamListenerAdapter("AAPL",
            PolygonStreamMessageType.values()) {
        @Override
        public void onStreamUpdate(PolygonStreamMessageType streamMessageType, PolygonStreamMessage streamMessage) {
            System.out.println("===> streamUpdate " + streamMessageType + " " + streamMessage);
        }
    });
} catch (WebsocketException e) {
    e.printStackTrace();
}
```
Again, refer to the `PolygonAPI` [Javadoc](https://javadoc.io/doc/net.jacobpeterson/alpaca-java/latest/net/jacobpeterson/polygon/PolygonAPI.html) for an exhaustive list of all available methods.

## Building
To build this project yourself, clone this repository and run:
```
./gradlew build
```

To install built artifacts to your local maven repo, run:
```
./gradlew build install
```

Contributions are welcome!
