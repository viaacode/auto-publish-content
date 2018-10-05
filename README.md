# auto-publish-content

## Trigger

### Automatic trigger

This application will run at 00:00 every 1st day of the month (`0 0 0 1 * ?`).

### Manual trigger

You can trigger this application manually by sending a request to http://hostname:4321/publish. This endpoint allows you to make any call (POST, GET, PUT, HEAD, ...) and does not look at any data that was sent. It is simply to force the application to start. 

```shell
curl -X GET http://hostname:4321/publish
```

Result, if ok, should be:

```json
{
  "result": "started"
}
```

## Process

The application will:

- Get all organisations
- For every organisation
    - Get all files that are older than 6 months
    - For every file
        - Send a publish request to the [mam-publish-service](https://github.com/viaacode/mam-publish-service)
- Publish a message on a queue to be picked up by [auto-publish-mail
](https://github.com/viaacode/auto-publish-mail)
- Send a notification to Slack with the amount of publishes per organisation

## Reporting

At 00:00 on the 3rd day of the month, the application will check the Elasticsearch index that [mam-publish-service](https://github.com/viaacode/mam-publish-service) uses and requests how many jobs were successful and how many failed. It will send a Slack message with these numbers.
