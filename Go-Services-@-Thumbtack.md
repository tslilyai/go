#Go Services @ Thumbtack
- [ Creating and Deploying](#services)
- [Service Readiness Checklist](#check)
- [Current Go Services](#current)

***

#<a name=services>Creating and Deploying Services</a>
1. Clone the [Go-Skeleton](https://github.com/thumbtack/go-skeleton/) repo. This contains the base files for any Go service (see README for details)

2. Create development, staging, and production environments (See [[ Elastic Beanstalk @ Thumbtack ]])

3. Set up CI (See [[ Travis @ Thumbtack ]])

4. Deploying your Application: See [[Deployment-Guidelines]] for the standard protocol around deployment.  Our Go applications are deployed to Elastic Beanstalk, using the `Generic Docker` platform. We would like to use the pre-configured Go platform, but it does not support installing system package dependencies before running `go build`, which makes it unusable for Hercule (and probably others in the future).
    
    **Important Note:** The deploy script uses `git ls-files` to determine what it should bundle into the deployment artifact. While you don't need to commit your files in order to do a deploy to a development environment, you do need to `git add` them.
  
    - **Deploy to development**: To deploy to a working branch to a testing environment (must be in Oregon), run ```./tteb deploy (development|<user>)```
Anything which you want to run in the development environment must be committed to the head of your development branch. This will create a deployment artifact using `git archive`, and will upload it to the appropriate environment.

    - **Deploy to staging**: Deploys to staging are automatically handled by Travis after successful builds on `master`. If your build succeeds, but your staging deploy fails for some reason, you can just re-run the build in Travis. See https://magnum.travis-ci.com/thumbtack/{APPLICATION} for Travis build details, and [[Travis @ Thumbtack]] for more information.

    - **Deploy to production**: ```./tteb deploy production``` This will deploy the latest successfully built application version to production, which has already been uploaded via the automatic staging deploy. It will not bundle your current directory, in order to make sure only good, tested artifacts are deployed to production.

***
#<a name=check>Service Checklist<a>
### Environment Configuration
* Environment Variables `$ENV`, `$APP_NAME`, `AWS_REGION` must be set properly (will be automatically set using the `create_environment.sh` script in donatello)
  * `$ENV` can take on values "development", "production", or "staging"
  * `$AWS_REGION` must be either development region "us-west-2" or staging/production region "us-east-1"
  * `$APP_NAME` must be the name of your application (i.e. "kiki" or "lakitu")

###Testing
See [[Testing Go Code]] for how you should include tests, and [[Benchmarking and Profiling in Go]] for how you can benchmark/profile your service.

###Logging
Logging is handled by the Papertrail `.ebextension`, and should be included in all projects. Your app should send all of its logs to stdout for this to work properly - **DO NOT** send your logs to a file!

**Keep the logs clean!** You can just write to `STDOUT`, and the EB extension will take care of everything else. In other words, this works:

`log.Printf("Mayday! Error! %s", err.Error())`

###Monitoring
Monitoring is provided by the Datadog `.ebextension` and [Ooyala's dogstatsd client](https://github.com/ooyala/go-dogstatsd/).  See [[Datadog-monitoring]] for an overview of how monitoring works.

- **Adding Metrics**
  - See [the README of the statsd/metrics package](https://github.com/thumbtack/gott/tree/master/statsd/metrics) for instructions on how to use the metrics package to add monitoring to Go services ([the metrics GoDocs] (https://godoc.thumbtack.io/github.com/thumbtack/gott/statsd/metrics))
  - Metrics should include:
    - request volume/rate
    - response time
    - errors or failed requests
    - EC2 specific: (handled in the datadog configuration setup in `.ebextensions`)
      - memory/disk space
      - CPU usage
      - number of EC2 instances
    - service-specific metrics (i.e. emails sent, matches processed, etc.)


- **Metrics Dashboards on DataDog**
  - Pulse: tracking the past hour (e.g. [Kiki Pulse](https://app.datadoghq.com/screen/25299/kiki-pulse))
  - Overview: adjustable over different time ranges (e.g. [Kiki Overview](https://app.datadoghq.com/dash/55317/kiki-overview))

**Note**: It is preferable to send 0s rather than not sending the metric at all when there are no errors. This is because once a metric has stopped reporting, it will disappear from the UI after a few hours, until it is reported again. For consistency, it's better to continually report values, even if they are 0s.


###Alerting
There are alerts available on Elastic Beanstalk for hitting a certain number of instances running behind an ELB, which you should enable. You should also set up some Pingdom checks, as mentioned in the [[Deployment-Guidelines]]

Datadog monitors should alert for alarming metric values (e.g. no EC2 instances, slow response time, etc.)
 
***

#<a name=current>Current Go services<a>
- [kiki](https://github.com/thumbtack/kiki) (emailing)
- [agonas](https://github.com/thumbtack/agonas) (matching)
- [snigen](https://github.com/thumbtack/snigen) (snippets)
- [swan](https://github.com/thumbtack/swan) (recommendations)
- [lakitu](https://github.com/thumbtack/lakitu) (sms-push-notifications)
- [fujimoto](https://github.com/thumbtack/fujimoto) (pro request category suggestions)
- [hercule](https://github.com/thumbtack/hercule) (homepage category recommendation)
- [cryotube](https://github.com/thumbtack/cryotube) (schedule queue tasks)
- [lushan](https://github.com/thumbtack/lushan) (realtime data visualization)
- [prospect](https://github.com/thumbtack/prospect)