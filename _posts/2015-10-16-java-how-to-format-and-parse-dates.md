---
layout: post
title: "Java - How to format and parse dates"
---

Hi guys here comes a very quick and usefull guide of how to parse and format Dates in Java, I hope it helps.

## Using the java.text.DateFormat and java.text.SimpleDateFormat

### Formatting - from date to string

The simplest way to format a Date in java is to use the ``java.text.DateFormat``, it is an abstract class so we have to use an implementation of it. You can call DateFormat.getInstance methods to get a concrete class and start working.

```java
DateFormat df = DateFormat.getInstance();
System.out.println(df.format(new Date()));

// Output: 10/16/15 3:15 PM
```

```java
DateFormat df = DateFormat.getTimeInstance();
System.out.println(df.format(new Date()));
// Output: 3:15:03 PM
```

```java
DateFormat df = DateFormat.getDateInstance();
System.out.println(df.format(new Date()));

// Output: Oct 16, 2015
```

```java
DateFormat df = DateFormat.getDateTimeInstance();
System.out.println(df.format(new Date()));

// Output: Oct 16, 2015 3:15:03 PM
```

It works but it is not so usefull, most part of time we want to format a Date using some custom pattern like day month year. We can use ``java.text.SimpleDateFormat`` to customize our date pattern:

```java
DateFormat df = new SimpleDateFormat("dd/MM/yyyy HH:mm:ss.SSS");
System.out.println(df.format(date));

// Output: 16/10/2015 15:03:17.396
```

We can also show week days and month names just changing the pattern:

```java
DateFormat df = new SimpleDateFormat("EEEE dd MMMM yyyy");
System.out.println(df.format(date));
System.out.println(Locale.getDefault());
// Output: Friday 16 October 2015
// Output: en_US
```

And finally we can change the default locale in order to change the month and week day name, ``java.util.Locale`` represents the Locale which will be considered. 

```java
Locale.setDefault(Locale.FRENCH);
```

Now we can show in French

```java
DateFormat df = new SimpleDateFormat("EEEE dd MMMM yyyy");
System.out.println(df.format(date));
System.out.println(Locale.getDefault());

// Output: vendredi 16 octobre 2015
// Output: fr
```

### Parsing - from string to date

To parse a Date to String we use the same Class DateFormat but now calling the ``parse`` method, if the String does not match with the pattern a ``java.text.ParseException`` will be thrown.

```java
DateFormat df = new SimpleDateFormat("dd/MM/yyyy HH:mm:ss.SSS");
Date date = df.parse("16/10/2015 15:25:07.861");
```

```java
DateFormat df = new SimpleDateFormat("EEEE dd MMMM yyyy");
Date date = df.parse("Friday 16 October 2015");
```

Same thing using Locales

```java
Locale.setDefault(Locale.FRENCH);

DateFormat df = new SimpleDateFormat("EEEE dd MMMM yyyy");
Date date = df.parse("jeudi 15 octobre 2015");
```

## With Java 8 - java.time.format.DateTimeFormatter

Java 8 brings a new API to deal with Date and Time, ``java.time.*`` fixes some problems related with TimeZones and adds some facilities while using date and time manipulations (adding or substracting days hours minutes).

We can create a ``java.time.format.DateTimeFormatter`` using the ``ofPattern`` passing the same pattern used by ``SimpleDateFormat`` or using any constant of ``DateTimeFormatter`` examples (BASIC_ISO_DATE, ISO_LOCAL_DATE, ISO_ZONED_DATE_TIME). See [java/time/format/DateTimeFormatter.html](https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html) for more informations.

### Formatting - from date to string

To format a date using this API is very simple, create a ``java.time.format.DateTimeFormatter`` and pass to ``format`` method of a ``java.time.LocalDateTime``, ``java.time.ZonedDateTime``, ``java.time.LocalDate`` or ``java.time.LocalTime``

```java
LocalDateTime now = LocalDateTime.now();
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm:ss.SSS");
System.out.println(now.format(formatter));

// Output: 16/10/2015 16:34:23.532
```

Beware when working with ``java.time.LocalDate`` if you try to format using a pattern that requires any time field it will throw an exception

```java
LocalDate now = LocalDate.now();
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm:ss.SSS");
System.out.println(now.format(formatter));

// Output: Exception in thread "main" java.time.temporal.UnsupportedTemporalTypeException: Unsupported field: HourOfDay
```

Same using ``java.time.LocalTime``

```java
LocalTime now = LocalTime.now();
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm:ss.SSS");
System.out.println(now.format(formatter));

// Output: Exception in thread "main" java.time.temporal.UnsupportedTemporalTypeException: Unsupported field: DayOfMonth
```

### Parsing - from string to date

To parse we use the same ``java.time.format.DateTimeFormatter`` calling its ``parse`` method. Parse returns a ``java.time.temporal.TemporalAccessor`` so you can create a ``LocalDateTime`` using it.

```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm:ss.SSS");
TemporalAccessor temporalAccessor = formatter.parse("21/10/2015 11:37:09.702");
LocalDateTime localDateTime = LocalDateTime.from(temporalAccessor);
```

## References
  - Java 7 SimpleDateFormat and patterns: [java/text/SimpleDateFormat.html](http://docs.oracle.com/javase/7/docs/api/java/text/SimpleDateFormat.html).
  - Java 7 DateFormat: [java/text/DateFormat.html](http://docs.oracle.com/javase/7/docs/api/java/text/DateFormat.html).
  - Java 7 Locale: [java/util/Locale.html](http://docs.oracle.com/javase/7/docs/api/java/util/Locale.html).
  - Java 8 Time Api [articles/java/jf14-date-time-2125367.html](http://www.oracle.com/technetwork/articles/java/jf14-date-time-2125367.html)
