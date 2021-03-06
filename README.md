# ColourStream Cron Bundle

This bundle provides a simple interface for registering repeated scheduled
tasks within your application, including support for installs where the host
does not allow for command-line access (TODO).

This bundle is tested only against Symfony 2.1. It will likely work with Symfony 2.0, but YMMV

4. Update your DB schema
```shell
app/console doctrine:schema:update
```

4. Start using the bundle:
```shell
app/console cron:scan
app/console cron:run
```

## Running your cron jobs automatically

This bundle is designed around the idea that your tasks will be run with a minimum interval - the tasks will be run no more frequently than you schedule them, but they can only run when you trigger then (by running `app/console cron:run`, or the forthcoming web endpoint, for use with webcron services).

To facilitate this, you can create a cron job on your system like this:
```
*/5 * * * * /path/to/symfony/install/app/console cron:run
```
This will schedule your tasks to run at most every 5 minutes - for instance, tasks which are scheduled to run every 3 minutes will only run every 5 minutes.

## Creating your own tasks

Creating your own tasks with CronBundle couldn't be easier - all you have to do is create a normal Symfony2 Command (or ContainerAwareCommand) and tag it with the @CronJob annotation, as demonstrated below:
```php
/**
 * @CronJob("PT1H")
 */
class DemoCommand extends Command
{
    public function configure()
    {
		// Must have a name configured
		// ...
    }
    
    public function execute(InputInterface $input, OutputInterface $output)
    {
		// Your code here
    }
}
```

The interval spec ("PT1H" in the above example) is documented on the [DateInterval](http://au.php.net/manual/en/dateinterval.construct.php) documentation page, and can be modified whenever you choose.
For your CronJob to be scanned and included in future runs, you must first run `app/console cron:scan` - it will be scheduled to run the next time you run `app/console cron:run`
