# Sub

Sub is a Prow Cloud Pub/Sub adapter for handling CI Pub/Sub notification requests.
It currently supports Periodic Prow Job. Note that the prow job need to be defined in the configuration.

## Deployment Usage

Sub supports 2 running modes, Push and Pull. Note that each subscription need to be one or the other, but Sub will run both.

When deploy the sub component, you need to specify `--config-path` to your prow config, and optionally
`--job-config-path` to your prowjob config if you have split them up.


Options:
* `--push-secret-file`: Path to Pub/Sub Push secret file.
* `--dry-run`: Dry run for testing. Uses API tokens but does not mutate.
* `--grace-period`: On shutdown, try to handle remaining events for the specified duration.

### Push Server

In order to use the Push Mode, an HTTPs server needs to be setup and the URL must be defined in the Cloud Pub/Sub subscription.
More information at https://cloud.google.com/pubsub/docs/quickstart-console.

To secure even more requests from Cloud Pub/Syb, you can use `--push-secret-file` option.
When using a push secret add the token to the URL like so  https://myapp.mydomain.com/myhandler?token=application-secret.
More info at https://cloud.google.com/pubsub/docs/faq#security.

### Pull Server

All pull subscriptions need to be defined in Prow Configuration:

```
pubsub_subscriptions:
  "gcp-project-01":
  - "subscription-01"
  - "subscription-02"
  - "subscription-03"
  "gcp-project-02":
  - "subscription-01"
  - "subscription-02"
  - "subscription-03"
```

Sub must be running with `GOOGLE_APPLICATION_CREDENTIALS` environment variable pointing to the service
account credentials JSON file. The service account used must have the right permission on the
subscriptions (`Pub/Sub Subscriber`, and `Pub/Sub Editor`).

More information at https://cloud.google.com/pubsub/docs/access-control.

