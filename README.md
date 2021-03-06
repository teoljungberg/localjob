# Localjob

Localjob is a simple, self-contained background queue built on top of [POSIX
message queues][pmq]. Workers and the app pushing to the queue must reside on
the same machine. It's the sqlite of background queues (although sqlite scales
further than Localjob). Here's a post about [how it works][blog].

Localjob is for early-development situations where you don't need a
full-featured background queue, but just want to get started with something
simple that does not rely on any external services. A bigger goal with the
project is to be able to migrate to another background queue system by switching
adapter: `Localjob.adapter = Resque` to switch to Resque, without changes to
your own code.

The POSIX message queue is persistent till reboot. You will need to tune system
parameters for your application, please consult [posix-mqueue][pmq-gem]'s
documentation.

Localjob works on Ruby >= 2.0.0 and Linux.

WIP not everything works as advertised, but try it out and report anything that
doesn't work! It's not released as a gem yet, so add the following to your
Gemfile to use it:

```ruby
gem 'localjob', git: "git@github.com:Sirupsen/localjob.git"
```

## Usage

Localjobs have the following format:

```ruby
class EmailJob
  def initialize(user_id, email)
    @user, @email = User.find(user_id), email
  end

  def perform
    @email.deliver_to(@user)
  end
end
```

To queue a job, create an instance of it and push it to the queue:

```ruby
queue = Localjob.new
queue << EmailJob.new(current_user.id, welcome_email)
```

Then spawn a worker with `bin/localjob`.

[pmq]: http://linux.die.net/man/7/mq_overview
[pmq-gem]: https://github.com/Sirupsen/posix-mqueue
[blog]: http://sirupsen.com/unix-background-queue/
