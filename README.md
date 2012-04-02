# HashMonitor: Turn Logs Into Metrics Like A Boss

HashMonitor is a simple way to turn your logs into metrics by using
**hashtags** in your log messages.

## How does it work?

By default, HashMonitor **reads text lines from stdin**, and every 30 seconds
**writes JSON statistics to stdout**. You can pipe this JSON output into your
favorite stats dashboard (tinyfeedback, statsd, etc) for easy visualization.

### Example: counting warning events

For example, you could keep track of warnings in your application by simply
logging with a `#warn` hashtag:

    log("something weird happened #warn")
    log("something odd happened #warn")
    log("something strange happened #warn")

HashMonitor will read your log file line-by-line, and count the number of
`#warn` events:

    { warn: { count: 3 } }

### Example: watching performance metrics

You can also track **value-based** metrics by assigning a numeric value
to your hashtags:

    log("speed was pretty fast #loaded_in_seconds=3")
    log("speed was pretty fast #loaded_in_seconds=2")
    log("something was slowwww #loaded_in_seconds=17")

HashMonitor will read your log file line-by-line, and count the number of
`#loaded_in_seconds` events as well as other statistics:

    { loaded_in_seconds: 
       { count: 3,
         mean: 7.333333333333333,
         stddev: 6.847546194724712,
         x01: 17,
         x10: 17,
         median: 2,
         x90: 3,
         x99: 3,
         min: 17,
         max: 3 } }

## How can I try it out?

Easiest is installing hashmonitor globally:

    $ sudo npm install -g hashmonitor

...you may also need to install coffee-script if you don't have it:

    $ sudo npm install -g coffee-script

Then just run the HashMonitor using the default stdin/stdout:

    $ hashmonitor

Once you are here, just type some fake log messages in:

    hello #world
    goodnight #moon
    over the #moon

After 30 seconds you will see JSON stats output, like:

    { world: { count: 1 }, moon: { count: 2 } }

The counts are reset with each stats output.  You can also try out value-based
metrics:

    slow stuff #loadtime=25
    fast stuff #loadtime=10

Instead of just seeing counts now, you will also see additional stats about
the distribution of these values:

    { loadtime: 
       { count: 2,
         mean: 17.5,
         stddev: 7.5,
         x01: 10,
         x10: 10,
         median: 25,
         x90: 25,
         x99: 25,
         min: 10,
         max: 25 } }

There ya go Boss!