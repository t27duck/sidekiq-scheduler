# sidekiq-scheduler

<p align="center">
  <a href="http://moove-it.github.io/sidekiq-scheduler/">
    <img src="https://moove-it.github.io/sidekiq-scheduler/images/small-logo.svg" width="468px" height="200px" alt="Sidekiq Scheduler" />
  </a>
</p>

<p align="center">
  <a href="https://badge.fury.io/rb/sidekiq-scheduler">
    <img src="https://badge.fury.io/rb/sidekiq-scheduler.svg" alt="Gem Version">
  </a>
  <a href="https://codeclimate.com/github/moove-it/sidekiq-scheduler">
    <img src="https://codeclimate.com/github/moove-it/sidekiq-scheduler/badges/gpa.svg" alt="Code Climate">
  </a>
  <a href="https://travis-ci.org/moove-it/sidekiq-scheduler">
    <img src="https://api.travis-ci.org/moove-it/sidekiq-scheduler.svg?branch=master" alt="Build Status">
  </a>
  <a href="https://coveralls.io/github/moove-it/sidekiq-scheduler?branch=master">
    <img src="https://coveralls.io/repos/moove-it/sidekiq-scheduler/badge.svg?branch=master&service=github" alt="Coverage Status">
  </a>
  <a href="https://inch-ci.org/github/moove-it/sidekiq-scheduler">
    <img src="https://inch-ci.org/github/moove-it/sidekiq-scheduler.svg?branch=master" alt="Documentation Coverage">
  </a>
  <a href="http://www.rubydoc.info/github/moove-it/sidekiq-scheduler">
    <img src="https://img.shields.io/badge/yard-docs-blue.svg" alt="Documentation">
  </a>
</p>

`sidekiq-scheduler` is an extension to [Sidekiq](http://github.com/mperham/sidekiq) that
pushes jobs in a scheduled way, mimicking cron utility.

__Note:__ If you are looking for version 2.2.*, go to [2.2-stable branch](https://github.com/moove-it/sidekiq-scheduler/tree/2.2-stable).

## What this fork is

This fork is collection of commits from other community forks with the goal of having this gem working on the most recent sidekiq and redis gems as well as Ruby 3.0.

This fork includes these commits from these forks:

#### nulty/sidekiq-scheduler
- 11adf20b73cce65c551e3c36f1c492715a602c9d - Example cron formats have incorrect number of values

#### taylorkearns/sidekiq-scheduler
- 57036928dd650ad1e8717b8faeea6a4ff10250d7 - Update README to reflect reading Rails timezone

#### amatsuda/sidekiq-scheduler
- c67c97c9419b12cc92998dadcababf9d2eb10437 - Avoid circular require: scheduler.rb <=> manager.rb

#### olleolleolle/sidekiq-scheduler
- bdbebfe1970a8253b8b9c12079923daed8795a21 - Refer to https for rubygems.org

#### rmm5t/sidekiq-scheduler
- d442b794e67675049453e572df31ea24f1f49fc4 - Upgrade redis dependency
- 358e81a9f9c34198f8d2ff76abbddae19bac98a8 - Changed redis "exists" method calls to "exists?"
- 41bb577cb8e4b49febeb96f004362a86a670330f - Upgrade mock_redis
- 15445357137fe3ca7a1f15b7b8a71b56983ca314 - Fix pending specs
- 348ac7cd2ed11dec26ed4d4a28d53aa9f9a60517 - Use strings as keys instead of symbols
- 43558e5446ee6b5bed6bd9d8737c98fe02da0a92 - Drop support for ruby 2.2 and 2.3

#### c-sonnier/sidekiq-scheduler
- 4fb65270c70468f0a813d1d4230f28a4626136d9 - fix: sort recurring jobs by name

#### stefanahman/sidekiq-scheduler
- be9459cf057f9b75e4f6672685e998773fb890f6 - Add support for Ruby 3.0

#### rvshare/sidekiq-scheduler
- ea1d0f82804f8c5b6c947ee78b51ee05c65cb21d - Figure out exactly when the job is supposed to run
- c75674926ab93cdd1d723197077dada53a0b39e8 - Can't use return because of how Rufus handles the block
- afa4daed22ef01eec7acae927fabbdd3281cb1da - Use #utc to get an instance of Time
- 1ff1679eaafab321ab0a4af90ed261fed4ae2dd4 - Round `time` to remove sub seconds
- 3367f7a67a66eaec15cc0947a1f7a4aa962c6cef - Register a job in a redis transaction
- a94d5b7cc2212af0df31c8a0aac22154d80ff17f - Remove dead code
- 546dd996249851be4b3bce1403ec9533c045eaef - Use list group instead of table for improve web UI
- f127e9ab98959e3bb9f9d4a467d28c3ffe88078d - Fix misspelling `multi`
- 30e658fed5ccab06275688645a040a281c53f59f - Fixed CSS for dark mode

## Installation

``` shell
gem install sidekiq-scheduler
```

## Usage

### Hello World


``` ruby
# hello-scheduler.rb

require 'sidekiq-scheduler'

class HelloWorld
  include Sidekiq::Worker

  def perform
    puts 'Hello world'
  end
end
```

``` yaml
# config/sidekiq.yml

:schedule:
  hello_world:
    cron: '0 * * * *'   # Runs once per minute
    class: HelloWorld
```

Run sidekiq:

``` sh
sidekiq -r ./hello-scheduler.rb
```

You'll see the following output:

```
2016-12-10T11:53:08.561Z 6452 TID-ovouhwvm4 INFO: Loading Schedule
2016-12-10T11:53:08.561Z 6452 TID-ovouhwvm4 INFO: Scheduling HelloWorld {"cron"=>"0 * * * *", "class"=>"HelloWorld"}
2016-12-10T11:53:08.562Z 6452 TID-ovouhwvm4 INFO: Schedules Loaded

2016-12-10T11:54:00.212Z 6452 TID-ovoulivew HelloWorld JID-b35f36a562733fcc5e58444d INFO: start
Hello world
2016-12-10T11:54:00.213Z 6452 TID-ovoulivew HelloWorld JID-b35f36a562733fcc5e58444d INFO: done: 0.001 sec

2016-12-10T11:55:00.287Z 6452 TID-ovoulist0 HelloWorld JID-b7e2b244c258f3cd153c2494 INFO: start
Hello world
2016-12-10T11:55:00.287Z 6452 TID-ovoulist0 HelloWorld JID-b7e2b244c258f3cd153c2494 INFO: done: 0.001 sec
```

## Configuration options

Configuration options are placed inside `sidekiq.yml` config file.

Available options are:

``` yaml
:dynamic: <if true the schedule can be modified in runtime [false by default]>
:dynamic_every: <if dynamic is true, the schedule is reloaded every interval [5s by default]>
:enabled: <enables scheduler if true [true by default]>
:scheduler:
  :listened_queues_only: <push jobs whose queue is being listened by sidekiq [false by default]>
```

## Schedule configuration

The schedule is configured through the `:schedule` config entry in the sidekiq config file:

``` yaml
:schedule:
  CancelAbandonedOrders:
    cron: '0 */5 * * *'   # Runs when second = 0, every 5 minutes

  queue_documents_for_indexing:
    cron: '0 0 * * *'   # Runs every hour

    # By default the job name will be taken as worker class name.
    # If you want to have a different job name and class name, provide the 'class' option
    class: QueueDocuments

    queue: slow
    args: ['*.pdf']
    description: "This job queues pdf content for indexing in solr"

    # Enable the `metadata` argument which will pass a Hash containing the schedule metadata
    # as the last argument of the `perform` method. `false` by default.
    include_metadata: true

    # Enable / disable a job. All jobs are enabled by default.
    enabled: true
```

### Schedule metadata
You can configure Sidekiq-scheduler to pass an argument with metadata about the scheduling process
to the worker's `perform` method.

In the configuration file add the following on each worker class entry:

```yaml

  SampleWorker:
    include_metadata: true
```

On your `perform` method, expect an additional argument:

```ruby
  def perform(args, ..., metadata)
    # Do something with the metadata
  end
```

The `metadata` hash contains the following keys:

```ruby
  metadata.keys =>
    [
      :scheduled_at # The epoch when the job was scheduled to run
    ]
```

## Schedule types

Supported types are `cron`, `every`, `interval`, `at`, `in`.

Cron, every, and interval types push jobs into sidekiq in a recurrent manner.

`cron` follows the same pattern as cron utility, with seconds resolution.

``` yaml
:schedule:
  HelloWorld:
    cron: '0 * * * *' # Runs when second = 0
```

`every` triggers following a given frequency:

``` yaml
    every: '45m'    # Runs every 45 minutes
```

`interval` is similar to `every`, the difference between them is that `interval` type schedules the
next execution after the interval has elapsed counting from its last job enqueue.

Note that `every` and `interval` count from when the Sidekiq process (re)starts. So `every: '48h'` will never run if the Sidekiq process is restarted daily, for example. You can do `every: ['48h', first_in: '0s']` to make the job run immediately after a restart, and then have the worker check when it was last run.

At, and in types push jobs only once. `at` schedules in a point in time:
``` yaml
    at: '3001/01/01'
```

You can specify any string that `DateTime.parse` and `Chronic` understand. To enable Chronic
strings, you must add it as a dependency.

`in` triggers after a time duration has elapsed:

``` yaml
    in: 1h # pushes a sidekiq job in 1 hour, after start-up
```

You can provide options to `every` or `cron` via an Array:

``` yaml
    every: ['30s', first_in: '120s']
```

See https://github.com/jmettraux/rufus-scheduler for more information.

## Load the schedule from a different file

You can place the schedule configuration in a separate file from `config/sidekiq.yml`

``` yaml
# sidekiq_scheduler.yml

clear_leaderboards_contributors:
  cron: '0 30 6 * * 1'
  class: ClearLeaderboards
  queue: low
  args: contributors
  description: 'This job resets the weekly leaderboard for contributions'
```

Please notice that the `schedule` root key is not present in the separate file.

To load the schedule:

``` ruby
require 'sidekiq'
require 'sidekiq-scheduler'

Sidekiq.configure_server do |config|
  config.on(:startup) do
    Sidekiq.schedule = YAML.load_file(File.expand_path('../../sidekiq_scheduler.yml', __FILE__))
    SidekiqScheduler::Scheduler.instance.reload_schedule!
  end
end
```

The above code can be placed in an initializer (in `config/initializers`) that runs every time the app starts up.

## Dynamic schedule

The schedule can be modified after startup. To add / update a schedule, you have to:

``` ruby
Sidekiq.set_schedule('heartbeat', { 'every' => ['1m'], 'class' => 'HeartbeatWorker' })
```

If the schedule did not exist it will be created, if it existed it will be updated.

When `:dynamic` flag is set to `true`, schedule changes are loaded every 5 seconds. Use the `:dynamic_every` flag for a different interval.

``` yaml
# config/sidekiq.yml
:dynamic: true
```

If `:dynamic` flag is set to `false`, you'll have to reload the schedule manually in sidekiq
side:

``` ruby
SidekiqScheduler::Scheduler.instance.reload_schedule!
```

Invoke `Sidekiq.get_schedule` to obtain the current schedule:

``` ruby
Sidekiq.get_schedule
#  => { 'every' => '1m', 'class' => 'HardWorker' }
```

## Time zones

Note that if you use the cron syntax and are not running a Rails app, this will be interpreted in the server time zone.

In a Rails app, [rufus-scheduler](https://github.com/jmettraux/rufus-scheduler) (>= 3.3.3) will use the `config.time_zone` specified in Rails.

You can explicitly specify the time zone that rufus-scheduler will use:

``` yaml
    cron: '0 30 6 * * 1 Europe/Stockholm'
```

Also note that `config.time_zone` in Rails allows for a shorthand (e.g. "Stockholm")
that rufus-scheduler does not accept. If you write code to set the scheduler time zone
from the `config.time_zone` value, make sure it's the right format, e.g. with:

``` ruby
ActiveSupport::TimeZone.find_tzinfo(Rails.configuration.time_zone).name
```

## Notes about connection pooling

If you're configuring your own Redis connection pool, you need to make sure the size is adequate to be inclusive of both Sidekiq's own connection pool and Rufus Scheduler's.

That's a minimum of `concurrency` + 5 (per the [Sidekiq wiki](https://github.com/mperham/sidekiq/wiki/Using-Redis#complete-control)) + `Rufus::Scheduler::MAX_WORK_THREADS` (28 as of this writing; per the [Rufus README](https://github.com/jmettraux/rufus-scheduler#max_work_threads)), for a total of 58 with the default `concurrency` of 25.

You can also override the thread pool size in Rufus Scheduler by setting e.g.:

```
SidekiqScheduler::Scheduler.instance.rufus_scheduler_options = { max_work_threads: 5 }
```

## Notes about running on Multiple Hosts

Under normal conditions, `cron` and `at` jobs are pushed once regardless of the number of `sidekiq-scheduler` running instances,
assumming that time deltas between hosts is less than 24 hours.

Non-normal conditions that could push a specific job multiple times are:
 - high cpu load + a high number of jobs scheduled at the same time, like 100 jobs
 - network / redis latency + 28 (see `MAX_WORK_THREADS` https://github.com/jmettraux/rufus-scheduler/blob/master/lib/rufus/scheduler.rb#L41) or more jobs scheduled within the same network latency window

`every`, `interval` and `in` jobs will be pushed once per host.

## Sidekiq Web Integration

sidekiq-scheduler provides an extension to the Sidekiq web interface that adds a `Recurring Jobs` page.

``` ruby
# config.ru

require 'sidekiq/web'
require 'sidekiq-scheduler/web'

run Sidekiq::Web
```

![Sidekiq Web Integration](https://github.com/moove-it/sidekiq-scheduler/raw/master/images/recurring-jobs-ui-tab.png)

## ActiveJob integration

When using sidekiq-scheduler with ActiveJob your jobs can just extend `ApplicationJob` as usual, without the `require` and `include` boilerplate. Under the hood Rails will load up the scheduler and include the worker module for you.

```rb
class HelloWorld < ApplicationJob
  def perform
    puts 'Hello world'
  end
end
```

## The Spring preloader and Testing your initializer via Rails console

If you're pulling in your schedule from a YML file via an initializer as shown, be aware that the Spring application preloader included with Rails will interefere with testing via the Rails console.

**Spring will not reload initializers** unless the initializer is changed.  Therefore, if you're making a change to your YML schedule file and reloading Rails console to see the change, Spring will make it seem like your modified schedule is not being reloaded.

To see your updated schedule, be sure to reload Spring by stopping it prior to booting the Rails console.

Run `spring stop` to stop Spring.

For more information, see [this issue](https://github.com/Moove-it/sidekiq-scheduler/issues/35#issuecomment-48067183) and [Spring's README](https://github.com/rails/spring/blob/master/README.md).


## Manage tasks from Unicorn/Rails server

If you want start sidekiq-scheduler only from Unicorn/Rails, but not from sidekiq you can have
something like this in an initializer:

``` ruby
# config/initializers/sidekiq_scheduler.rb
require 'sidekiq'
require 'sidekiq-scheduler'

puts "Sidekiq.server? is #{Sidekiq.server?.inspect}"
puts "defined?(Rails::Server) is #{defined?(Rails::Server).inspect}"
puts "defined?(Unicorn) is #{defined?(Unicorn).inspect}"

if Rails.env == 'production' && (defined?(Rails::Server) || defined?(Unicorn))
  Sidekiq.configure_server do |config|

    config.on(:startup) do
      Sidekiq.schedule = YAML.load_file(File.expand_path('../../scheduler.yml', __FILE__))
      SidekiqScheduler::Scheduler.instance.reload_schedule!
    end
  end
else
  SidekiqScheduler::Scheduler.instance.enabled = false
  puts "SidekiqScheduler::Scheduler.instance.enabled is #{SidekiqScheduler::Scheduler.instance.enabled.inspect}"
end
```

## License

MIT License

## Copyright

Copyright 2013 - 2018 Moove-IT.
Copyright 2012 Morton Jonuschat.
Some parts copyright 2010 Ben VandenBos.
